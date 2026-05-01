# Database Indexing

## 1. Physical Data Storage (How Data Lives on Disk)

Before indexing can be understood, we must understand how the database actually stores information. It is not just a simple table; it is a complex hierarchy of pages and blocks.

### Data Pages (The Logical Unit)

- **Definition:** The primary unit of storage managed by the DBMS.
- **Size:** Generally **8 KB**.
- **Capacity:** If a row is 64 bytes, one page can store roughly **125 rows**.

**Internal Structure of a Data Page:**

| Section | Size | Purpose |
|---|---|---|
| Header | 96 bytes | Page number, free space info, checksum |
| Data Records | 8060 bytes | Where the actual rows are stored |
| Offset Array | 36 bytes | Pointers to data records within the page, allows logical ordering without physically moving rows |

### Data Blocks (The Physical Unit)

- **Definition:** The minimum amount of data read/written by a single I/O operation.
- **Size:** Ranges from 4 KB to 32 KB (most commonly **8 KB**).
- **Management:** Unlike data pages, blocks are managed by the underlying storage system (disk/SSD), not the DBMS.
- **Mapping:** The DBMS maintains a mapping of which Data Page is stored in which physical Data Block.

---

## 2. The Mechanics of Indexing: B+ Trees

Without an index, the database must perform a **Full Table Scan** ($O(n)$), checking every row. Indexing uses the **B+ Tree** data structure to achieve $O(\log n)$ performance.

### How the B+ Tree Functions

- **Sorted Data:** Maintains data in a sorted manner for rapid searching.
- **Node Rules:** In an $M$ order tree, a node can have $M$ children and $M-1$ keys.
- **Root & Intermediate Nodes:** Act as signposts to guide the search; they do not necessarily hold the actual index value if it has been deleted.
- **Leaf Nodes:** The bottom-most nodes that hold the actual indexed column values and pointers to the data pages.

---

## 3. Types of Indexing

### Clustered Index

- **Definition:** The physical order of the rows in the data pages matches the logical order of the index.
- **Limit:** Only **one** clustered index per table (because data can only be physically sorted one way).
- **Priority:**
  1. Primary Key (automatically becomes the clustered index).
  2. If no primary key, the DBMS creates a hidden unique column to act as the clustered index.

### Non-Clustered Index (Secondary Index)

- **Definition:** A separate structure from the data rows. The index contains the indexed values and a pointer back to the data page where the row lives.
- **Limit:** You can have **multiple** non-clustered indexes (e.g., on Name, Email, or Address).
- **Trade-off:** Requires additional disk space and memory, and every `INSERT` or `DELETE` operation requires the DBMS to update every non-clustered index.

---

## 4. Database Operations: Behind the Scenes

### The "Insert" Process & Page Splitting

1. **Find Page:** The DBMS uses the B+ Tree to find the correct logical location for the new row.
2. **Check Space:** It loads the corresponding Data Page.
3. **Page Split:** If the page is full (8 KB limit), the DBMS performs a **Page Split** — creates a new page and redistributes the data (e.g., moves half the rows to the new page).
4. **Update Pointers:** The B+ Tree nodes are updated to point to the new page locations.

### The "Search" Process

1. **Load Index:** The DBMS loads the Index Page into memory.
2. **Traverse Tree:** It follows the B+ Tree pointers to the leaf node.
3. **Identify Page:** The leaf node provides the specific Data Page ID.
4. **Locate Block:** The DBMS uses its mapping to find the physical Data Block on the disk.
5. **Fetch Data:** The specific block is loaded, and the row is retrieved using the offset array.
