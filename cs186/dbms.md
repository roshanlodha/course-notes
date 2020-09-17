# Database Management System Architecture
## Architecture 
1. Parsing and Optimization: parses, checks, and verifies the SQL query and translate it into an efficient relational query plan.
2. Relational Operators: executes the query plan by operating on **records** and **files**. 
3. Files and Index Management: organizes **tables** and **records** as groups of **pages** in a logical file.
4. Buffer Management: provides the illusion of operating in memory.
5. Disk Space Management: translates page requests into physical bytes of one or more devices.
Cross-cutting issues realted to storage and memory management are **concurrency control** and **recovery**.
	- Concurrency control: Multiple users management
	- Recovery: failure management

### 5. Storage Media
Most database systems were originally designed for *magnetic, spinning* **disks**. The disk API included:
* **read**: transfering a page of data from the disk to RAM
* **write**: transfering a page of data from RAM to the disk
Reads and writes were not random (they were sequential) and were very slow.

#### Storage Heirarchy
**Registers**, **L1 cache**, and **L2 cache** all sit inside the CPU. The **RAM**,**SSD**, and **Disk** are all seperate devices. These are listed here in increasing order of time and size. Thus, currently used data is typically stored in RAM while the disk is secondary or tertiary storage. 

#### Disks
**Platters** spin  as the **arm assembly** is moved in or out of position onto a desired **track**. Accessing a disk page includes:
1. Time to access a disk block, or the **seek time**.
2. Waiting for the block to rotate under the head, or **rotational delay**.
3. Actually moving the data to and from the disk surfact, or **transfer time**. 
The key is the lower the I/O cost, or the seek and rotational delays. 

#### Flash Storage
**SSDs** are organized into cells with fast and predictable reads. Write is not predictable. 

#### Space Management
We read and write large chunks of sequential bytes for speed. Caching popular blocks similarly increased speed. **Blocks** or **pages** are a single unit of transfer for a disk. The disk space managment maps pages to locations on disks.<br>
The typical implementations either utilize the host's operating system, adding a layer of abstraction, or work directly with the disk API, providing a fast but cumbersome implementation. 

#### File Structures
1. Unordered Heap Files: records placed arbitrarily across pages
2. Clustered Heap Files: records and pages grouped in a meaningful way
3. Sorted Files: pages and recordsa are in strict sorted order
4. Index Files: B+ Trees, Linear Hasing, etc.

##### 1. Unordered Heap Files
Unordered heap files are not grouped in a particular order. To support record level operations, we must keep track of the pages in a file, free space on pages, and records on a page. 
1. Heap File as List: The heap file has a special *header page* that contains the location of the heap file and the header page saved. Each page contains 2 pointers, free space, and data. One linked-list is for full pages, while another is for free pages. This optimizes functions like store and read.
2. Page Directory: Create a *directory*, with multiple header pages each encoding a pointer to a page and the number of free bytes on that page. This approach improves on the linked-list structure in time. 

#### Page Basics:
The **header** may contain metadaata about the page, including the number of records, free space, pointers, bitmaps, etc. Records can be **fixed** or **variable** length, and layouts can be **packed** or **unpacked**. We need to be able to locate records by their IDs and must be able to delete and records. 
- Fixed Length, Packed records: We can locate records using `offset = header + (records size) * (n-1)`. Additions are appended to the end of the page, but deletions are tricky as they require repacking. Moreover, records IDs need to be updated, as they have been shifted upwards. 
- Fixed Length, Unpacked records: Using a bitmap, we can denote slots with records. A record's ID is the slot ID. Insertion finds the first empty slot in the bitmap. Deletion is similarly simple, as the bitmap slot must be cleared. 
- Variable Length, Unpacked records: By moving the header to the *footer*, we can optimize encoding. We store pointers to records as well as a pointer to the start of free space. Deletion is handled by pointer manipulation. Insertion can occur at the end of free space (updating the free space pointer) or be efficiently reorganized lazily, or on significant fragmentation. Both slots and Records grow inward, thus upon meeting we are certain that the page is full. 

#### Record Formats:
Each record in a table or relation has a fixed combination of types. Relational databased also use the same page format for data on a disk or in memory to save the cost of conversion. We can assume a system catalog (another table) stores the schema so there is no need to store type information with records. <br>
We want records to be compact and have fast access to fields. One way to achieve this in variable length strings is to use record padding. This approach is wasteful and inefficient. A better approach is use a per-record header. Using a pointer to each variable length field, we can determine exactly where each variable-length field begins and ends. 