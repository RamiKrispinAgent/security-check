---
title: "Performance Instrumentation Stub"
output: html_document
---

# Performance Instrumentation Stub

This file demonstrates the structure for instrumenting OpenClaw processes to log timing data to `performance_log.jsonl`.

## Structure of `performance_log.jsonl`

Each entry in the log file is a single JSON object, formatted as:

```json
{
  "timestamp": "2026-04-13T22:10:00Z",
  "process": "web_fetch",
  "model": "gemma4 128",
  "duration_ms": 1234,
  "input_size": 456,
  "output_size": 789,
  "status": "success"
}
```

## Example Instrumented Function

Below is a conceptual wrapper for `web_fetch`:

```python
import time
import json
import os
from datetime import datetime

def instrumented_web_fetch(url, extractMode="markdown", maxChars=10000):
    start_time = time.time()
    try:
        # Call the original function
        result = web_fetch(url, extractMode, maxChars)
        duration_ms = int((time.time() - start_time) * 1000)
        
        # Log to file
        log_entry = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "process": "web_fetch",
            "model": "gemma4 128",  # This would be dynamic in practice
            "duration_ms": duration_ms,
            "input_size": len(url),
            "output_size": len(result),
            "status": "success"
        }
        
        log_path = "performance_log.jsonl"
        with open(log_path, "a") as f:
            f.write(json.dumps(log_entry) + "\n")
        
        return result
    except Exception as e:
        duration_ms = int((time.time() - start_time) * 1000)
        log_entry = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "process": "web_fetch",
            "model": "gemma4 128",
            "duration_ms": duration_ms,
            "status": "error",
            "error": str(e)
        }
        with open(log_path, "a") as f:
            f.write(json.dumps(log_entry) + "\n")
        raise e
```

This approach can be applied to all OpenClaw tool calls to build a comprehensive performance profile.
