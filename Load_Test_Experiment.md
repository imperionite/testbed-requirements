# Preliminary Performance and Derived Scalability Analysis of the Serverless-Based Internship Management System (SBIMS)

**Study:** Serverless-Based Internship Management System for Higher Education Institutions  
**Evaluation tool:** K6 load testing  
**Analysis type:** Descriptive statistical analysis and comparative workload-response trend analysis  
**Status:** Preliminary report for group review and Chapter IV–V preparation

## Purpose and Alignment with the Research

This report presents a preliminary analysis of the SBIMS load-testing experiments conducted using K6. It supports the sixth specific research objective, which aims to empirically characterize system performance under simulated concurrent workloads by examining response time, throughput, error rate, and supporting performance measures, and to infer scalability characteristics from the observed workload-response trends.

It directly addresses Research Question 4:

_How does the developed SBIMS behave under progressively increasing concurrent workloads in terms of response time, throughput, error rate, and supporting application performance measures, and what scalability characteristics can be inferred from the observed workload-response trends?_

The analysis also contributes to the overall research objective of evaluating the developed system. However, it addresses only the performance and derived scalability component. Functional correctness, perceived usability, and requirements validation must be assessed using their respective testing and evaluation evidence.

## Experimental Basis and Analysis Method

The previously shared K6 records contain 12 trials across four workload levels: 10, 25, 50, and 100 virtual users (VUs), with three trials at each level. Each trial was configured for a four-minute duration, with a one-second think time and three authenticated GET requests in the tested workflow. Authentication was performed once in K6's `setup()` function per execution, rather than through a separate concurrent login for every VU.

The experiments therefore characterize the behavior of the configured workflow under the tested concurrency levels. They should not be interpreted as a direct measurement of the number of distinct students, coordinators, or other users that the complete SBIMS can sustainably serve.

The analysis uses the following measures:

- **Average response time:** The mean response time reported for each trial, used to compare typical request latency across workload levels.
- **P95 and P99 response times:** Trial-level 95th- and 99th-percentile latency measures used to examine slower requests and the upper tail of the response-time distribution.
- **Throughput (requests per second, or RPS):** The reported rate of HTTP request processing, used to observe how request throughput changes as concurrency increases.
- **HTTP failure rate:** The proportion of HTTP requests reported as failed.
- **Workflow success rate:** The reported proportion of completed workflows that succeeded.
- **Supporting measures:** Maximum response time, completed workflows, iterations, and available load-generator resource measurements, where appropriate.

For each workload level, the descriptive summary uses the mean and sample standard deviation of the three trial-level measurements. The standard deviation describes variability between trials. It is not a confidence interval and does not establish statistical significance.

## Preliminary Descriptive Results

The following figures were calculated from the previously shared trial records. Response times are in milliseconds. Throughput is in requests per second, and the rates are percentages.

| Virtual users | Average response time, mean ± SD | Mean trial P95 | Mean trial P99 | Throughput, mean ± SD | HTTP failure rate, mean | Workflow success rate, mean |
| ------------: | -------------------------------: | -------------: | -------------: | --------------------: | ----------------------: | --------------------------: |
|            10 |                      2,395 ± 915 |          6,743 |         21,134 |         2.870 ± 1.007 |                  1.876% |                     96.848% |
|            25 |                      2,152 ± 697 |          4,302 |         19,031 |         7.430 ± 1.682 |                  1.448% |                     97.371% |
|            50 |                      3,130 ± 620 |          8,684 |         16,549 |        11.521 ± 1.992 |                  1.506% |                     97.781% |
|           100 |                      6,065 ± 468 |         13,445 |         21,361 |        12.894 ± 0.549 |                  1.942% |                     96.964% |

**Interpretation of the table:** These results suggest that throughput increased as the workload grew, but the increase became smaller at the higher tested load. Average response time was substantially higher at 100 VUs than at 50 VUs. Meanwhile, the reported HTTP failure rate remained around 1.4%–1.9% on average, and the workflow success rate remained around 96.8%–97.8%.

