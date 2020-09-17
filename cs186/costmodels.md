# Cost Models
We want a big picture estiamte for data access to determine which implementation works best. To do this, we must clearly identify our assumptions up front and then estimate the cost in a principled way. 

## Cost Model for Analysis
Variables:
* B: Number of data blocks in a file
* R: Number of records per block
* (Average) time to read/write disk block
Assumptions:
* Ignore sequential vs random I/O
* Ignore cache eviction costs (we also assuming that data must be brought into memory to be operated on)
* Ignore any CPU costs after fetching data into memory 
Operation|Heap File|Sorted File
---|---|---
scan records|B\*D|B\*D
equality search|0.5\*B\*D|log<sub>2</sub>B\*D
range search|B\*D|(log<sub>2</sub>B+pages)\*D
insert record|2\*D|(log<sub>2</sub>B+B)\*D
delete record|(0.5\*B+1)\*D|(log<sub>2</sub>B+B)\*D

