# Ordering

Ordering models dictate how requests are processed by workload accelerators.
Most accelerators define an ordering model and some accelerators support
multiple ordering models and allow the ordering model to be configured either on
a job, queue or device level.

The choice of ordering model depends upon several factors, including:

- **Workload characteristics**: Processing time variability, dependency between
  tasks, and presence of critical tasks.
- **Accelerator capabilities**: Ability to handle out-of-order processing,
  speculative execution and prioritization.
- **Performance goals**: Optimizing for htroughput, latency or a balance of
  both.

The most common ordering models are described below.

## In-Order Processing

- A FIFO ordering model.
- Maintains the original order of workload requests received by the accelerator.
  This applies to the order in which requests are executed and to the order in
  which their completions are sent.
- Might not be optimal for workloads with varying processing times for
  individual requests.
- Slower requests can hold up faster ones, reducing overall throughput. This is
  a form of Head of Line Blocking (HOLB).

## Out-of-Order Processing

- Processes requests out of the original order, prioritizing those that can
  finish quicker.
- Improves overall throughput by utilizing idle accelerator resources.
- Requires more complex hardware to manage dependencies and ensure correct final
  output.
- May introduce higher latency for individual requests compared to in-order
  processing.

## Priority Based Processing

- Useful for scenarios where certain tasks are critical and need faster
  turnaround times.
- Assigns priorities to workload requests, with higher priority tasks processed
  first.
- Requires a mechanism to define and manage priorities.
- Lower priority tasks might experience significant delays.

## Speculative Processing

- Attempts to predict future workloads and pre-process requests speculatively.
- Can significantly improve performance if predictions are accurate.
- Introduces wasted resources if predictions are wrong.
- Requires sophisticated hardware and software for accurate prediction.

## Execution vs Completion Processing

An accelerator may support different ordering models for the execution of
requests than for the ordering of completions. As an example, an accelerator may
only support out-of-order processing for executing the requests, but may support
both in-order and out-of-order completions.
