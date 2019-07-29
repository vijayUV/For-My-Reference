# A SharePoint Calculate function to find the duration between two time stamps

## My columns InTime and OutTime has values in hh:mm:ss

### Find the difference between the hh values

#### LEFT(OutTime,2)-LEFT(InTime,2)

=CONCATENATE(INT(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))/60),":",MOD(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2)),60))
