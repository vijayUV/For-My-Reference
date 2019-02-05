# Fix To: "We've hit a Snag" SharePoint 2013 Newsfeed Error
## Who should try this solution
### There are several reasons for this error but this is one pain in the Ass
#### Before you proceed with this solution, make sure that your issue falls in this category,
-	Check whether the User Profile Service is up and running
-	Check whether the App fabric is up and running
-	Check whether the distributed cache is up and running
-	Make sure your MySite is accessible 
-	Make sure that the User Profile Service App Pool account has full access on the User Profile Service Application (can check it in the manage service applications section - permissions)
-	Make sure the Activity Feed Timer is running successfully.
#### If all the above is in place and you still face the Issue, you may need my solution, let’s give one final check
Deactivate the Site Feed feature from the manage site features, and activate it again.
Create a sub site after the above step and test the News Feed on the new subsite, if it works….!!
## Welcome this solution is for you

This is an issue with the “HashTags” site column on the root sitecollection and has to be addressed on the root site collection.
-	Create a new root site collection, this will have a healthy “HashTags” site column and we are going to add this to our site collection with issue.  
-	Go to our root site collection where we have the news feed issue, navigate to site columns on the site settings page.
-	We need to delete the “HashTags” column. ( you can use the following powershell)

``` $w2=get-spweb Get-SPWeb https:// your root site collection with issue
$w2.fields.Delete("HashTags")   
$w2.Update()  ```

Now we shall add the field from our new healthy site collection to the root site using AddFieldAsXml method

```
$workingsite = Get-SPWeb https:// Your New healthy root sitecollection   	    
$fieldId=$workingsite.Fields["HashTags"]        
$fieldIdschema=$ fieldId.SchemaXml    	    
$errorsite = Get-SPWeb https:// Your Problamatic root site collection    
$newfld=$errorsite.Fields.AddFieldAsXml($fldschema)  

```

You may receive a warning regarding the version if you run this multiple time.. you can ignore this warning.

Now go to the Micro feed list and add the "HashTags" column from the existing site columns.

Baaang..!! 
Your News Feed should start working

Hope this will be usefull.
Cheers!!
