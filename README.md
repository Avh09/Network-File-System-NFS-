# Introduction

The major components that you’ll need to implement are:

- **Clients**: Systems or users requesting access to files within the network file system (NFS). They perform file operations like reading, writing, deleting, and streaming.
- **Naming Server (NM)**: A single central hub connecting clients to storage servers. It provides the storage server details for requested files or directories, acting as a directory service.
- **Storage Servers (SS)**: Responsible for physically storing and retrieving files. They handle data persistence and facilitate efficient file storage.

Within the NFS ecosystem, clients can perform various file operations:

- **Writing a File/Folder**: Supports both synchronous and asynchronous writes.
- **Reading a File**: Retrieve contents of a file.
- **Deleting a File/Folder**: Removes files or folders to free up space.
- **Creating a File/Folder**: Allocates storage and initializes metadata for new files or folders.
- **Listing Files/Folders**: Retrieves a list of files and subfolders in a directory.
- **Getting Additional Information**: Provides metadata such as size, permissions, and timestamps.
- **Streaming Audio Files**: Allows audio files to be streamed directly.

---

# Specifications

## 1. Naming and Storage Servers

### 1.1 Initialization

**Steps to initialize the system:**

1. **Naming Server (NM) Initialization**: The NM coordinates file locations and directory structure. Its IP and port must not be hardcoded.
2. **Storage Server (SS) Initialization**:
   - Each SS (e.g., SS_1, SS_2) registers with the NM by providing:
     - **IP address**
     - **Port for NM connection**
     - **Port for client connection**
     - **Accessible paths**: Define which paths are exposed to the NFS.
   - Additional storage servers (SS_2 to SS_n) follow the same process.
3. **NM Accepts Client Requests**: Once all SS are registered, the NM starts handling client requests.

---

### 1.2 On Storage Servers

**Functionality provided by SS:**

- **Dynamic Addition of Servers**: New SS can register with NM dynamically.
- **Commands Issued by NM**:
  - Create empty files/directories.
  - Delete files/directories.
  - Copy files/directories between SS.
- **Client Interactions**:
  - **Read/Write Files**: Retrieve or update file contents.
  - **Metadata Retrieval**: Provide size and permissions.
  - **Stream Audio Files**: Send binary audio data for streaming.

---

### 1.3 On Naming Server

**Functions of NM:**

- Store and manage data about registered SS.
- Provide task feedback to clients after operations.

---

## 2. Clients

### Client Functionality:

1. **Path Finding**:
   - Example: Client sends `READ dir1/dir2/file.txt` to NM.
   - NM locates the file and provides the corresponding SS details.
   - If path is not available in server, due to server being down or ayn reason, it will READ from backup.
2. **File Operations**:
   - **Reading/Writing/Streaming**: NM facilitates the process by connecting the client to the correct SS.
   - **Creating/Deleting Files**: NM forwards the request to SS for execution and provides feedback.
   - **Copying Files/Directories**: NM manages data transfer between SS.
3. **List Accessible Paths**: NM provides a list of paths available across all registered SS.

---

## 3. Other Features

### 3.1 Asynchronous and Synchronous Writing

- **Asynchronous Writes**: Allows clients to receive immediate acknowledgment without waiting for completion.
- **Synchronous Writes**: Clients can prioritize tasks using a `--SYNC` flag.


---

### 3.2 Multiple Clients

- **Concurrent Access**:
  - NM processes multiple requests simultaneously using initial ACKs.
  - Ensures that only one client writes to a file at a time.
- **Concurrent File Reading**:
  - Multiple clients can read a file simultaneously, but write operations are exclusive.

---

### 3.3 Error Codes

Define error codes for various scenarios, such as:
- File not found.
- File locked due to a write operation.

---

### 3.4 Search in Naming Servers

- **Efficient Search**: Use data structure Hashmaps for faster lookups.
- **LRU Caching**: Implement caching for frequently accessed paths to improve performance.

