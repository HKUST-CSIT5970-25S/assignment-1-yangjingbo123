[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: Yang Jingbo

### Student Id: 21085156

### Email: jyangdw@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > The measurement tool used in the tests is **`sysbench`**, a widely used open-source benchmarking tool that can measure CPU and memory performance, among other things.
    >
    > ------
    >
    > ### **Configuration of the Measurement Tool**
    >
    > The configuration of `sysbench` was tailored to measure CPU and memory performance effectively. Below are the parameters used and the rationale for each:
    >
    > #### **1. CPU Test Configuration**
    >
    > - Command:
    >
    >   ```
    >   sysbench cpu --cpu-max-prime=20000 run
    >   ```
    >
    > - Parameters:
    >
    >   - ```
    >     --cpu-max-prime=20000
    >     ```
    >
    >     : This parameter sets the upper limit for prime number calculations. A higher value increases the computational load, making it suitable for testing CPU performance.
    >
    >     - **Why this value?**: `20000` is a reasonable value that balances computational intensity and test duration. It ensures the CPU is sufficiently stressed without making the test unnecessarily long.
    >
    > #### **2. Memory Test Configuration**
    >
    > - Command:
    >
    >   ```
    >   sysbench memory --memory-block-size=1K --memory-total-size=10G run
    >   ```
    >
    > - Parameters:
    >
    >   - ```
    >     --memory-block-size=1K
    >     ```
    >
    >     : This parameter sets the size of each memory block to 1 KB.
    >
    >     - **Why this value?**: A smaller block size (e.g., 1 KB) increases the number of memory operations, making it suitable for testing memory latency and throughput.
    >
    >   - ```
    >     --memory-total-size=10G
    >     ```
    >
    >     : This parameter sets the total amount of data to be transferred during the test to 10 GB.
    >
    >     - **Why this value?**: A larger total size ensures the test runs long enough to provide stable and reliable results.

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.micro` | 884.7379 (events/s) | 545.03 (MiB/sec) |
    | `t2.medium`  | 1658.32 (events/s) | 868.69 (MiB/sec) |
    | `c5d.large` | 733.19 (events/s) | 9705.56(MiB/sec) |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.
    
    #### **1. CPU Performance**
    
    - **`t2.micro`**: 884.7379 events/s
    - **`t2.medium`**: 1658.32 events/s
    - **`c5d.large`**: 733.19 events/s
    
    **Observations**:
    
    - The `t2.medium` instance shows the highest CPU performance (1658.32 events/s), which is **~1.87x** faster than the `t2.micro` instance.
    - Surprisingly, the `c5d.large` instance has the lowest CPU performance (733.19 events/s), which is **~0.83x** slower than the `t2.micro` instance.
    
    **Reason**:
    
    - The `t2` instances use burstable CPU performance, which allows them to temporarily use higher CPU resources when needed. This may explain why the `t2.medium` outperforms the `c5d.large` in this test.
    - The `c5d.large` instance, while having more vCPUs, may not have been fully utilized during the `sysbench` CPU test, leading to lower performance.
    
    #### **2. Memory Performance**
    
    - **`t2.micro`**: 545.03 MiB/sec
    - **`t2.medium`**: 868.69 MiB/sec
    - **`c5d.large`**: 9705.56 MiB/sec
    
    **Observations**:
    
    - The `c5d.large` instance has significantly higher memory performance (9705.56 MiB/sec), which is **~11.16x** faster than the `t2.medium` instance and **~17.8x** faster than the `t2.micro` instance.
    - The `t2.medium` instance shows moderate memory performance (868.69 MiB/sec), which is **~1.59x** faster than the `t2.micro` instance.
    
    **Reason**:
    
    - The `c5d.large` instance is optimized for compute-intensive workloads and has higher memory bandwidth, which explains its exceptional memory performance.
    - The `t2` instances have lower memory bandwidth, which limits their memory performance.
    
    ------
    
    ### **Does Performance Increase with vCPUs and Memory?**
    
    - **CPU Performance**: No, the performance does not increase linearly with the number of vCPUs. The `t2.medium` outperforms the `c5d.large` despite having fewer vCPUs, likely due to burstable CPU performance.
    - **Memory Performance**: Yes, the memory performance increases significantly with higher memory resources. The `c5d.large` instance, with more memory and higher bandwidth, demonstrates vastly superior memory performance compared to the `t2` instances.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` | 3.58 Gbits/sec | 0.286    |
    | `m5.large` - `m5.large`   | 4.80 Gbits/sec | 0.188    |
    | `c5n.large` - `c5n.large` | 9.87 Gbits/sec | 0.146    |
    | `t3.medium` - `c5n.large` | 2.12 Gbits/sec | 0.689    |
    | `m5.large` - `c5n.large`  | 2.18 Gbits/sec | 0.647    |
    | `m5.large` - `t3.medium`  | 4.26 Gbits/sec | 0.222    |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

    1. **Same Instance Types**:
       - Instances of the same type generally exhibit **higher TCP bandwidth** and **lower RTT** compared to instances of different types.
       - For example:
         - `c5n.large` - `c5n.large` has the highest TCP bandwidth (9.87 Gbits/sec) and the lowest RTT (0.146 ms).
         - `t3.medium` - `t3.medium` has lower TCP bandwidth (3.58 Gbits/sec) and higher RTT (0.286 ms) compared to `c5n.large` instances.
    2. **Different Instance Types**:
       - Instances of different types generally exhibit **lower TCP bandwidth** and **higher RTT** compared to instances of the same type.
       - For example:
         - `t3.medium` - `c5n.large` has a TCP bandwidth of 2.12 Gbits/sec and an RTT of 0.689 ms, which is significantly lower and higher, respectively, compared to same-type instances.
    3. **Performance Variability**:
       - The **TCP bandwidth** and **RTT** vary based on the instance types being compared.
       - For example:
         - `m5.large` - `t3.medium` has a higher TCP bandwidth (4.26 Gbits/sec) and lower RTT (0.222 ms) compared to `t3.medium` - `c5n.large` or `m5.large` - `c5n.large`.

    ### Key Takeaways:

    - **Same-type instances** provide better network performance (higher TCP bandwidth and lower RTT) due to optimized communication and resource allocation.
    - **Different-type instances** experience reduced performance, likely due to differences in hardware capabilities, network configurations, or resource contention.
    - The `c5n.large` instance type, designed for compute-intensive workloads, consistently provides the best network performance among the tested instances.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      | 32.8 Mbits/sec | 65.777   |
    | N. Virginia - N. Virginia | 4.88 Gbits/sec | 0.226    |
    | Oregon - Oregon           | 4.99 Gbits/sec | 0.089    |

    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.

1. **Same Region Performance**:
   - Instances deployed in the **same region** (e.g., `N. Virginia - N. Virginia` and `Oregon - Oregon`) exhibit **high TCP bandwidth** and **low RTT**.
   - For example:
     - `N. Virginia - N. Virginia`: TCP bandwidth = 4.88 Gbits/sec, RTT = 0.226 ms.
     - `Oregon - Oregon`: TCP bandwidth = 4.99 Gbits/sec, RTT = 0.089 ms.
   - This is because communication within the same region occurs over a high-speed, low-latency internal network.
2. **Different Region Performance**:
   - Instances deployed in **different regions** (e.g., `N. Virginia - Oregon`) experience **significantly lower TCP bandwidth** and **much higher RTT**.
   - For example:
     - `N. Virginia - Oregon`: TCP bandwidth = 32.8 Mbits/sec, RTT = 65.777 ms.
   - This is due to the physical distance between regions, which introduces higher latency and reduces bandwidth due to network congestion and routing overhead.
3. **Comparison**:
   - The TCP bandwidth between regions is **~150 times lower** compared to the same region.
   - The RTT between regions is **~290 times higher** compared to the same region.

### Key Takeaways:

- **Same-region communication** provides optimal network performance, with high bandwidth and low latency, making it ideal for latency-sensitive or high-throughput applications.
- **Cross-region communication** suffers from significantly reduced performance due to the physical distance and network infrastructure limitations. This makes it less suitable for latency-sensitive or high-throughput workloads.
- When deploying applications, it’s crucial to consider the region placement of instances to minimize latency and maximize bandwidth, especially for distributed systems or real-time applications.