
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


Based on those costs, here are estimates for a variety of cluster configurations 
running at 24, 12, 8 and 4 hours per day for 30 days: 

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

Unfortunately, even a single node cluster using the cheapest SKU available can't be run 24x7 for $50/month.

\
Next: [Installing Local Software Components](.\03_install_tools.html) \
Previous: [Required Components](.\01_components.html) \
[Table of Contents](.\index.html)
