
<h1> Implementing a Cloud Data Warehouse in Microsoft Azure Synapse Analytics </h1>

<ul> 
<b> <h3> Summary</h3> </b>

<b> What is a Data Warehouse?  </b>
<li> Data warehousing is a technology that aggregates structured data from one or more sources so that it can be compared and analyzed </li>
<li> It's reasonable to use Data Warehouse when you have historical data that is sitting there, ready for analytics to be applied to it </li>
<li> Rather than to soak in data (like a database), a data warehouse is designed to produce data for analysis. That is, a database is designed to record while a data warehouse is designed to analyize. </li>
<li> For a Data Warehouse the datas purpose is defined (structured and highly transformed). For a Data Lake the datas purpose is not yet determined. </li>
<br>
<b> What is Azure SQL Data Warehouse? </b> 
<br><br>
<li> Azure SQL Data Warehouse is cloud based enterprise Data Warehouse and uses massiveley parallel processing (MPP)</li>
<li> Azure SQL Data Warehouse and Azure Synapse Analytics are used interchangeably (the same thing) </li>
<li> Massively parallel processing can be described as multiple processing nodes (computers) which are interconnected to each other and kept in the same chassi. </li>
<br>
<b> What are best practises for Azure SQL Data Warehouse? </b>
<br><br>
<li> Data warehouses often contain large tables, and require techniques for both managing these large tables and providing good query performance </li>
<li> Data distribution allows the work of a query to be divided into 60 smaller queries that run in parallel.
<li> Data partitioning allows a table, index, or index-organized table to be subdivided into smaller pieces, where each piece of such a database object is called a partition.</li>



---

<h3> Understanding Azure Synapse Analytics </h3>

<p> Synapse Analytics is a combination of: </p>
<ul>
  <li>Azure Data Lake Storage </li> 
  <li> <b> Azure SQL Data Warehouse </b> </li>
  <li>Azure Analytics</li>
</ul>
<br>
<p> However, Azure SQL Data Warehouse and Azure Synapse Analytics are today used interchangeably. Azure SQL Data Warehouse is a cloud based enterprise data Warehouse (EDW) that uses <b> massiveley parallel processing (MPP)</b>. SQL Data Warehouse is an appropriate tool when you need to keep historical data seperate from transactional data. That is, it's reasonable to use Azure SQL Data Warehouse when you have <b>historical data</b> that is sitting there, ready for analytical services. </p>

<b> Understanding massively parallel processing (MPP) </b>

<ul>
<p> Massively parallel processing can be described as multiple processing nodes (computers) which are interconnected to each other and kept in the same chassi. Each computer has its own memory, disk-space, operating system and IO.  </p>

<li> <b> Control Node: </b> The front end that interacts with all applications and connections. The MPP enginge runs on the control node to optimize and coordinate parallel queries.  </li>

<li> <b> Compute Node: </b> Provides the computational power for analytics. The compute nodes are separated from storage nodes and scaled using the data warehouse units (DWU). Computing is any activity that uses computers to manage, process and communicate information.  </li>

<li> <b> Storage Node: </b> The storage nodes are separateed from the compute nodes in order to keep data at rest. Storage is cheaper than data that is being analyzed. </li>

<li> <b> Data Warehouse unit: </b> A collection of analytic resources that are provisioned. This is a combination of CPU, memory and IO (Input/output). These can be scaled up or down to meet needs. Provisioning is the configuration,
deployment and management of IT resources. </li>

<li> <b> Data Movement Service: </b> Data transport technology that coordinates data movement between compute nodes. When SQL Data Warehouse runs a query, the work is divided into 60 smaller queries that run in parallel.  </li>
</ul>

<img src="Images/parallel.JPG" width="700">

<p> At the bottom of the picture is the Azure storage where the data is keept. It is seperated from the compute power (which are more expensive) </p>

<b> Implementing Data Distribution for a SQL Data Warehouse </b> </p> 
<p> When SQL Analytics (in Azure Synapse) runs a query, the work is divided into 60 smaller queries that run in parallel. Each of the 60 smaller queries runs on one of the data distributions. Each Compute Node manages one or more of the 60 distributions. A SQL pool with maximum compute resources has one distribution per Compute Node. A SQL pool with minimum compute resources has all the distributions on one Compute Node.
  </p>

