# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

# The service being monitored

This assessment monitors the payment-service, which handles transaction processing for a critical business workflow. 
# the operational problem  being addressed 
The operational issue being addressed is a degradation in service health: during a short period, the service exhibits severe latency and timeout spikes, accompanied by elevated CPU and memory usage, which can disrupt payment processing and impact customer experience.
# purpose of AiOPs in this assessement 
The purpose of AIOps in this assessment is to use telemetry patterns, such as response times, resource utilization, and error-level logs, to detect abnormal behavior early, identify likely root causes, and support faster operational response before the service fails completely.

# Task 2: Analysis of logs and metrics

1. Metrics fields
The numeric operational metrics in the data are:
- response_time_ms: end-to-end latency for each request or check-in
- cpu_percent: CPU utilization percentage
- memory_percent: memory utilization percentage
These are the fields that quantify service health and resource usage over time.

2. Log information
The log-related fields are:
- log_level: severity level such as INFO or ERROR
- message: descriptive text for the event, for example "Payment request processed successfully" or "Database connection timeout"
The service field identifies which service produced the event, but it is not itself a log metric.

3. Use of timestamps
The timestamps are ISO 8601 timestamps in one-minute intervals, from 2026-09-20T10:00:00 through 2026-09-20T10:09:00. They are used to sequence the service observations and to show that the abnormal period is concentrated in a short window around 10:05 and 10:06. This makes it possible to correlate performance degradation, resource spikes, and error messages over time.

4. Normal behaviour
The normal observations are the records at 10:00, 10:01, 10:02, 10:03, 10:04, 10:07, 10:08, and 10:09. In those records:
- response_time_ms stays roughly between 120 ms and 150 ms
- cpu_percent remains around 42% to 57%
- memory_percent stays around 51% to 57%
- log_level is INFO
- message indicates successful transaction processing
This pattern is consistent with expected stable operation.

5. Unusual behaviour
The unusual observations are the records at 10:05 and 10:06. In those records:
- response_time_ms jumps to 610 ms and 640 ms
- cpu_percent rises to 75% and 94%
- memory_percent rises to 70% and 91%
- log_level changes to ERROR
- message indicates timeout failures and a database connection issue
This is a clear degradation event, suggesting a service health problem or infrastructure bottleneck affecting payment processing capacity.

Overall, the dataset shows a short-lived operational incident where latency and resource utilization spike sharply, followed by a return to normal behavior after the incident window.