These are descriptive averages, not evidence that all requests or workflows behaved similarly. The tail-latency measures and the variability between trials provide important additional context.

_Note:_ The P95 and P99 figures are averages of the three trial-level percentiles at each workload. They are not percentiles calculated from all requests pooled across the three trials.

## Analysis and Interpretation of Performance

### Response time

At 10 VUs, mean average response time was approximately 2.40 seconds. At 25 VUs, it decreased to approximately 2.15 seconds. At 50 VUs, it increased to approximately 3.13 seconds, and at 100 VUs, it reached approximately 6.07 seconds.

The results do not show a strictly monotonic increase in response time at every workload step. In particular, the 25-VU average was lower than the 10-VU average. This difference may reflect trial variability or differences in execution conditions and should not be treated as evidence that adding concurrent users improves performance.

The more important pattern appears between 50 and 100 VUs, where mean average response time increased by approximately 93.8%. This indicates that the tested workflow experienced greater latency under the highest tested workload.

The P95 figures provide complementary evidence. Mean trial P95 increased from approximately 8.68 seconds at 50 VUs to 13.45 seconds at 100 VUs. Thus, the higher workload was associated with a deterioration in the upper tail of response times, not just in the average.

The variation between trials also matters. At 10 VUs, individual trial P95 values ranged from approximately 1.89 seconds to 13.11 seconds. This substantial spread means that the mean P95 alone does not fully describe the observed behavior.

**Preliminary interpretation:** Response-time behavior suggests increasing performance pressure at higher concurrency, particularly at 100 VUs. The variability between trials means that the results should be interpreted alongside individual trial observations and the execution logs.

### Throughput

Mean reported throughput increased from 2.870 RPS at 10 VUs to 7.430 RPS at 25 VUs, 11.521 RPS at 50 VUs, and 12.894 RPS at 100 VUs.

From 10 to 100 VUs, the reported throughput increased by approximately 349%, while the configured concurrency increased tenfold. This indicates that the system processed requests at a higher overall rate under greater concurrency.

However, throughput did not increase proportionally to the number of VUs. Between 50 and 100 VUs, concurrency doubled, but reported throughput increased by only approximately 11.9%.

This difference is important because higher concurrency does not necessarily produce a corresponding increase in completed work. At higher load, additional concurrent activity may result in longer waiting times, contention, queuing, resource limits, or other sources of overhead. The current results alone cannot determine which of these mechanisms, if any, explains the observed pattern.

The throughput figures must also be interpreted in light of the K6 measurement configuration. Because authentication occurs in `setup()`, the group should verify whether the reported RPS includes the setup login request and how the CSV analyzer aggregates HTTP requests. This distinction is necessary before treating the metric as the sustained throughput of the three-request workflow alone.

**Preliminary interpretation:** Throughput increased throughout the tested range, but the smaller gain between 50 and 100 VUs suggests diminishing throughput returns at the highest tested concurrency.

### HTTP failure rate and workflow success

The reported mean HTTP failure rates ranged from approximately 1.448% to 1.942%. The reported mean workflow success rates ranged from approximately 96.848% to 97.781%.

The workflow success rate was highest at 50 VUs and lower at 100 VUs. The HTTP failure rate was also highest on average at 100 VUs. Together, these reported measures suggest that the system continued to complete most tested workflows, but the highest workload did not provide the strongest observed reliability results.

These averages do not establish that the error rate increased consistently with each workload step. The trial-level variation is substantial, particularly for HTTP failure rate, so the individual results should be reviewed before attributing the observed differences solely to concurrency.

The group must also distinguish request-level failures from workflow-level failures. An HTTP request can fail without every workflow failing, depending on how the workflow and analyzer define success. The two rates should therefore not be treated as interchangeable.

