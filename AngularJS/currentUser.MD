```html
var app = angular.module("myapp", []);
app.controller("currentusercontroller", function ($scope, $http, $filter) {
    $scope.getUsers = function () {
        $http({
            url: _spPageContextInfo.webAbsoluteUrl + "/_api/web/currentuser?$select=ID,Title,LoginName",
            method: "GET",
            headers: { "Accept": "application/json;odata=verbose" }
        }).then(function (result) {
            $scope.usrID = result.data.d.ID;
            $scope.usrName = result.data.d.Title;
            $scope.usrLoginName = result.data.d.LoginName;
            $scope.usrLoginName = $scope.usrLoginName.split('\\')[1];
            $scope.usrLoginName = $scope.usrLoginName.toUpperCase();
            $scope.todayDate = new Date().toDateString();
            $scope.getInDirections(result.data.d.Id);
            $scope.getOutDirections(result.data.d.Id);
        }, function (result, status) {
            alert('Error Occured');
        });
    }
    $scope.getInDirections = function (usrName) {
        $http({
            url: "/_api/web/lists/getByTitle('Direction')/items/?$filter=AssignedMe eq " + usrName,
            method: "GET",
            headers: { "Accept": "application/json;odata=verbose" }
        }).then(function (result) {
            $scope.dirItems = result.data.d.results;
            $scope.dirsArray=[];
            newDir = 0;
            inProgCount = 0;
            overDue = 0;
            nearDue = 0;
            angular.forEach($scope.dirItems,function(value,key){
                if(value.Status == "New" || value.Status=="Re-open")
                {
                    newDir++;// This gives the no.of New Directions
                    $scope.dirsArray.push(value);
                }
                else if(value.Status == "In Progress" || value.Status=="Accepted" || value.Status=="Waiting on someone else" || value.Status=="Time Extension" || value.Status=="Re Assign")
                {
                    inProgCount++;// This gives the no.of InProgress Directions
                    var todayDate = new Date();
                    var dirDueDate = new Date(value.DueDate);
                    if(todayDate > dirDueDate)
                    {
                        overDue++;// This gives the no.of OverDue Directions
                        $scope.dirsArray.push(value);
                    }
                    else{
                        var timediff = Math.abs(todayDate.getTime() - dirDueDate.getTime());
                        var diffDays = Math.ceil(timediff / (1000 * 3600 * 24));
                        if(diffDays <= 7)
                        {
                            nearDue++;// This gives the no.of NearDue Directions
                            $scope.dirsArray.push(value);
                        }
                    }
                }
            });
            console.log(JSON.stringify($scope.dirsArray));
            $scope.newIn = newDir;
            $scope.inProgressIn =inProgCount;
            $scope.nDueIn = nearDue;
            $scope.oDueIn = overDue;
        }, function (result, status) {
            alert('Error Occured');
        });
    }


    $scope.getOutDirections = function (usrID) {
        $http({
            url: "/_api/web/lists/getByTitle('Direction')/items/?$filter=AuthorId eq " + usrID,
            method: "GET",
            headers: { "Accept": "application/json;odata=verbose" }
        }).then(function (result) {
            $scope.dirOutItems = result.data.d.results;
            completedCount = 0;
            timeExtCount = 0;
            reAssignCount = 0;
            rejectCount = 0;
            angular.forEach($scope.dirOutItems,function(value,key){
                if(value.Status == "Completed")
                {
                    completedCount++;// This gives the no.of New Directions
                }
                else if(value.Status == "Time Extension")
                {
                    timeExtCount++;
                }
                else if(value.Status == "Re Assign")
                {
                    reAssignCount++;// This gives the no.of InProgress Directions
                }
                else if(value.Status == "Rejected")
                {
                    rejectCount++;
                }
            });
            $scope.completed = completedCount;
            $scope.timeExtension =timeExtCount;
            $scope.reAssign = reAssignCount;
            $scope.rejected = rejectCount;
        }, function (result, status) {
            alert('Error Occured');
        });
    }
});

```


