## Re-Create a SharePoint site with RBS enabled Content DB
Step 1: Take content db back up by using the following queries

Use master
GO
BACKUP DATABASE WSS_Content_FileShare
TO DISK =N'\\1.1.1.1\Fileshare_backup\FileShareDB.BAK'
WITH COMPRESSION
GO

/* Perform a Tail Log Backup of FileStreamDB */

BACKUP LOG WSS_Content_FileShare
TO DISK =N'\\1.1.1.1\Fileshare_backup\FileShareDB.TRN'
WITH COMPRESSION, NORECOVERY
GO

Step 2: Restore the content db on the new db server

/* Identify the list of the database & log files contained within the backup set */
Use master
GO
RESTORE FILELISTONLY 
FROM DISK = N'F:\Fileshare_backup\FileShareDB.BAK'
GO

/* Restore Full Backup with MOVE & NORECOVERY */
RESTORE DATABASE FileStreamDB
FROM DISK = N'G:\Fileshare_backup\FileShareDB.BAK'
WITH 
MOVE 'WSS_Content_FileShare' to 'F:\Fileshare\WSS_Content_FileShare.mdf',
MOVE 'WSS_Content_FileShare_log' to 'F:\Fileshare\WSS_Content_FileShare_log.ldf',
MOVE 'RBSFilestreamFile' to 'F:\Fileshare\RBSFilestreamFile',
NORECOVERY, FILE =1
GO


Check the collation.
