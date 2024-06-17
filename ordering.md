# Ordering

The parallel nature of most hardware accelerators, and the different accelerator processing time for different sized jobs can cause variances in the order in which jobs are porcessed within an accelerator. Most accelerators define an ordering model to describe their behavior with respect to ordering and some accelerators support multiple ordering models which can be configured either on a job, queue or device level.

**Ordering Domain**: The scope of the ordering model. This could be for a group of jobs, a queue or a device.

**Submission Order**: The order in which jobs are submitted to an accelerator. If jobs are submitted via a request queue, the submission order is the order in which jobs are enqueued into the submission queue.

**Completion Order**: The order in which jobs are completed by an accelerator. The nature of the completion ca vary, and may include enqueuing a completion descriptor into a completion queue or a write-back to a memory location.

**Unordered**: The accelerator does not provide any guarantees about the order of execution or completion jobs performed by the accelerator.

**Out of order Execution**: The accelerator may process jobs in a different order from their submission order.

**Execution Ordering**: The accelerator processes jobs the in the order in which they were submitted.

**Completion Ordering**: The accelerator ensures that the completions are sent in the same order as the submission order. Completion ordering only relates to the order of completions, it does not provide any guarantees related to the execution order of the jobs. This is useful for workloads, e.g. packet processing workloads, where maintaining ordering is important. An implication of completion ordering is that if two jobs A and B are submitted in the order {AB} and if the execution time for job B is shorted than job A and both A and B are processed in parallel, to provide completion odering, the completion for job B must be held back until the completion for job A is available.

**Sequential Execution Ordering**: The accelerator processes jobs in the order they were submitted, and finishes processing the first job before starting to process the next job. This ordering model is useful when there are data dependencies between two jobs such that the second depends upon the results of the first.

**Parallel Execution Ordering**: The accelerator starts processing jobs in the order they were submitted, but the jobs may be processed in parallel. For example, for two jobs A & B, with a submission order of {AB}, the accelerator will start processing job A before job B, but it may start processing job B before job A completes and their execution may run in parallel.