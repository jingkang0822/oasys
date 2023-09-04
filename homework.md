# Oasys home-work

## 1. Instant Verifier in Oasys Network
### Explain the benefits of the instant verifier in the Oasys network. Detail the mechanism as thoroughly as possible.

The introduction of the "Instant Verifier" in the Oasys network serves as a pivotal advancement over traditional Optimistic Rollups. While conventional Optimistic Rollups require a seven-day waiting period to achieve transaction finality, Oasys slashes this time considerably. Below is an intricate dissection of this mechanism:

- **Origin State Root & Sequencer Replica Node**: These serve as the cornerstones of the state root, where the Origin State Root represents the pristine, initial state and the Sequencer Replica Node is akin to its dynamic mirror.
- **Hub-layer Validators**: These validators act as arbiters, validating the state transitions between the Origin State Root and the Sequencer Replica Node.
- **Instant Verification**: A swift verification is triggered once 50% or more of these hub-layer validators affirm the authenticity of the state root.


<div style="text-align:center;">
  <img alt="PNG" src="https://docs.oasys.games/assets/images/verifier-e3eb916a7fbbf59e285806c021ad7f41.png" 
  width="560" height="320" 
  style="margin-top: 20px; margin-bottom: 20px;" />
</div>


By hastening the process of transaction finality, the Instant Verifier enhances the overall user experience. This quick turnaround is particularly invaluable in applications demanding immediate transaction finality.

In essence, Oasys harmonizes the rigor of Optimistic Rollups with the expedience of real-time state confirmations via the Instant Verifier.

## 2. Consensus Mechanism
### Describe our consensus mechanism in detail. Focus especially on how validators are selected and the rules for fork choice.


The consensus algorithm that underpins the Oasys Network is a Delegated Proof of Stake (DPoS), an advanced iteration of the traditional Proof of Stake (PoS) system. Here, we delve deep into the mechanisms of validator selection and the fork choice rules.

### **Validator and Miner Selection**

1. **Becoming a Validator**: Aspiring validators must stake a minimum of 10 million OAS tokens through a validator contract and register their corresponding address. Changes in stake values are processed at the close of each Epoch, and falling below the 10 million OAS threshold leads to automatic disqualification for the subsequent Epoch.

2. **Delegators and Voting**: Delegators, essentially the token holders, wield voting power. A validator's stake and the votes they garner collectively determine their selection for validation duties.

3. **Miner Election Logic**: Block creators or miners are randomly selected from the pool of validators, albeit in a weighted manner based on their stakes. A non-responsive elected miner triggers a re-election by the consensus algorithms.

4. **Handling Inactivity**: Validators showing consistent inactivity or failure in block production are sidelined from the validation process, though their staked tokens remain unaffected.

5. **Epochs and Rewards**: An Epoch, approximately a day long, consists of 5760 blocks. Staking rewards are calculated based on a validator's uptime during each Epoch. An inactive validator is excluded from these rewards.

The consensus framework of Oasys strikes a balance between random selection and meritocracy, with substantial weight given to stake and performance metrics. The structure democratizes the influence of token holders while ensuring a dependable and secure validation process.

### **Fork Choice Rules**

1. **Uncle Blocks**: Unique to Oasys, the concept of "Uncle Blocks" is absent. Block producers are stringently appointed via consensus, negating the need for secondary branches.


## 3. Memory Consumption Issue
### The current instant verifier is gradually consuming more memory. Identify the possible root cause.

As the head of the team, I propose a bifurcated strategy to combat the escalating memory consumption issues we've encountered lately. Our focus should pivot between conceptual scrutiny of our L2 roll-up mechanism and practical debugging measures.

## Section 1: Understanding L2 Mechanism Evolutions
The first leg of our strategy concentrates on demystifying alterations in our L2 roll-up mechanism, especially compared to its predecessor, the Optimism roll-up system. The Oasys documentation highlights several key changes:


1. **Instant Verifier**: Unlike traditional Optimistic Rollups with a 7-day challenge period, Oasys uses an 'Instant Verifier' to speed up state commitment finalization.
   - **Potential Issue**: Accelerated state commitment might skip certain memory-clearing steps or maintain redundant data.
   - **Suggested Action**: Review the Instant Verifier's code for potential memory retention or variable de-referencing issues. <br><br>
   
2. **Hub-layer Validators**: A new layer of validators is introduced, responsible for validating state commitments.
   - **Potential Issue**: Additional complexity and layers could be resource-intensive.
   - **Suggested Action**: Audit validator resource usage, especially during instantiation and destruction phases. <br><br>
   
3. **50% Rule for Instant Verification**: Instant verification is enabled when 50% or more of the Hub-layer validators confirm the validity of a state root.
   - **Potential Issue**: Queuing or caching state roots while waiting for consensus may escalate memory usage.
   - **Suggested Action**: Inspect the storage and clearance mechanisms for state roots during the consensus waiting period. <br><br>
   
4. **Sequencer Replica Node**: This component acts as a continuously updated reflection of the Origin State Root.
   - **Potential Issue**: Continuous updates could lead to memory bloat if older states are not efficiently managed.
   - **Suggested Action**: Check the state root management in the Sequencer Replica Node, particularly the purging of older states. <br><br>

5. **Conditional Finality**: Oasys employs a flexible finality based on validator consensus.
   - **Potential Issue**: Added conditional logic might lead to inefficient memory allocation.
   - **Suggested Action**: Review the conditional statements and logic gates that implement flexible finality for any inefficiencies or resource leaks. <br><br>

By systematically evaluating these specific areas, we can more effectively isolate and address the root causes of the observed memory leaks.

## Section 2: Hands-on Debugging Measures
Our second focal point is pragmatic: employing concrete tools and methods to unearth the roots of our memory consumption issue.

### A. Leverage Go's Native Profiling Tools
- **Step 1**: Generate a Heap Dump  
  Execute the command `curl -s http://localhost:6060/debug/pprof/heap -o heap.out` to create a heap dump.

- **Step 2**: Examine the Heap Dump  
  Use the `go tool pprof heap.out` command to launch an interactive interface for inspecting memory allocation.

### B. Deploying **`goleak`** for Goroutine Surveillance
- **Step 1**: Initial State Capture  
  Start by capturing the existing state of all active goroutines to set a benchmark for leakage detection.

- **Step 2**: Run Program Logic  
  Execute the likely sections of your program where goroutine leakage could occur.

- **Step 3**: State Comparisons  
  Conduct a before-and-after comparison of the goroutine states to spot any abnormalities.

### C. Miscellaneous Debugging Strategies
- **Focus on Recent Changes**: Direct your attention towards recently modified objects or mechanisms.
- **Debug Mode**: Engage your code in a test environment under debug mode to identify incrementally expanding objects.
- **Manual Oversight**: Integrate debugging or logging instruments at strategic points in your code for object status assessment.

I hope this structured framework aids in quickly identifying the issue. I will be at the Token2049 conference in Singapore and look forward to potentially connecting and collaborating.