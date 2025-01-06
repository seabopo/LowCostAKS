
### PROJECT GOAL
To build an Azure Kubernetes Service (AKS) test cluster to start studying for
some certifications. My Visual Studio Subscription comes with a $50 monthly
Azure credit, so this is the budget target for my test subscription.

### CLUSTER REQUIREMENTS
To be able to test the following:
  * Linux and Windows nodes.
  * Multiple nodes for affinity and anti-affinity testing.
  * Availability zones to test Topology Spread Constraints.
  * NGINX-INGRESS using a load balancer.
  * A public IP.

### CLUSTER COMPONENTS AND COSTS
The minimum cluster components required:
  * A [Kubernetes Cluster](https://learn.microsoft.com/en-us/azure/aks/free-standard-pricing-tiers) 
    control plane for cluster management. There are three available SKUs: Free, Standard and Premium. 
    Since this is a test cluster I'll use the free SKU. The Free SKU includes all AKS features and supports 
    clusters up to 1,000 nodes, though Microsoft recommends 10 or fewer nodes for a free cluster.
  * A [load balancer](https://learn.microsoft.com/en-us/azure/load-balancer/skus) for public ingress/egress. 
    AKS clusters support two different load balancer SKUs: Basic and Standard. The Basic SKU is free, but only 
    supports a single VM Scale set and doesn't support Availability Zones. The Standard SKU will have to be used 
    to support all the cluster requirements above. It will cost approximately $15/month if used 24x7 with low 
    amounts of traffic.
  * A [public IP address](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses) 
    (~$3/month) will be required for public access.
  * A low-cost VM SKU for nodes, which is covered in the next section.
  
### SELECTING A LOW-COST VM SKU
Most resources on the internet recommend the 
[B-Series](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes-b-series-burstable) VM SKUs because
they are very budget-friendly, starting around $30/month for both Linux and Windows. However, Microsoft no longer 
allows the use of B-Series VMs as System Nodes for an AKS cluster because of performance issues with the scheduler, 
so another SKU has to be used. 

The following list contains lower-cost, General Purpose SKUs that support Local (Ephemeral) Storage 
as of 12/15/2024. Unfortunately the cheapest usable VM Size for the system node pool is now almost 
double the cost of the equivalent B-Series SKUs at around $50 (ARM) or $60 (x64) per month.

| VM Size       |vCPUs| RAM |Disks|IOPS |LocalDisk   |Lnx24 |Lnx4 |Win24 |Win4 | Notes |
|---------------|-----|-----|-----|-----|------------|------|-----|------|-----|-------|
|   B2s         |  2  |  4  |  4  |1,280|   8 (SCSI) | $28  | $5  | $33  | $6  | (s)   |
|   D2plds_v5   |  2  |  4  |  4  |3,750|  75 (SCSI) | $49  | $9  | $101 | $17 | (a)   |
|   B2ms        |  2  |  8  |  4  |1,920|  16 (SCSI) | $56  | $10 | $61  | $10 | (s)   |
|***D2alds_v6***|  2  |  4  |  4  |4,000| 110 (NVME) | $61  | $10 | $131 | $22 |       |
|   D2lds_v5    |  2  |  4  |  4  |3,750|  75 (SCSI) | $61  | $10 | $113 | $19 |       |
|***D2as_v4***  |  2  |  8  |  4  |3,200|  16 (SCSI) | $61  | $10 | $113 | $19 |       |
|   D2s_v3      |  2  |  8  |  4  |3,200|  16 (SCSI) | $62  | $10 | $117 | $20 |       |
|***D2ads_v5*** |  2  |  8  |  4  |3,750|  75 (SCSI) | $65  | $11 | $117 | $20 |       |
|   D2ds_v4     |  2  |  8  |  4  |3,200|  75 (SCSI) | $72  | $12 | $124 | $21 |       |
|   D2ds_v5     |  2  |  8  |  4  |3,700|  75 (SCSI) | $72  | $12 | $124 | $21 |       |
|   D4plds_v5   |  4  |  8  |  8  |6,400| 150 (SCSI) | $98  | $16 | $201 | $34 | (a)   |
|   B4ms        |  4  | 16  |  8  |2,880|  32 (SCSI) | $112 | $19 | $122 | $20 | (s)   |
|   D4alds_v6   |  4  |  8  |  8  |7,600| 220 (NVME) | $121 | $20 | $224 | $37 |       |
|   D4lds_v5    |  4  |  8  |  8  |6,400| 150 (NVME) | $122 | $20 | $226 | $38 |       |
|   D4ads_v5    |  4  | 16  |  8  |6,400| 150 (SCSI) | $131 | $22 | $235 | $39 |       |

**Notes**
  * VM Size costs are monthly for 24 hours (Lnx/Win 24) and 4 hours (Lnx/Win 4) per day for 30 days.
  * VM Size costs do not include the storage costs, which are approximately $14 per month ($0.02/hr) if run 24x7.
    Storage costs are calculated from nodes running for 1 week. The nodes reported a 120GB disk rather than the
    local disk sizes listed above, which are taken from Microsoft's SKU list.
  * Local Disk sizes are in GiB.
  * For any VM Size other than the Burstable (B-Series) the Windows license cost doubles the monthly cost.
  * (a) = ARM SKUs, which may be less compatible with some 3rd party services.
  * (s) = SKUs that can't be used for System-Mode Node Pools.
  * [Azure Virtual Machine Naming Conventions](
     https://learn.microsoft.com/en-us/azure/virtual-machines/vm-naming-conventions)  
      a = AMD-based processor  
      b = Block Storage performance  
      d = diskful (that is, a local temp disk is present)  
      i = isolated size  
      l = low memory; a lower amount of memory than the memory intensive size  
      m = memory intensive; the most amount of memory in a particular size  
      p = ARM Cpu  
      t = tiny memory; the smallest amount of memory in a particular size  
      s = Premium Storage capable, including possible use of Ultra SSD   


### **ESTIMATED CLUSTER COSTS**
The **D2plds_v5** SKU is the cheapest system node with 2 virtual CPUs and 4GB of RAM (the Kubernetes minimum
requirement). Building a single-node cluster based on this SKU had the node using 38% of it's memory, so NGINX 
and a small number of other services should be able to run on those nodes. The B-Series SKUs still works well 
for app nodes, and they only cost an extra $5 or $10 per month for the Windows licenses where every other SKU 
doubles the monthly cost for a Windows OS vs a Linux OS.

Running a 2-node cluster based on these SKUs for one week accrued the following subscription costs:

| Cluster Component                     | WeeklyCost | HourlyCost |
|---------------------------------------|------------|------------|
| System Pool Node (D2plds_v5)          |     $11.23 |     $0.067 |
| System Pool Node Disk (128GB)         |      $2.91 |     $0.017 |
| Application Pool Node (B2s)           |      $7.50 |     $0.045 |
| Application Pool Node Storage (128GB) |      $3.43 |     $0.020 |
| Public IP Address                     |      $0.65 |     $0.004 |
| Kubernetes Standard Load Balancer     |      $3.26 |     $0.019 |
| Total                                 |     $28.98 |     $0.173 |


Based on those costs, here are estimates for a variety of cluster configurations running at 24, 12, 8 and 4 
hours per day for 30 days: 

| Nodes           |SKUs            |vCPUs|RAM|Disk | $/hr|hrs/$50| $/day|24x7|12x7| 8x7| 4x7|
|-----------------|----------------|-----|---|-----|-----|-------|------|----|----|----|----|
| 1 S             |D2plds_v5       |  2  | 4 |  75 |$0.11|  458  | $2.62| $79| $39| $26| $13|
| 1 S + 1 L       |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.17|  298  | $4.03|$121| $60| $40| $20|
| 1 S + 1 W       |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.17|  286  | $4.20|$126| $63| $42| $21|
| 1 S + 1 L + 1 W |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.23|  214  | $5.61|$168| $84| $56| $28|
| 1 S + 1 W       |D2plds_v5 + B2ms| 2/2 |4/8|75/16|$0.21|  234  | $5.13|$154| $77| $51| $26|
| 1 S + 1 W       |D2ads_v5  + B2ms| 2/2 |8/8|75/16|$0.24|  212  | $5.66|$170| $85| $57| $28|
| 1 S + 3 L       |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.29|  175  | $6.86|$206|$103| $69| $34|
| 1 S + 2 L + 2 W |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.36|  137  | $8.74|$262|$131| $87| $44|
| 1 S + 3 L + 3 W |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.48|  103  |$11.60|$348|$174|$116| $58|
| 3 S + 3 L + 3 W |D2plds_v5 + B2s | 2/2 |4/4|75/8 |$0.97|   52  |$23.30|$699|$349|$233|$116|

Nodes:
 - S = Linux System Node
 - L = Linux Application Node
 - w = Windows Application Node

\
Next: [Estimating Cluster Costs](.\01_costs.html)



<style>
    p + ul { margin-block-start: -15px }
    p      { margin-block-start:  20px }
    h3     { margin-block-end:   -16px; font-weight: bold; }
    h4     { margin-block-end:   -16px; font-weight: bold; }
</style>

<!--
P + ul {
    display: block;
    list-style-type: disc;
    margin-block-start: -15px;
    margin-block-end: 1em;
    margin-inline-start: 0px;
    margin-inline-end: 0px;
    padding-inline-start: 40px;
}
-->
