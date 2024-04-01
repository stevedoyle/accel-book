# Estimating Performance

## Amdahls Law

Amdahl's Law is a principle in computer architecture and parallel computing that governs the potential speedup of parallelizable tasks. It was formulated by computer architect Gene Amdahl in 1967.

The essence of Amdahl's Law revolves around the concept that not all parts of a program can be parallelized. Even in programs designed to run in parallel, there are often sequential portions that cannot be split into concurrent tasks. Amdahl's Law quantifies the maximum possible speedup that can be achieved by parallelizing a computation.

Mathematically, Amdahl's Law is expressed as follows:

$\text{Speedup} = \frac{1}{(1 - P) + \frac{P}{N}}$

Where:

- $\text{Speedup}$ is the improvement in performance achieved by executing a task in parallel compared to executing it serially.
- $P$ is the proportion of the computation that can be parallelized.
- $N$ is the number of processing units or cores available.

From this formula, we can derive several important insights:

1. As $N$ increases, the speedup approaches a maximum limit, which is $1/(1 - P)$. This limit indicates the theoretical maximum speedup achievable by parallelizing a computation.
2. Amdahl's Law highlights that even if a small portion of a program cannot be parallelized (even as $P$ approaches 0), the potential speedup is limited.
3. The effectiveness of parallelization is heavily influenced by the portion of the computation that can be parallelized ($P$). If $P$ is small, the potential speedup will be limited regardless of the number of processing units.

Amdahl's Law is crucial for understanding the trade-offs involved in parallel computing. It underscores the importance of identifying and optimizing parallelizable portions of algorithms to achieve significant performance improvements. Additionally, it emphasizes the diminishing returns of adding more processing units once the parallelizable portion has been fully exploited.

## Accelerator Performance Benefit Upper Bound

Amdahl's Law can be used to estimate the upper bound performance benefit of a workload accelerator when applied to a portion of a workload. In this case:

- $\text{Speedup}$ is the improvement in performance achieved by using an accelerator to speedup a portion of the workload.
- $P$ is the proportion of the workload that can be accelerated.
- $N$ is the acceleration factor which is the throughput of the accelerated portion vs using the general purpose CPU to process the accelerated portion.

The upper bound performance benefit of the accelerator is then the maximum limit, which is $1/(1-P)$.

## Accelerator Real World Performance Benefit

In reality, the maximum theoretical performance benefit of an accelerator is difficult to achieve. There is usually a setup cost associated with using an accelerator. This could be preparing and sending descriptors to a PCIe attached accelerator or a GPU or preparing registers when using a co-processor or specialized CPU instructions. This setup cost can be viewed as increasing the non-acceleratable (or serialized) portion of the workload which essentially slightly reduces the proportion of the workload that can be accelerated.

- Explain why the upper bound performance is usually not achievable.
- Give some examples of items that can reduce the benefit.
- Use Amdahls law to show some curves showing the tradeoffs and impacts of overheads.

## Systematic Approach

- Define a systematic approach to generating a first order estimate of the seedup that an accelerator can bring to a workload.
