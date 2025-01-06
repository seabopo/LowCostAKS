
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

\
Next: [Estimating Cluster Costs](.\02_costs.html) \
Previous: [Readme](.\index.html)
 