<p> There are three types of distributions.</p>
<ul>
  <li> Replicated Table</li>
  <p>  A replicated table provides the fastest query performance for small tables (full data consistency). Replicating a table removes the need to transfer data among compute nodes before a join or aggregation. Replicated tables are best utilized with small tables. Extra storage is required and there is additional overhead that is incurred when writing data which makes large tables impractical. </p>
  <li> Round Robin</li>
  <p> Round Robin distributes data evenly across the table without additional optimization. Loading data into Round Robin is quick but query performance is better for Hash.  </p> 
  <li> Hash Table </li>
  <p> A hash distributed table can deliver the highest query performance for joins and aggregations on large tables.
To shard data into a hash-distributed table, SQL Analytics uses a hash function to deterministically assign each row to one distribution.</p>
</ul>
  
<img src="Images/table.JPG" width="700">

<b> Implemening Partitions for a SQL Data Warehouse </b>

<p> Table partitions enable you to divide your data into smaller groups of data. In most cases, table partitions are created on a date column. Partitioning can benefit data maintenance and query performance. Improve the efficiency and performance of loading data by the use of partition deletion, switching and merging </p>

<p> For example, a sales fact table might contain just data for the past 36 months. At the end of every month, the oldest month of sales data is deleted from the table (by using a delete statement). However, deleting large amount of data (row-by-row) with a delete statement can take long time, and creates the risk for large rollbacks if something goes wrong. A more optimal approach is to drop the oldest partition of data. Deleting the individual rows could take hours, deleting an entire partition could take seconds.</p>


<p> There are three types of table partitions </p>
<ul>
  <li> Clustered columnstore </li>
  <p> Updateable primary storage method. Great for read-only </p> 
  <li> Clustured index</li>
  <p> An index that is physically stored in the same order as the data being indexed. </p>
  <li> Heap </li>
  <p> Data is not in any particular order. Use when data has no natural order (no time series) </p>
  
</ul>
</p>

<p> Creating a table with to many partitions can hurt performance under some circumstances (10 to a few hundred is resonable). For clustered columnstore tables, it is important to consider how many rows belong to each partions. Keep in mind that before partitions are created, SQL Data Warehouse already divides each table into 60 distributed databases. 
  
  ---
 <h3> Deploying a Data Warehouse in Microsoft Azure Synapse Analytics </h3>
  
  <p> In this exercise, we are going to deploy a data warehouse in Microsoft Azure Synapse Analytics (Formerly SQL Data Warehouse). In the exercise we are going to </p>
<ul>
  <li> Connect to a data warehouse
  <li> Prepare the data warehouse to load data</li>
  <li> Load data into the data warehouse</li>
  <li> Examine configuration settings and tasks in the portal </li>
 </ul>
 </p>
 
   <b> Steps to Create a SQL Data Warehouse in Azure </b>
