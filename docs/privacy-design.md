# CIRVA Privacy-First System Design

This document details the core privacy principles, telemetry guardrails, and self-healing local correction mechanisms implemented in CIRVA.

## Privacy Principles

CIRVA is built on a **Zero-Knowledge Architecture** for messaging. The core tenants are:
1. **Local-First Processing**: Sentiment, intent, and message categorization must be computed locally on-device whenever possible.
2. **PII Isolation**: Personally Identifiable Information (PII) never leaves the device hardware bounds.
3. **Data Minimization**: Cloud requests must only transfer scrubbed inputs and must discard them immediately after inference.
4. **Anonymous Telemetry**: Server logs record operational metrics (latency, model hits), never raw message payloads or user identities.

---

## Telemetry Guardrail (Supabase Schema)

To trace performance and optimize the classification thresholds, CIRVA utilizes an anonymous telemetry tracking table inside Supabase. This telemetry records events like model performance, classification times, and client errors without keeping raw user inputs.

```sql
-- Schema for anonymous telemetry
CREATE TABLE user_events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE SET NULL,
  event_name TEXT NOT NULL,
  metadata JSONB DEFAULT '{}'::jsonb,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexing for telemetry analytical queries
CREATE INDEX idx_user_events_name ON user_events(event_name);
CREATE INDEX idx_user_events_created_at ON user_events(created_at DESC);
```

### Event Payload Example

When an intent classification occurs, a client event is sent:

```json
{
  "event_name": "context_classification",
  "metadata": {
    "tier": 1,
    "model": "MiniLM-ONNX-Quantized",
    "latency_ms": 14,
    "confidence": 0.88,
    "predicted_label": "urgent",
    "fallback_triggered": false
  }
}
```

---

## Federated Client-Side Correction Loops

To optimize classification accuracy without sending raw messages back to central databases for retraining, CIRVA implements a local **self-healing correction loop**:

```
[ User overrides AI prediction ] ──► [ Local Correction Logged ]
                                              │
                                              ▼
                                 [ SQLite Local Dictionary Update ]
                                              │
                                              ▼
                                 [ Local Fine-Tuning of Bias Weights ]
```

1. **The Correction Trigger**: If the app labels a message *casual* but the user manually re-labels it to *urgent*, a local correction event is written to SQLite.
2. **Local Bias Weights**: The client updates local model bias offsets saved in the SQLite cache database.
3. **Naïve Bayes/Softmax Adjustments**: When evaluating the final intent probabilities from the ONNX output, local bias offsets are added to the output tensor locally.
4. **Result**: The classification gets smarter over time for the user's specific vocabulary, without exposing a single word of text to the cloud.