**Important data-quality check:** In the previously shared records, `HTTP_Failed_Count` and `HTTP_Passed_Count` appeared reversed relative to the reported HTTP failure and success rates. For example, some trials reported a high failure count despite a low failure percentage, and one trial reported a zero-percent failure rate alongside a nonzero failed count. These discrepancies must be checked against the raw K6 output and the CSV-generating analyzer before the counts are used as supporting evidence. The percentages in this preliminary report are reproduced from the previously shared records, not independently verified against the raw request-level data.

### Maximum response time and supporting measures

Several trials reported maximum response times close to 30,000 milliseconds, consistent with the configured 30-second HTTP timeout. This pattern raises the possibility that some requests reached or approached the timeout boundary.

However, a maximum latency close to the configured timeout is not sufficient by itself to confirm a timeout. The raw K6 output, error messages, and relevant request-level records should be inspected to determine whether the affected requests actually timed out or whether another condition produced the observed maximum.

Other supporting measures, such as iterations, completed workflows, and load-generator CPU and memory, can help contextualize the experiment. However, CPU and memory recorded on the load generator describe the testing machine, not necessarily the SBIMS server or the managed cloud services supporting it. They should not be used alone to diagnose a server-side bottleneck.

## Derived Scalability Analysis

### Throughput scaling and diminishing returns

A useful way to interpret derived scalability is to compare the change in throughput with the change in workload.

| Workload transition | VU increase | Throughput increase | Preliminary interpretation                                                                       |
| ------------------- | ----------: | ------------------: | ------------------------------------------------------------------------------------------------ |
| 10 → 25 VUs         |        2.5× |  Approximately 159% | Throughput increased substantially as concurrency grew.                                          |
| 25 → 50 VUs         |          2× | Approximately 55.1% | Throughput continued to increase, but less than proportionally to the VU increase.               |
| 50 → 100 VUs        |          2× | Approximately 11.9% | Throughput gains were comparatively small despite doubled concurrency.                           |
| 10 → 100 VUs        |         10× |  Approximately 349% | The system processed more requests, but throughput did not scale proportionally to the workload. |

These comparisons are descriptive. They indicate that the system accommodated greater concurrency by increasing reported throughput, but the observed gain weakened at higher workload levels.

This pattern is consistent with **diminishing throughput returns** in the tested workload range. It may indicate that the system or its supporting environment was approaching a performance constraint under the particular workflow and test conditions. The experiments do not establish the exact constraint or whether it originated in the application runtime, database, network, managed services, or load generator.

### Throughput scaling efficiency

A simple derived measure is throughput per configured VU:

![Equation](https://latex.codecogs.com/svg.latex?E_N%20%3D%20\frac{X_N}{N})

where \(X_N\) is mean reported throughput at workload \(N\), and \(N\) is the number of configured VUs.

| VUs | Mean reported throughput | Throughput per configured VU |
| --: | -----------------------: | ---------------------------: |
|  10 |                2.870 RPS |                 0.287 RPS/VU |
|  25 |                7.430 RPS |                 0.297 RPS/VU |
|  50 |               11.521 RPS |                 0.230 RPS/VU |
| 100 |               12.894 RPS |                 0.129 RPS/VU |

Throughput per configured VU was broadly similar at 10 and 25 VUs, then declined at 50 and 100 VUs. At 100 VUs, the measure was approximately 55% lower than at 25 VUs.

This derived metric supports the observation that additional configured concurrency yielded less throughput per VU at higher loads. It is not a measure of server efficiency, resource utilization, or per-user productivity. Because VUs are virtual workload generators rather than a direct count of real human users, the measure should be used only as a comparative indicator within this test configuration.

### What the experiments support about scalability

The results support three cautious inferences:

1. **The tested system accommodated increasing concurrency.** Reported throughput increased across the four tested workload levels.
2. **The throughput gain weakened at higher concurrency.** Doubling the configured VUs from 50 to 100 produced only about an 11.9% increase in reported throughput.
3. **The highest tested workload was associated with greater latency.** Mean average response time increased by approximately 93.8% between 50 and 100 VUs, and mean trial P95 also increased.

Taken together, these findings suggest that the tested SBIMS workflow exhibited **increasing performance pressure and diminishing throughput returns at higher tested concurrency**.

This is a limited inference about derived scalability, not proof that the system is or is not scalable in every operational context. The experiments do not demonstrate unlimited scaling, establish a maximum sustainable workload, or prove that the deployed serverless services automatically scaled in response to load.

A stronger claim about scalability would require additional evidence, such as validated service-side resource and scaling metrics, more workload levels around the observed change in behavior, repeated runs under controlled conditions, and an explicit definition of acceptable latency and failure rates.

## Implications for Research Objective 6 and Research Question 4

The findings address the key elements of the sixth research objective as follows:

| Required evaluation dimension | Evidence from the experiments                                             | Preliminary finding                                                                                                                      |
| ----------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Response time                 | Mean, P95, P99, and maximum latency                                       | Latency was higher at 100 VUs than at 50 VUs, while variation between trials was considerable.                                           |
| Throughput                    | Mean reported RPS                                                         | Throughput increased at all tested workload levels, with a smaller gain from 50 to 100 VUs.                                              |
| Error rate                    | Reported HTTP failure rate and workflow success rate                      | Most workflows were reported as successful, but failures remained present and the highest workload had the lowest mean workflow success. |
| Supporting measures           | Workflow counts, iterations, maximum latency, and available resource data | Useful for context, subject to data consistency checks and clear distinction between load-generator and server-side measures.            |
| Derived scalability           | Throughput gains and throughput per configured VU                         | Results suggest diminishing throughput returns and increasing performance pressure at higher tested concurrency.                         |
| Deployment constraints        | Selected deployment environment and test configuration                    | Conclusions are limited to the tested environment, workflow, and workload levels.                                                        |

For Research Question 4, the preliminary answer is that the SBIMS workflow processed more requests as concurrency increased, but the gain in throughput diminished at higher workloads while response times increased. The reported failure and workflow-success measures indicate that successful processing remained predominant, although the system did not achieve zero reported failures. Consequently, the results suggest a workload-dependent performance limit within the tested range, but do not identify a definitive capacity ceiling or infrastructure bottleneck.

## Limitations and Checks Before Finalizing Chapter IV

Before the group treats the results as final research evidence, the following items should be resolved or explicitly acknowledged:

1. **Verify HTTP count labels.** Compare `HTTP_Failed_Count` and `HTTP_Passed_Count` against the raw K6 output and the analyzer logic. Do not silently swap the values.
2. **Verify the RPS calculation.** Establish whether setup-phase authentication requests are included and document exactly which requests contribute to reported throughput.
3. **Inspect maximum-latency records.** Confirm whether values near 30 seconds represent actual request timeouts, and report the evidence from the raw output.
4. **Check missing and inconsistent workflow fields.** In particular, investigate missing `Workflow_Failed` data and differences between `Iterations` and `Workflow_Completed`.
5. **Retain trial-level observations.** With three trials per workload, the individual trial results are important for interpreting variability and unusual observations.
6. **Verify the exact non-functional requirements.** Compare the observed results against the approved requirements and their exact traceability identifiers before claiming that any performance requirement has been satisfied.
7. **Document the environment.** Record the deployment configuration, test-runner details, K6 version, code revision, and other available execution metadata where possible.
8. **Avoid unsupported generalization.** These experiments characterize the configured workflow, not necessarily every SBIMS module, all real-world usage patterns, or all possible deployment conditions.

These checks do not negate the descriptive value of the experiments. They define what must be established before the measurements can support stronger claims.

## Assessment of Performance Adequacy and Scalability Extent

The K6 experiments provide preliminary empirical evidence that the SBIMS can process the tested workflow under simulated concurrency levels of 10, 25, 50, and 100 virtual users. Reported throughput increased across these workload levels, demonstrating an increase in the rate of request processing. However, the higher workload levels were associated with increased response times, and throughput gains became comparatively smaller between 50 and 100 virtual users.

These findings indicate that the system's observed performance was workload-dependent. Although most workflows were reported as successful, nonzero failures remained present, and the reported response-time distributions suggest that some requests experienced substantially greater latency than the average. Consequently, the experiments demonstrate request-processing capability under the tested conditions, but do not alone establish that the system satisfies all performance expectations for its intended operational setting.

In terms of derived scalability, the system demonstrated an increase in throughput as configured concurrency increased, but this increase was not proportional to the increase in virtual users. The limited throughput gain and increased latency observed at the highest tested workload suggest diminishing throughput returns within the tested range. This supports a cautious inference that increasing concurrency imposed additional performance pressure on the tested workflow.

The extent of performance adequacy cannot be definitively determined until the observed results are compared with the approved non-functional requirements and their specified thresholds. Likewise, the experiments do not establish maximum sustainable capacity, prove automatic infrastructure scaling, or identify a definitive performance bottleneck. These conclusions are limited to the configured workflow, test duration, workload levels, and selected deployment environment. The results should therefore be interpreted as an initial performance characterization and a limited inference about workload-dependent scalability, rather than a comprehensive capacity assessment

## Preliminary Conclusion for Discussion

Based on the previously shared K6 trial records, the SBIMS demonstrated increasing reported throughput as the configured concurrent workload increased from 10 to 100 VUs. Nevertheless, the throughput increase was not proportional to the increase in concurrency. The limited gain observed between 50 and 100 VUs, together with the increase in mean response time and P95 latency, suggests that the tested workflow experienced increasing performance pressure at higher concurrency.

The reported workflow success rates remained approximately 96.8%–97.8% on average, indicating that most tested workflows were reported as successful, while nonzero failures remained present. Because the request-count fields require verification and several trial-level anomalies were identified, the error-related findings should remain provisional until the raw output and analysis logic are checked.

For Research Objective 6 and Research Question 4, the experiments provide an initial empirical characterization of performance and a basis for inferring workload-dependent scalability behavior. The evidence is consistent with increasing throughput and diminishing throughput returns at higher tested loads. It does not, however, establish maximum sustainable capacity, prove automatic infrastructure scaling, or identify a definitive bottleneck.

The group should use these findings as a foundation for Chapter IV's presentation and interpretation of results. Chapter V should then state conclusions that directly follow from the validated evidence, acknowledge the limitations of the test configuration, and recommend targeted improvements or further testing only where justified by the results.

## Guidance for Chapter V Conclusion and Recommendations

The group preparing Chapter V may consider the following evidence-based directions:

- **Conclusion:** Summarize the observed response-time, throughput, and failure behavior across the tested workload levels, rather than describing the system simply as “scalable” or “not scalable.”
- **Recommendation for performance testing:** If additional testing is feasible, include more workload levels around the 50–100 VU range to investigate the diminishing throughput gains observed in the current experiments.
- **Recommendation for reliability:** Examine validated failed-request records and timeout evidence before recommending specific changes to the application or infrastructure.
- **Recommendation for measurement quality:** Improve the completeness and consistency of experiment metadata and derived counters so the results can be reproduced and independently verified.
- **Recommendation for future scalability evaluation:** Where available, correlate client-side K6 results with server-side and managed-service metrics before attributing performance changes to a particular component.
- **Recommendation for requirements compliance:** Evaluate the results against the approved performance requirements and their traceability references before declaring the system compliant with specific thresholds.

These are preliminary directions for the group's consideration, not final Chapter V conclusions. The final statements should reflect the verified results, the approved requirements, and the limitations acknowledged in Chapter IV.