<ul>
  <li> Create a Resource, choose Databases, Click Azure Synapse Analytics (formerly SQL DW) </li>
  <li> Select subscription and resource group, give the Data Warehouse a name, create an Azure SQL serve, put the Performance level to the lowest, and click create.</li>
  <p> If you have ever built and installed SQL servers from scratch you can appriciate how little effort it takes with Azure. </p>
  <li> Pause the Compute Node so you dont get charged (If you are running a query, it's not a good idea to pause).</li>
  <li> We will still be charged for the storage </li> 
 </ul>
 
   <b> Setting a Firewall Rule and Connecting to an SQL Data Warehouse </b>
<ul>
  <li> Click resume </li>
  <li> Create Firewall rules</li>
  <p> Azure SQL Data Warehouse creates a Firewall at the service level. This prevents external application and tools to connect to the server or any database within the server. In order to connec to our SQL server (that host our Data Warehouse) we need to add a Firewall rule. This rule is going to enable connectivity for one specific ip address or a range of ip addresses. Azure SQL Data Warehouse communicates over port 1.4.3.3. You might have a situation were that port is closed in your Firewall, meaning that you are going to need to open up that port. </p>
  <li> To add Firewall rules start with copying and clicking on the servername, click on Firewall and virtual networks </li>
  <li> Add client IP. Than press save</li>
  <p> Now we have added a Firewall rule, we have an admin with the password and we can connect to the datbase. </p>
  <li> Open Microsoft SQL Server Management Studio, press Object explorer, press Connect, Database enginge, fill in servername, login with authentication details </li>
  <li> When expanding the database and you should see the Data Warehouse and a master database. 
 </ul>
 <br>
  <b> Preparing an SQL Pool in Azure Synapse Analyitcs to Load Data </b>
  <p> In this section;</p>
  <ul>
	<li> We are going to create an login and an user to load data into our warehouse.</li>
	<li> We are going to link into NYC Taxi Source Data.   </li>
	<li>  We are than going to create tables to hold data inside our data warehouse. </li>
</ul>
<br>
   <p> The admin account is not very good for loading massive amount of data. The data takes alot of processing power as it has to compile and compress. It's a good idea to create a login and an user account specificly for loading data. The login is going to be associated with a resource class that is going to allow us a higher maximum amount of memory being used </p>

  <p> Rightclick master database and select new query. </p>
	<pre>CREATE LOGIN marcuslarsson1 WITH PASSWORD = 'Password';
CREATE USER marcuslarsson FOR LOGIN marcuslarsson; </pre> 
<p> Rightclick TestDataWarehous</p>
<pre>CREATE USER marcuslarsson FOR LOGIN marcuslarsson; 
GRANT CONTROL ON DATABASE::[TestDataWareHouse] to marcuslarsson;
EXEC sp_addrolemember 'staticrc20', 'marcuslarsson' </pre> 
  <p> Login to datebase as a new user. Object explorer => Connect => Database enginge =>  </p>
  <p> Create a master key for database. Now we are ready to connect to the external datasource.
    </p>
 <pre>CREATE MASTER_KEY; </pre>	
<pre> CREATE MASTER KEY;
CREATE EXTERNAL DATA SOURCE NYTPublic
WITH
(
	TYPE = Hadoop,
	LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
);  </pre> 
  <p>
 In order for us to load the data into our data warehouse we need to create an external file store statement. This is going to specify the formating charactaristics and the options for our external data file. This statement specifies that the external data is
stored as text. We use a pipe variable in order to separate the values. This file is compressed with gzip.     
 </p>
  
  <pre>CREATE EXTERNAL FILE FORMAT uncompressedcsv
WITH (
	FORMAT_TYPE = DELIMITEDTEXT,
	FORMAT_OPTIONS (
		FIELD_TERMINATOR =',',
		STRING_DELIMITER = '',
		DATE_FORMAT = '',
		USE_TYPE_DEFAULT = False
	)
);
CREATE EXTERNAL FILE FORMAT compressedcsv
WITH (
	FORMAT_TYPE = DELIMITEDTEXT,
	FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
		STRING_DELIMITER = '',
	DATE_FORMAT = '',
		USE_TYPE_DEFAULT = FALSE
	),
	DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
	);
  </pre> 
  <p> Now our external file format is set and we are ready to create the schema. </p>
  <pre>CREATE SCHEMA ext; </pre>

  
  <p> Now we will create several external tables, and these tables will point to the azure blob that we defined previously. </p>
 <pre>CREATE EXTERNAL TABLE [ext].[Date] 
