# AIOps Assessment: Payment Service Incident Detection

## 1. Scenario overview
This project models a lightweight AIOps workflow for the `payment-service`, a critical service that processes payment requests. The objective is to identify unusual operational behavior early by combining metric telemetry and log evidence before the service degrades further and impacts customer transactions.

The assessment dataset contains a short but clear incident window. During a brief period, the service experiences abnormal latency, elevated CPU and memory utilization, and outage-like error logs. The AIOps system is expected to detect those observations, emit an anomaly event, forward it through a topic-based event flow, and present a readable operational report.

## 2. Operational data description
The telemetry is stored in `data/service_data.json` and contains ten one-minute observations between `2026-09-20T10:00:00` and `2026-09-20T10:09:00`.

Each record contains:
- `timestamp`
- `service`
- `response_time_ms`
- `cpu_percent`
- `memory_percent`
- `log_level`
- `message`

The key metric fields are:
- `response_time_ms`: end-to-end latency
- `cpu_percent`: CPU utilization percentage
- `memory_percent`: memory utilization percentage

The log fields are:
- `log_level`: severity level such as `INFO` or `ERROR`
- `message`: descriptive event text such as successful processing or timeout failure

## 3. Observations from logs and metrics
### Normal behavior
The normal observations are the records at:
- `10:00`
- `10:01`
- `10:02`
- `10:03`
- `10:04`
- `10:07`
- `10:08`
- `10:09`

These records show stable values:
- response time roughly between 120 ms and 150 ms
- CPU around 42% to 57%
- memory around 51% to 57%
- log level remains `INFO`
- messages indicate successful payment processing

### Abnormal behavior
The unusual observations are the records at:
- `10:05`
- `10:06`

These records show a severe degradation event:
- response time jumps to 610 ms and 640 ms
- CPU rises to 75% and 94%
- memory rises to 70% and 91%
- log level changes to `ERROR`
- messages mention timeout and database connection failures

This combination strongly indicates an operational incident affecting service health and payment processing capacity.

## 4. Anomaly detection findings
The repository’s detector in `src/anomaly_detector.py` classifies a record as an anomaly when it exceeds configured operational thresholds or emits a concerning error-level log.

The final detection output identified two anomaly records:

1. `2026-09-20T10:05:00`
   - `High response time`
   - `Error log detected`

2. `2026-09-20T10:06:00`
   - `High response time`
   - `High CPU utilization`
   - `High memory utilization`
   - `Error log detected`

These findings correctly match the observed incident window and clearly explain why each observation was flagged.

## 5. Event-processing flow
The workflow follows the project’s architecture:

Operational Data -> `AnomalyDetector` -> `Event` -> `EventProducer` -> `EventTopic` -> `EventConsumer` -> downstream AIOps processing

The stages are:
- raw records are processed from `data/service_data.json`
- detection checks metric thresholds and log severity
- matching records are converted into anomaly event objects
- the producer publishes the event onto the in-memory topic
- the consumer consumes the event from the topic
- the resulting event is treated as the downstream processed AIOps signal

## 6. Final workflow execution result
The verified workflow execution produced the following result:

- `Records processed: 10`
- `Anomalies detected: 2`
- `Events consumed: 2`

Example processed anomaly output:
- timestamp: `2026-09-20T10:05:00`
- type: `ANOMALY`
- reasons: `High response time`, `Error log detected`

- timestamp: `2026-09-20T10:06:00`
- type: `ANOMALY`
- reasons: `High response time`, `High CPU utilization`, `High memory utilization`, `Error log detected`

This demonstrates that the operational issue was successfully processed from raw telemetry into a readable downstream event representation.

## 7. Issues identified and corrected
The workflow initially had several problems that prevented it from working as expected:

1. Package/test import issues
   - Cause: Python could not discover the project package during pytest collection.
   - Fix: added `src/__init__.py` and a `pytest.ini` file to make the workspace importable.

2. Log detection bug
   - Cause: the detector was only checking `WARNING` severity and ignored `ERROR` events.
   - Fix: updated the detection logic to treat both `WARNING` and `ERROR` as concerning log conditions.

3. Event flow mismatch
   - Cause: the producer and consumer were bound to different in-memory topics, so published events were not being consumed from the correct stream.
   - Fix: the producer and consumer now use the same `EventTopic` instance.

These corrections preserve the intended event-driven design and keep the workflow within the existing architecture.

## 8. Limitation and possible improvement
A key limitation of the current detection logic is that it relies on static thresholds and evaluates each sample independently. It does not use time-series trends or rolling baselines, so it may miss gradual degradation or overreact to brief, noisy spikes in otherwise healthy systems.

A possible improvement would be to add a baseline model that compares current metrics against recent historical windows and uses an alert threshold based on variance or z-score instead of only fixed absolute values.

## 9. Reproduction steps
To reproduce the demonstration on another machine:

1. Clone the repository.
2. Open the project folder.
3. Ensure Python is available.
4. Run the tests:
   ```bash
   PYTHONPATH=. pytest -q
   ```
5. Execute the end-to-end pipeline:
   ```bash
   PYTHONPATH=. python src/aiops_pipeline.py
   ```
6. Review the output. The script prints:
   - number of records processed
   - number of anomalies detected
   - detected anomaly events
   - reasons for each anomaly

The expected result is that the workflow processes all ten records and reports two anomalies in the `10:05` and `10:06` time window.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

