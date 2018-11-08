#### Usually the content that we Create/Upload in SharePoint gets stored on SQL Server database (Content DB), this way of storing data is good for a scenario where the data stored is in less in size but for a case where the data stored is larger in size, say a document store, the database size increases rapidly and it is difficult to manage and maintain load on SQL Server.

#### To address this issue we have a concept called RBS (Remote Blob Storage), which separates the actual content like folders, documents, images and videos from SQL server and place them on a FileSystem but still have their references on the SQL Server database. The content that is stored on FileSystem is in encrypted format which is not readable.

#### The following are the key steps in order to enable RBS,

·         Enable FILESTREAM on the database server

·         Provision a BLOB store for each content database

·         Install the RBS client library on each web server

·         Enable RBS for each content database

·         Test the RBS installation

#### Before we begin the operations, we need to consider the following as prerequisites to perform this action

·         The user account to perform these steps must be a member of Administrators Group

·         Should be a db_owner for all the databases for which he is trying to enable RBS.

·         Should have sufficient permissions to run Windows PowerShell.

### Step by step approach to enable RBS on SharePoint 2013.
#### Enable FileStream on the Database server
1.     On the Start menu, point to All Programs, point to SQL Server 2016, point to Configuration Tools, and then click SQL Server Configuration Manager.

2.     In the list of services, right-click SQL Server Services, and then click Open.

3.     In the SQL Server Configuration Manager snap-in, locate the instance of SQL Server on which you want to enable FILESTREAM.

4.     Right-click the instance, and then click Properties.

5.     In the SQL Server Properties dialog box, click the FILESTREAM tab.

6.     Select the Enable FILESTREAM for Transact-SQL access check box.

7.     If you want to read and write FILESTREAM data from Windows, click Enable FILESTREAM for file I/O streaming access. Enter the name of the Windows share in the Windows Share Name box.

8.     If remote clients must access the FILESTREAM data that is stored on this share, select Allow remote clients to have streaming access to FILESTREAM data.

9.     Click Apply.

10.   In SQL Server Management Studio, click New Query to display the Query Editor.

 

 

11.   In Query Editor, enter the following Transact-SQL code:

EXEC sp_configure filestream_access_level, 2

### RECONFIGURE

Click Execute.
Restart the SQL Server service.
Provision a BLOB store for each content database
Confirm that the user account performing these steps is a member of the db_owner fixed database role on each database that you are configuring RBS for.
Click Start, click All Programs, click Microsoft SQL Server 2008, and then click SQL Server Management Studio.
Connect to the instance of SQL Server that hosts the content database.
Expand Databases.
Click the content database for which you want to create a BLOB store, and then click New Query.
Paste the following SQL queries in Query pane, and then execute them in the sequence listed. In each case, replace [WSS_Content] with the content database name, and replace c:\BlobStore with the volume\directory in which you want the BLOB store created. The provisioning process creates a folder in the location that you specify. Be aware that you can provision a BLOB store only one time. If you attempt to provision the same BLOB store multiple times, you'll receive an error.
 

use [WSS_Content]

if not exists

(select * from sys.symmetric_keys

where name = N'##MS_DatabaseMasterKey##')

create master key encryption by password = N'Admin Key Password !2#4'

 

use [WSS_Content]
if not exists 
(select groupname from sysfilegroups 
where groupname=N'RBSFilestreamProvider')
alter database [WSS_Content]
add filegroup RBSFilestreamProvider contains filestream
 

use [WSS_Content] 
alter database [WSS_Content]
 add file (name = RBSFilestreamFile, filename = 
'c:\Blobstore') 
to filegroup RBSFilestreamProvider
 

                In the last query ‘c:\Blobstore’ is the path, where our Remote Blob folder is stored. This can be changed accordingly and it is a best practice to not to store our Blob Store on system drive or a drive with database files/ log files.

 

Install the RBS client library on each web server
 

On the first web server,

Confirm that the user account performing these steps is a member of the Administrators group on the computer where you are installing the library.
On any web server, browse to http://go.microsoft.com/fwlink/p/?LinkId=271938 and download the RBS_amd64.msi file.
Place this file in the user folder, for ex: user account used is SP_Farm then place the in C:\Users\SP_Farm
Click Start, click Run, type cmd into the Run text box, and then click OK.
Copy and paste the following command into the Command Prompt window. Replace WSS_Content with the database name, and replace DBInstanceName with the SQL Server instance name. You should run this command by using the specific database name and SQL Server instance name only one time. The action should finish within approximately one minute.
 
msiexec /qn /lvx* rbs_install_log.txt /i RBS_amd64.msi TRUSTSERVERCERTIFICATE=true FILEGROUP=PRIMARY DBNAME="WSS_Content" DBINSTANCE="DBInstanceName" FILESTREAMFILEGROUP=RBSFilestreamProvider FILESTREAMSTORENAME=FilestreamProvider_1
 

On the remaining Web and App servers,

 

Confirm that the user account performing these steps is a member of the Administrators group on the computer where you are installing the library.
On any web server, browse to http://go.microsoft.com/fwlink/p/?LinkId=271938 and download the RBS_amd64.msi file.
Click Start, click Run, type cmd into the Run text box, and then click OK.
Copy and paste the following command into the Command Prompt window. Replace WSS_Content with the database name, and replace DBInstanceName with the name of the SQL Server instance. The action should finish within approximately one minute.
 
msiexec /qn /lvx* rbs_install_log.txt /i RBS_amd64.msi DBNAME="WSS_Content" DBINSTANCE="DBInstanceName" ADDLOCAL=Client,Docs,Maintainer,ServerScript,FilestreamClient,FilestreamServer
 
Replace WSS_Content with our Database name and DBInstanceName with our SQL Server Instance.
 
After the installation please confirm that the RBS has been installed properly by performing the below check,
 
The rbs_install_log.txt log file is created in the same location as the RBS_amd64.msi file. Open the rbs_install_log.txt log file by using a text editor and scroll toward the bottom of the file. Within the last 20 lines of the end of the file, an entry should read as follows: Product: SQL Remote Blob Storage – Installation completed successfully.
On the computer that is running SQL Server 2008, verify that the RBS tables were created in the content database. Several tables should be listed under the content database that have names that are preceded by the letters "mssqlrbs".
 
### Enable RBS for each content database
 

1.     Start the SharePoint 2013 Management Shell.

·                  For Windows Server 2008 R2:

o    On the Start menu, click All Programs, click Microsoft SharePoint 2013 Products, and then click SharePoint 2013 Management Shell.

·                  For Windows Server 2012:

a.     On the Start screen, click SharePoint 2013 Management Shell.

If SharePoint 2013 Management Shell is not displayed on the Start screen:

b.     Right-click Computer, click All apps, and then click SharePoint 2013 Management Shell.

2.     At the Windows PowerShell command prompt, type the following command:

 

$cdb = Get-SPContentDatabase <ContentDatabaseName>
$rbss = $cdb.RemoteBlobStorageSettings
$rbss.Installed()
$rbss.Enable()
$rbss.SetActiveProviderName($rbss.GetProviderNames()[0])
$rbss
Replace <ContentDatabaseName> with the name of our content database on which we are enabling the RBS.

 

By this step we have finished configuring/Installing RBS, let us confirm this by performing the following Test

Test the RBS installation
1.       On the computer that contains the RBS data store (usually our db server), click Start, and then click Computer.

Browse to the RBS data store directory.
Check the size of this directory.
4.       Go to the SharePoint site and upload a document or file.

5.       Come back to the RBS directory and check the size of the directory. This should have increased size and updated at the time of doc upload.