(
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
)
WITH
(
    LOCATION = 'Date',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = uncompressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0
); 
CREATE EXTERNAL TABLE [ext].[Geography]
(
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
)
WITH
(
    LOCATION = 'Geography',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = uncompressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0 
);      
CREATE EXTERNAL TABLE [ext].[HackneyLicense]
(
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
)
WITH
(
    LOCATION = 'HackneyLicense',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = uncompressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0
);
CREATE EXTERNAL TABLE [ext].[Medallion]
(
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
)
WITH
(
    LOCATION = 'Medallion',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = uncompressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0
)
;  
CREATE EXTERNAL TABLE [ext].[Time]
(
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
)
WITH
(
    LOCATION = 'Time',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = uncompressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0
);
CREATE EXTERNAL TABLE [ext].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    LOCATION = 'Trip2013',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = compressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0
);
CREATE EXTERNAL TABLE [ext].[Weather]
(
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
)
WITH
(
    LOCATION = 'Weather',
    DATA_SOURCE = NYTPublic,
    FILE_FORMAT = uncompressedcsv,
    REJECT_TYPE = value,
    REJECT_VALUE = 0
)
;</pre> 
 </ul>
 
 
  <b> Loading NYC Taxi Data into an SQL Pool in Azure Synapse Analytics </b>
  <p> We are now going to load data into the external tables. We are going to view that data being loaded and then examine the details on that loading data </p>
	<p> We are loading this data directly into our final table. This might not be a good idea to do in production because you normally load your data into some kind of staging table in order to transform that data to fit into the tables. The data warehouse is going to import the data into a relational table. It has a distribution type that is Round Robin (standard) . It has en index which is a clustered column store index. </p>
<pre> 
CREATE TABLE [dbo].[Date]
WITH
( 
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
;
CREATE TABLE [dbo].[Geography]
WITH
( 
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT * FROM [ext].[Geography]
OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
;
CREATE TABLE [dbo].[HackneyLicense]
WITH
( 
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[HackneyLicense]
OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
;
CREATE TABLE [dbo].[Medallion]
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Medallion]
OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
;
CREATE TABLE [dbo].[Time]
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Time]
OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
;
CREATE TABLE [dbo].[Weather]
WITH
( 
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Weather]
OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
;
CREATE TABLE [dbo].[Trip]
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Trip]
OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
;
</pre>

<p> Daynamic Management View </p>

<pre>
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
FROM 
    sys.dm_pdw_exec_requests r
    INNER JOIN sys.dm_pdw_dms_external_work s
    ON r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc, 
    gb_processed desc;

</pre>

<p> View System Queries </p> 

<pre>

SELECT * FROM sys.dm_pdw_exec_requests;

</pre>
 </ul>
 
 ---
 
 <b> Examining Configuration Options for an SQL Pool in Azure Synapse Analytics</b>
 <p> In this section we are going to examine configuration options for the data warehouse.  </p>
 
 <ul>
	<li><b> Scale </b> Here we can scale the system (will be more expensive). </li>
	<li> <b> Activity log </b>: Here you find the log files. A log file is a file that records either events that occur in an operating system or other software runs (to query log information use KQL). </li>
	<li> <b> Tags </b>: All resources in Azure can recieve a tag. A tag is a descriptive charactarization that you put on any kind of resource so you can search by category. If you have certain clients that are responsible for paying for certain resources, then that would be an example of the use of a tag.</li>
	<li> <b> Geo-backup policy</b>: Here you see Azures policy for backing up data information. On Gen2 backup is taken care off. </li>
		<li> <b> Connecting strings</b>: Use the connection string if you need to load or connect to data from different applications </li>
	<li> <b> Export template</b>: Alot of resources in Azure are available thru a template. With export templates you can recreate the resources from scratch (using Azure Resource Manager) and have all the configurations from your original resource. </li>
	<li> <b> View Streaming analytics </b>: We can monitor streaming jobs, e.g. a collection of data comming from IoT devices streaming into our data warehouse. </li>
	<li> <b> Load data </b>: Here you find <b> Azure Data Factory </b> which allows you to store and integrate your information into your data warehouse. </li>
	<li> <b> Query editor </b>: We can connect to the database and run queries inside here without using SQL server management studio. </li>
</ul>


<b> Tuning and Optimizing a Data Warehouse in Microsoft Azure Synapse Analytics </b>

<ul>
	<li> Backing up a Data warehouse </li>
	<p> Snapshots offers a time when your database was at a certain level, so you can restore to that level. The backup will consist of many files, since SQL Data Warehouse is distributed (exists in alot of different places). Automatic restore points are taken several times a day <b> assuming your data warehouse is not paused </b>. Overview => New Restore Point (to create your own restore point). </p>
	<li> Restoring a Data warehouse</li> 
	<p>Restor points are deleted after 7 days and are not be available when DW is paused. To restore go to "Over" (next to scale) </p>
	<li> Managing cost of a Data warehouse </li>
	<p> The pricing page is available at Microsoft website. The cost depends on how the storage cost and the computing cost. </p>
	<li> Workload management </li>
	<p> Workload management includes the process of loading data, running analysis, reporting, exporting and managing the data in the data warehouse. Workload classification includes when you assign users to a role that has a corresponding resource class. We do this with <pre> CREATE WORKLOAD CLASSIFIER </pre></p> 
	<p> Workload importance: low, below_normal, normal, above_normal, high. A request with higher importance will be run before a request with lower importance. </p>
 
  ---
