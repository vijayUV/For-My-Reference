## Import/Export
#### Export a SubSsite :
stsadm -o export -url Source subsite url -filename “name and path for cmp file to generate” -includeusersecurity

Example:
stsadm -o export -url http://abcd.com/en/subsite/ -filename “c:backup” -includeusersecurity

#### Import a SubSite :
Import from a .cmp file

stsadm -o import -url Destination subsite url -filename “D:\Vijay\backup.cmp” -includeusersecurity

Example:
stsadm -o import -url https://xyz.com/en/subsite/ -filename “D:\Vijay\Current\backup.cmp” -includeusersecurity

#### Export a List :
Export-SPWeb -identity http://abcd.com/en/subsite/ -path D:\Vijay\SomeList.cmp -itemurl "/en/subsite/Lists/Listname"
keynote : itemurl should be in double quotes, listname should be picked from the url.

#### Import a List :
Import-SPWeb -identity http://xyz.com/en/subsite/ -path D:\Vijay\SomeList.cmp -updateversion ignore
