### Configure Office Web Apps on SharePoint Server

#### Remove Office web app from SharePoint Server:(if any)
Remove-SPWOPIBinding -All:$true

#### Remove any specific Office web app feature from SharePoint Server: (for example 'Excel')
Remove-SPWOPIBinding -Application "Excel"

#### Get all the Office web app features enabled on the current SharePoint farm 
Remove-SPWOPIBinding -All:$true
