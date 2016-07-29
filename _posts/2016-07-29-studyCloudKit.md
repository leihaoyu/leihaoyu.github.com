---
layout: post
title: "学习CloudKit"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## 总结一下自己学了CloudKit的理解


### 1.什么是CloudKit

有了CloudKit我们可以更方便的使用iCloud服务，苹果自带的相片等应用就是通过CloudKit这个框架链接iCloud服务的。

### 2. 怎么开启ClouKit

1. 加入苹果开发者计划（就是付费）
2. 工程里面的Capabilities开启CloudKit选项

### 3. CloudKit名词解释

1. Container 类似手机上的沙盒机制，苹果给每个app链接一个独立的容器。
2. Database 每个Container里面分为PublicDatabase公共数据库和PrivateDatabase私有数据库。公共数据库里面的内容每个用户都可以访问，私人的只有对应的iCloud账户能访问。
3. Record 对应每一个储存的数据，可以存储各类型数据。NSString、CLLocation、NSDate等，如果要存储图片等大型二进制数据用CKAsset，还可以存储另外一个Record的引用CKReference。
4. RecordZone 一个database里面可以包含多个RecordZone，一般默认用的DefaultZone。


### 4. CloudDit简单使用（增删改查）。

#### 增

        //To create a record in code

        //Create a record ID specifying a unique record name.
        CKRecordID *artworkRecordID = [[CKRecordID alloc] initWithRecordName:@"115"];
        Create a record object.
        CKRecord *artworkRecord = [[CKRecord alloc] initWithRecordType:@"Artwork" recordID:artworkRecordID];

        //Set the record’s fields.
        artworkRecord[@"title" ] = @"MacKerricher State Park";
        artworkRecord[@"artist"] = @"Mei Chen";
        artworkRecord[@"address"] = @"Fort Bragg, CA";

        //Get the database in your app’s default container.

        //To get the public database:

        CKContainer *myContainer = [CKContainer defaultContainer];
        CKDatabase *publicDatabase = [myContainer publicCloudDatabase];
        //To get the private database:
        CKContainer *myContainer = [CKContainer defaultContainer];
        CKDatabase *privateDatabase = [myContainer privateCloudDatabase];
        //To get a custom container:
        CKContainer *myContainer = [CKContainer containerWithIdentifier:@"iCloud.com.example.ajohnson.GalleryShared"];

        //To create a custom container shared by multiple apps, read Share Containers Between Apps.

        //Save the record.
        [publicDatabase saveRecord:artworkRecord completionHandler:^(CKRecord *artworkRecord, NSError *error){
            if (!error) {
                // Insert successfully saved record code
            }
            else {
                // Insert error handling
            }
        }];

#### 对单个record进行查找修改

        // Fetch the record from the database
        CKDatabase *publicDatabase = [[CKContainer defaultContainer] publicCloudDatabase];
        CKRecordID *artworkRecordID = [[CKRecordID alloc] initWithRecordName:@"115"];
        [publicDatabase fetchRecordWithID:artworkRecordID completionHandler:^(CKRecord *artworkRecord, NSError *error) {
            if (error) {
                // Error handling for failed fetch from public database
            }
            else {
                // Modify the record and save it to the database
                NSDate *date = artworkRecord[@"date"];
                artworkRecord[@"date"] = [date dateByAddingTimeInterval:30.0 * 60.0];
                [publicDatabase saveRecord:artworkRecord completionHandler:^(CKRecord *savedRecord, NSError *saveError) {
                    // Error handling for failed save to public database
                }];
            }
        }];

#### 使用Predicate进行查找

        CKDatabase *publicDatabase = [[CKContainer defaultContainer] publicCloudDatabase];
        NSPredicate *predicate = [NSPredicate predicateWithFormat:@"title = %@", @"Santa Cruz Mountains"];
        CKQuery *query = [[CKQuery alloc] initWithRecordType:@"Artwork" predicate:predicate];
        [publicDatabase performQuery:query inZoneWithID:nil completionHandler:^(NSArray *results, NSError *error) {
            if (error) {
                // Error handling for failed fetch from public database
            }
            else {
                // Display the fetched records
            }
        }];


#### 用CKAsset保存图片

        // Create a URL to the local file
        NSURL *resourceURL = [NSURL fileURLWithPath:@"…"];
        if (resourceURL){
            CKAsset *asset = [[CKAsset alloc] initWithFileURL:resourceURL];
            artworkRecord[@"image"] = asset;
        }


#### 使用Operation进行操作。

CKModifyRecordsOperation 可以用于新增

CKFetchRecordsOperation 可以用于查找


1. Add all the record IDs for the records you want to fetch to an array.
For example, to fetch multiple one-to-one relationships, add all the target record IDs of the reference fields to an array.

