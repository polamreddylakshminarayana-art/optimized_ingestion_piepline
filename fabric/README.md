# SQL Server to Lakehouse Pipeline

## 📋 Overview
Metadata-driven pipeline that loads data from SQL Server to Fabric Lakehouse with configurable strategies and comprehensive logging.

## 🎯 What It Does
Automatically loads tables from SQL Server to Lakehouse based on metadata configuration. Supports **full** and **incremental** loading strategies.

## 🔄 Pipeline Flow
1. **📊 Get Metadata** - Reads configuration from `source_config.etrac_keters`
2. **✅ Filter Active Objects** - Processes only `is_active = 1`
3. **🔍 Check Table Existence** - Uses Fabric Notebook to check if target tables exist
4. **⚡ Smart Loading** - For each table:
   - **If table doesn't exist OR strategy = Full** → Full load
   - **If table exists AND strategy = Incremental** → Load only new/changed records
5. **📝 Log Everything** - Detailed logging to `logs.raw_ingestion_logger`

## ⚙️ Configuration (Metadata.csv)
| Column | Description | Example |
|--------|-------------|---------|
| `object_id` | Unique ID | `550e8400-e29b-41d4-a716-446655440000` |
| `source_name` | Source system name | `SQL Server` |
| `source_db_name` | Database name | `eTracDB` |
| `source_schema_name` | Schema name | `dbo` |
| `source_table_name` | Table name | `Customers` |
| `source_extract_strategy` | `Full` or `Incremental` | `Incremental` |
| `is_active` | `1` = enabled | `1` |
| `sink_schema_name` | Target schema | `raw` |
| `sink_object_name` | Target table | `dim_customers` |

## 🔌 Connection Setup in Fabric
1. **Navigate to**: Fabric Workspace → Data Engineering
2. **Click**: "+ New" → "SQL Server Connection"
3. **Fill in**:
   - **Server**: `your-server.database.windows.net` (Azure) or `server-name\instance` (on-prem)
   - **Database**: Your database name
   - **Authentication**: SQL authentication
   - **Username**: Your SQL login
   - **Password**: Your password
4. **For on-prem SQL Server**:
   - Install **On-premises Data Gateway** on a server
   - During connection setup, select "Connect via gateway"
   - Choose your installed gateway

## 🛠️ Supported Tools
- ✅ **Fabric** - Native integration
- ✅ **ADF** - Azure Data Factory

## 📤 Supported Destinations
- ✅ **Fabric Lakehouse** - Primary target
- ⚠️ **Requires customization** for other destinations

## ⚠️ Known Limitations
- **Dynamic IP Issue**: Fabric uses dynamic outbound IPs, which can cause SQL Server firewall issues
- **Incremental Requires**: `source_created_date_column` and `source_modified_date_column` must be defined
- **Table Detection**: Relies on Fabric Notebook for table existence checks

## 📊 Logging Details
All loads are logged to `logs.raw_ingestion_logger` with:
- Source name, table name
- Row count loaded
- Load type (Full/Incremental)
- Status (Success/Failed)
- Error messages (if any)
- Duration and timestamps

## 💡 Key Benefits
- **Metadata-Driven**: Add tables by updating config, not code
- **Smart Strategy**: Automatically chooses full vs incremental
- **Parallel Loading**: 8 tables at once for performance
- **Audit Trail**: Complete visibility into all data movements
- **Error Resilient**: Individual table failures don't stop pipeline

## 🔧 Usage
1. **Configure** - Populate metadata table with source/target info
2. **Run** - Execute pipeline (scheduled or manual)
3. **Monitor** - Check logs for success/failure status
4. **Troubleshoot** - Use error messages from logs to fix issues

The pipeline handles the complexity - you just configure what to move where.
