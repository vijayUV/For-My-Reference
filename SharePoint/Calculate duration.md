# A SharePoint Calculate function to find the duration between two time stamps

## My columns InTime and OutTime has values in hh:mm:ss

### Find the difference between the hh values

#### LEFT(OutTime,2)-LEFT(InTime,2)

### Convert the hh difference into minutes

#### SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60

### Find the difference between the mm values

#### MID(OutTime,4,2)-MID(InTime,4,2)

### Now we have all the values in minutes, sum up both values

#### SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))

### Now convert the obtained sum of the minutes into HRS by dividing the value with 60

#### SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))/60

### We get a decimal value, take the interger part of it,

#### INT(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))/60)

### We have the total durtion in Hrs, to get the Mins part of it, get the MOD of the total minutes with 60

#### MOD(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2)),60)

### Concatenate both Hrs and Mins with
####CONCATENATE(INT(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))/60),":",MOD(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2)),60))

## The End formula should look like this
### =CONCATENATE(INT(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))/60),":",MOD(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2)),60))

## Sample Result for InTime -  08:43 and OutTime - 16:50 is
###  08:07




=CONCATENATE(INT(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2))/60),":",MOD(SUM(SUM(LEFT(OutTime,2)-LEFT(InTime,2))*60,MID(OutTime,4,2)-MID(InTime,4,2)),60))