2. Create the fetch record operation object by passing the array of record IDs as a parameter.

        CKFetchRecordsOperation *fetchRecordsOperation = [[CKFetchRecordsOperation alloc] initWithRecordIDs:fetchRecordIDs];

3. Optionally, provide a per record completion handler.
If you want to save the data from successful individual records, provide a per record completion handler. The completion handler should create a model object for a successfully fetched record, and because the operation may fail, should keep the record ID of a failed fetch.

        fetchRecordsOperation.perRecordCompletionBlock = ^(CKRecord *record, CKRecordID *recordID, NSError *error) {
            if (error) {
            // Retain the record IDs for failed fetches
            }
            else {
            // Create a model object and set any relationships to other models
            }
        };

4. Set the completion handler for the entire operation.

        fetchRecordsOperation.fetchRecordsCompletionBlock = ^(NSDictionary *recordsByRecordID, NSError *error) {
            if (error) {
                // Failed to fetch all or some of the records
            }
            else {
                // Update all associated views
            }
        };

    If you save the record IDs in the per record completion handler in step 3, you can attempt to fetch failed records again.

5. Start the operation.

        fetchRecordsOperation.database = [[CKContainer defaultContainer] publicCloudDatabase];
        [fetchRecordsOperation start];


#### 删除

1. 单个删除

        [publicDatabase deleteRecordWithID:recordID completionHandler:^(CKRecordID *recordID, NSError *error) {

        }];

2. 批量删除

        // we use CKModifyRecordsOperation to delete multiple records
        CKModifyRecordsOperation *operation = [[CKModifyRecordsOperation alloc] initWithRecordsToSave:nil recordIDsToDelete:recordIDs];
        operation.savePolicy = CKRecordSaveIfServerRecordUnchanged;
        operation.queuePriority = NSOperationQueuePriorityHigh;

        // The following Quality of Service (QoS) is used to indicate to the system the nature and importance of this work.
        // Higher QoS classes receive more resources than lower ones during resource contention.
        //
        operation.qualityOfService = NSQualityOfServiceUserInitiated;

        // add the completion for the entire delete operation
        operation.modifyRecordsCompletionBlock = ^(NSArray *savedRecords, NSArray *deletedRecordIDs, NSError *error) {



        // back on the main queue, call our completion handler

        };

        // start the operation


### 5. 还有一个比较重要的功能就是订阅功能，简单的类型如果有新内容，发送一个推送给我，让我实时更新关注的东西。


For iOS and tvOS apps, add this code to the application:didFinishLaunchingWithOptions: protocol method to register for push notifications:

        // Register for push notifications
        UIUserNotificationSettings *notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert categories:nil];
        [application registerUserNotificationSettings:notificationSettings];
        [application registerForRemoteNotifications];


1. Add the application:didReceiveRemoteNotification: protocol method to the app’s delegate.

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
        }

2. In the application:didReceiveRemoteNotification: method, convert the userInfo parameter to aCKNotification object.

        CKNotification *cloudKitNotification = [CKNotification notificationFromRemoteNotificationDictionary:userInfo];

3. Get the body of the notification.

        NSString *alertBody = cloudKitNotification.alertBody;

4. Get the new or modified record from the CKQueryNotification object.

        if (cloudKitNotification.notificationType == CKNotificationTypeQuery) {
            CKRecordID *recordID = [(CKQueryNotification *)cloudKitNotification recordID];
        }

5. Update views or notify the user according to the record changes.


### 6. iCloud的管理是直接和iCloud的用户挂钩的，所以如果用户切换了帐号，必须对数据重现加载。

在appdelegate订阅这个可以在切好帐号的时候得到通知

        [[NSNotificationCenter defaultCenter] addObserver:self
        selector:@selector(iCloudAccountAvailabilityChanged:)
        name:NSUbiquityIdentityDidChangeNotification
        object:nil];


可以用userDiscover获取通讯录里面使用了这个app的好友，首先获取授权

        [self.container requestApplicationPermission:CKApplicationPermissionUserDiscoverability
        completionHandler:^(CKApplicationPermissionStatus applicationPermissionStatus, NSError *error) {

            // back on the main queue, call our completion handler
        }];


然后在回调里面查找好友

        [self.container discoverUserInfoWithUserRecordID:recordID completionHandler:^(CKDiscoveredUserInfo *user, NSError *error) {
            if (error != nil)
            {
            }
            else
            {
                _userInfo = user;
                //NSLog(@"logged in as '%@ %@'", self.userInfo.firstName, self.userInfo.lastName);
            }

        }];

### 7. 还有一些注意事项

1. DashBoard的使用，需要知道开发和发布的区别和关系，还要知道怎么部署到发布环境。


