### sharepoint search Crawling Error : "Processing this item failed because of an unknown error when trying to parse its contents"

    #### Resolution :
    Make sure that the Search service account has permissions in Local Policies -- User rights assignment
    . Replace a process level token
    . Adjust memory quotas for a process
    . Impersonate a client after authentication

    Source :    
    https://blogs.msdn.microsoft.com/spblog/2013/10/08/crawl-error-sharepoint-2013-processing-this-item-                failed-    because-of-an-unknown-error-when-trying-to-parse-its-contents/

### SharePoint Search Crawler and Indexing : 
Full Crawl after rest index - Successes 0
                                                 Error - 'SharePoint response not found' (may not be the exact error msg, don't remember the exact error)

    Resolution:
    Stop SharePoint Timer Service and SharePoint Search Host-controller service. Delete the folders related to Index under the following path

C:\Program Files\Microsoft Office Servers\15.0\Data\Office Server\Applications\Search\Nodes\[Hex ID\IndexComponentN\storage\data\*

Start the services that we stoped above.

Reset the Index again.

Make a full crawl.

Source : https://blogs.msdn.microsoft.com/kristopherloranger/2014/09/26/sharepoint-2013-on-prem-reset-index-stuck-resolution/
