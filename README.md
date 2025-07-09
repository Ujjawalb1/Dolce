# Custom ERP Connector

Custom ERP Connector is a Python package designed to streamline integration with various ERP systems. It enables seamless database connections, data retrieval, and processing, allowing users to build custom connectors tailored to their requirements. This document outlines the installation, configuration, and deployment of the **Custom ERP Connector**, ensuring seamless ERP integration and automation.

Connects to Oracle, MySQL, PostgreSQL, MSSQL, NetSuite databases.
The script uses **APScheduler** to execute at regular intervals. Adjust scheduling as per business needs.


## Configuration File (`db-config.json`)
```json
{
  "dbType": "oracle,mysql,postgres",
  "connectionDetails": {
    "host": "HOST",
    "database": "DATABASE",
    "user": "USER",
    "port": "PORT",
    "password": "PASSWORD",
    "dbDriver": "driver installation path"
  },
  "env": "sandbox,prod",
  "authToken": "",
  "erpInstanceId": ""
}
```
- For MSSQL, use `"dbDriver": "ODBC Driver 18 for SQL Server"`

## Installation Methods
   ### ERP Connector can be installed by these methods:
   - ### Docker
   - ### Pip
   - ### NSSM (Windows Service)
### 1. Docker

#### Installation

##### Linux
Follow the official Docker installation guide: [Docker for Linux](https://docs.docker.com/engine/install/)

##### Windows (10/11)
1. **Check system requirements**:[Windows Installation Guide](https://docs.docker.com/desktop/setup/install/windows-install/)
2. **Verify virtualization is enabled**:
   <p>
   <img src="Docs/img_1.png" width="400">
   </p>
   
   **If not enabled, enable it in BIOS**: [Enable Virtualization](https://support.microsoft.com/en-us/windows/enable-virtualization-on-windows-c5578302-6e43-4b4b-a449-8ced115f58e1)
3. **Enable Hyper-V and WSL**:
   <p>
   <img src="Docs/img_2.png" width="400">
   </p>
   <p>
   <img src="Docs/img_3.png" width="400">
   </p>
4. **Download and install Docker Desktop**: [Download Docker](https://docs.docker.com/desktop/setup/install/windows-install/)

#### Running the Docker Image
1. **Pull the Docker image**:
   ```bash
   docker pull tanmaywalekar/custom_connector:latest
   ```
2. **Prepare configuration**:
   - Create a directory named `clear` with a subdirectory `config`.
   - Place `db-config.json` inside the `config` folder. 
   - Example `db-config.json`:
   ```json
   {
     "dbType": "oracle,mysql,postgres",
     "connectionDetails": {
       "host": "HOST",
       "database": "DATABASE",
       "user": "USER",
       "port": "PORT",
       "password": "PASSWORD",
       "dbDriver": "driver installation path"
     },
     "env": "sandbox,prod",
     "authToken": "",
     "erpInstanceId": ""
   }
   ```
   - For MSSQL, use `"dbDriver": "ODBC Driver 18 for SQL Server"`
   - For Oracle, use `/opt/oracle/instantclient_23_7`
3. **Run the container in `clear` directory**:
   - **Linux/macOS**:
     ```bash
     docker run -v $(pwd)/config:/app/config tanmaywalekar/custom_connector
     ```
   - **Windows**:
     ```bash
     docker run -v ${PWD}/config:/app/config tanmaywalekar/custom_connector
     ```

#### Developer Commands
1. **Build and test Docker image**:
   ```bash
   docker build -t custom_connector:latest -f Dockerfile.erp-connector.multiarch .
   ```
2. **Save image as a tar file**:
   ```bash
   docker save -o connectorDockerImage.tar custom_connector:latest
   ```
3. **Load image from tar file**:
   ```bash
   docker load -i connectorDockerImage.tar
   ```
4. **Create cross-platform builds and push to Docker Hub**:
   ```bash
   ./cross-platform-docker-build.sh
   ```

### 2. Install Using Pip

#### Prerequisites
- **Python 3.11**
- **pip (Python package installer)**

#### Installation

##### Windows
1. Download Python 3.11 from [Python Downloads](https://www.python.org/downloads/windows/).
2. Run the installer and check **"Add Python 3.11 to PATH"**.
3. Complete the installation.

##### macOS
1. Download Python 3.11 from [Python Downloads](https://www.python.org/downloads/macos/).
2. Run the installer and follow the instructions.
3. Verify installation:
   ```bash
   python3.11 --version
   ```

#### Generate and Install Package
1. Go to project directory and Generate the package:
   ```bash
   python3.11 setup.py sdist
   ```
2. Install the package:
   ```bash
   pip install dist/custom_erp_connector-0.24.tar.gz
   ```
   Alternatively, install from GitHub:
   ```bash
   pip install https://github.com/ClearTax/clear-erp-connector/raw/<VERSION>/custom_erp_connector-<VERSION>.tar.gz
   ```
3. Verify installation:
   ```bash
   python3.11 -m pip show custom_erp_connector
   ```

#### Configuration File (`db-config.json`)
##### Example
```json
{
  "dbType": "mysql",
  "connectionDetails": {
    "host": "localhost",
    "database": "your_database_name",
    "user": "your_username",
    "password": "your_password",
    "dbDriver": "driver installation path"
  },
  "env": "sandbox",
  "authToken": "your_auth_token",
  "erpInstanceId": "your_erp_instance_id"
}
```


#### Usage
Run the package with:
```bash
python3.11 -m erp_connector.scheduler db-config.json
```


### 3. ERP Connector - using NSSM (Windows)
- Runs as a background service on Windows VM via NSSM and Fetches data periodically.
- Logs execution and errors.

#### System Requirements
- **OS**: Windows (for VM setup)
- **Python**: 3.9 or higher
- **Dependencies**: `oracledb`, `psycopg2`, `mysql-connector-python`, `APScheduler`

#### VM Setup Steps
1. **Prepare the Windows VM**.
2. **Install Python**:
   ```bash
   "C:\Users\Administrator\AppData\Local\Programs\Python\Python311\python.exe" setup.py install
   ```
3. **Install NSSM**.
4. **Configure `db-config.json`**.
5. **Create a `.bat` file** to run the script.
   - Example
   ```bash
   @echo off
   cd "C:\ClearTax\custom_erp_connector-0.24"
   "C:\Users\Administrator\AppData\Local\Programs\Python\Python311\python.exe" -m erp_connector.scheduler db-config.json
   ```
6. **Set log paths in NSSM**.
   - **Set Log Path in NSSM's I/O Tab**: During the NSSM service setup, navigate to the I/O tab and specify paths for stdout and stderr logs:
     - Output log path: `C:\ClearTax\script_output.log`
     - Error log path: `C:\ClearTax\script_error.log`
7. **Install and verify the service**:
   ```bash
   nssm install clear
   ```
   **In the configuration window**:
     - Specify the path to the .bat file.
     - Set the startup directory where the .bat file is located.
     - Set log paths to capture output and errors.
   **Verify Service Installation**:
   ```bash
   nssm status clear
   ```

#### Error Handling and Debugging
1. Error Logs:
   - The script generates `stderr` logs, capturing errors related to database connectivity, query execution, or script failures.
2. Remote Access for Debugging:
   - Use RDP/SSH to access the VM remotely and check the logs.
   - Logs will be available in the directory specified during NSSM configuration (e.g., `C:\path\to\logs\`).
3. Service Restart:
   - NSSM can be configured to automatically restart the service in case of failure by setting the "Restart" option in the NSSM configuration.

#### Alternative Setup (If Virtual Machine is Not Available)
If a Virtual Machine is not available, the Python script can be deployed using a Kubernetes (K8s) instance. This involves containerizing the Python script in a Docker container and running it in the Kubernetes environment.

Containerize the Python script:
   Create a Docker image and push it to a container registry.
Deploy the container in Kubernetes:
   Deploy the container in a Kubernetes cluster, using a Kubernetes Deployment for continuous running or a Kubernetes CronJob to schedule the periodic execution.
Monitor Logs:
   Logs from the Kubernetes pod/container can be monitored for troubleshooting any issues.