<h3> Notes </h3>

 
 <b> Resource Group </b>
 <p> Resource groups (RG) in Azure is an approach to group a collection of assets in logical groups for automatic provisioning, monitoring, and access control, and for more effective management of their costs. One benefit of using RGs in Azure is grouping related resources that belong to an application together, as they share an unified lifecycle from creation to usage and finally, de-provisioning. The underlying technology that powers resource groups is the Azure Resource Manager (ARM). ARM was built by Microsoft in response to the shortcomings of the old Azure Service Manager (ASM) technology that powered the old Azure management portal. </p> 

<b> Database vs Data warehouse </b>
<p> Database is a structured place to store data. A data warehouse is a form of database. Rather than to soak in data, a data warehouse is designed to produce data for analysis. That is, a database is designed to record while a data warehouse is designed to analyize. A data warehouse is usually normalized etc. </p>

<b> Computing  </b>
<p> Computing is any activity that uses computers to manage, process and communicate information. </p>

<b> Processing</b>
<p> Processing is the actual execution of instructions or the instance. </p>

<b> Cache </b>
<p> CPU Cache: The CPU Cache is the CPUs internal memory. It's job is to store copy of data from RAM which is waiting to be used by the CPU. Basically what the CPU Cache does, is that it holds common data that it thinks the CPU will access. CPU always check cache memory first. A computer have two different types of memory. Dynamic RAM and SRAM (used in CPU Cache). SRAM does not have to be constantly refreshed. Much faster than DRAM but more expensive. 

Browser Cache: A way to make website faster for you when your browsing the internet. When you visit the website, it basically downloads a copy of the website and stores it on your harddrive. Next time you load website it goes really fast. 
 </p>


<b> Ports & IP Addressing </b>
<p> Together IP and port number is called the socket. IP adress are used to identify what computer we are sending to or from and the port represents what application or service we are sending to and from. If you are using windows open command and type netstat -a -b -n (with admin privelage) to find port numbers. Common port numbers: HTTP 80, FTP (File transfer) 20 </p> 

<b> Firewall </b>
<p> A Firewall is a system that is designed to prevent unauthorized access to enter a private network by filtering information that comes in from the internet. A Firewall blocks unwanted traffic and permits wanted traffic. Firewall => Router => Computer. A Firewall works by filtering the incomming network data and determines by its rules if its allowed to enter a network. These rules are customizable and determined by the network administrator. Generally Firewalls allow data to go out.  </p>

<b> SQL Server Management Studio(SSMS)</b>
<p> Integrated environment for managing any SQL infrastructure, from SQL Server to Azure SQL Database </p>


<b> System databases in SQL Server </b>
<p>
<ul>
  <li> <b> master </b> keeps and manages all system level information. Such as system configuration, logins, linked servers, credentials etc. Take master data backup because if it becomes corrupted/deleted you won't be able to start SQL server. </li>
  <li> <b> model </b> The model database acts as a template for all the databases. The template is used in the creation of any new user-database created in this instance (store procedures, database configurations etc) </li>
  <li> <b> msdb </b> SQL Server agent information is managed by msdb. Will hold backup, log_shipping. </li>
  <li> <b> tempdb </b> holds temporary data, tables like locally temporary tables.</li>
  <li> <b> resource </b> not accessable by the user </li>
  <li> <b> distribution </b> not accessable by the user </li>
</ul>
</p>

<b> Collation</b>
<p> It's important to pick the right collation when you are installing SQL server. When you store something in a database, you use characters (abc$*). What a collation does, it decides how to organize these variables (ABCabc123). An example of a collation is UTF8_unicode_ci (case insensitive). ASCII (American Standard Code for Interchange, American standard). </p>
