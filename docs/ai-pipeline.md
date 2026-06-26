# CIRVA AI Pipeline & Inference Flow

This document details the inference pipeline, tokenization, model structures, and fallback logic used inside the CIRVA context-aware communication engine.

## Inference Flow

When a user types or receives a message, the following workflow is executed:

```
[ User Input / Incoming Message ]
               │
               ▼
   [ Tokenizer (Naive/WordPiece) ]
               │
               ▼
    [ Local ONNX Session Run ]
               │
               ▼
    [ Extract Intent/Emotion Vector ]
               │
      Is Confidence >= 0.70?
       ├─── YES ───► [ Action Completed Locally ]
       │
       └─── NO  ───► [ Anonymize Input Payload ]
                              │
                              ▼
                     [ Send Groq API Request ]
                              │
                              ▼
                     [ Extract Final Intent ]
                              │
                              ▼
                     [ Log Telemetry Event ]
```

---

## Local ONNX Pipeline (Tier 1)

### Model Specification
* **Base Architecture**: MiniLM-L6-H384-uncased.
* **Format**: ONNX (Open Neural Network Exchange), 8-bit quantized to minimize resource usage on mobile devices.
* **Asset Size**: ~30 MB.

### Tokenizer implementation
Due to platform constraints in React Native, the tokenizer is implemented in pure TypeScript/JavaScript as a naive WordPiece tokenizer utilizing a vocabulary file bundled with the app.

### Lifecycle of local session
1. **Instantiation**: The ONNX session is pre-loaded into memory during app startup.
2. **Tokenization**: Inputs are trimmed, lowercase-folded, mapped to vocabulary indices, and padded/truncated to a sequence length of 128.
3. **Session Run**:
   ```typescript
   import { InferenceSession, Tensor } from 'onnxruntime-react-native';

   const inputIds = new Tensor('int64', new BigInt64Array(tokenIds), [1, 128]);
   const attentionMask = new Tensor('int64', new BigInt64Array(mask), [1, 128]);

   const results = await session.run({ input_ids: inputIds, attention_mask: attentionMask });
   ```
4. **Softmax Output**: Probabilities are computed over the final classification layer to yield intent labels (e.g., `urgent`, `playful`, `casual`, `action-item`).

---

## Cloud Fallback Pipeline (Tier 2)

If the maximum probability in the output vector is lower than `0.70`, the system automatically promotes the request:

1. **PII Scrubbing**: Regex filters strip out telephone numbers, email addresses, and names.
2. **Payload Envelope**:
   ```json
   {
     "model": "llama-3.1-8b-instant",
     "messages": [
       {
         "role": "system",
         "content": "You are a low-latency context-aware assistant. Classify the user input into one of these labels: [urgent, playful, casual, action-item]. Respond with ONLY the label name."
       },
       {
         "role": "user",
         "content": "<scrubbed_input_text>"
       }
     ],
     "temperature": 0.0,
     "max_tokens": 10
   }
   ```
3. **Execution**: Sent via HTTP POST to Groq's edge endpoints using API keys stored securely in the device's keychain.
