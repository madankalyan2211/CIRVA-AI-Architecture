# CIRVA Live Database & Telemetry Report

This metrics report details the live database stats, row counts, and telemetry usage indicators extracted directly from the CIRVA Supabase database.

**Report Generated On:** Sunday, June 28, 2026 at 05:43 PM MST

---

## Live Database Row Counts

| Database Relation | Description | Live Record Count | Status |
| :--- | :--- | :--- | :--- |
| `public.users` | Registered user profiles & settings | **19** | Active |
| `public.conversations` | Chat room descriptors (direct/circle) | **6** | Active |
| `public.messages` | Shared text, images, and audio messages | **128** | Active |
| `public.friend_requests` | Pending/accepted peer connection records | **16** | Active |
| `public.feedback` | User bug reports & feature requests | **1** | Active |

---

## AI Semantic & Telemetry Indicators

| Telemetry Dimension | Indicator Value | Explanation |
| :--- | :--- | :--- |
| **Total System Messages** | **128** messages | Total volume of communication traffic. |
| **AI Processed Messages** | **17** inferences | Messages successfully classified by the hybrid emotion + intent engine. |
| **AI Coverage Ratio** | **13.3%** | Percentage of text traffic enriched by ONNX / Groq reasoning layers. |
| **Anonymized Feedback** | **1** submissions | Volume of product refinement insights stored in the database. |

---

## Telemetry Database Footprint

* **Estimated database storage used**: ~30.6 MB (Includes default PostgreSQL schemas, extensions, and tables).
* **Maximum provisioned free-tier limit**: 2.0 GB (Capacity utilization: ~1.5%).
* **Scale-up capability**: Fully compatible with AWS-backed Pro Tier storage autoscaling.
