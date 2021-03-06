# StreamStorage.AliyunOssStorage for .Net
Implement StreamStorage for Aliyun OSS.

## StreamStorage.ini
	[stream_storage]
	storageType=aliyun.oss

	[aliyun.oss]
	endpoint=http://oss-cn-hangzhou-internal.aliyuncs.com
	accessKeyId=XXX
	accessKeySecret=XXX
	bucketName=bucket1-d8b98e0a
	optCountQuotaPerDay=10000
	objectMetadata_CacheControl=public,max-age=86400
	__class=StreamStorage.AliyunOssStorage, StreamStorage.AliyunOssStorage.AliyunOssStorageProvider

	[localfs]
	rootFolder=c:\Folder1
	__class=StreamStorage, StreamStorage.LocalFSStorageProvider

## Usage in csharp code
    var provider = StreamStorage.StreamStorageServiceFactory.Create().Provider;
    // define object name
    string objectName = "client_1/" + System.Guid.NewGuid().ToString("D") + ".txt";

    // check object is exists or not
    bool objExists = provider.ObjectExists(objectName);

    // save object (if exists, then override it)
    using (System.IO.Stream ms = new System.IO.MemoryStream())
    {
        byte[] buffer = System.Text.Encoding.UTF8.GetBytes("Hello, my friend!");
        ms.Write(buffer, 0, buffer.Length);
        ms.Position = 0;
        provider.PutObject(objectName, ms, true);
        ms.Close();
    }

    // save object (if exists, then override it), fill with metadata
    StreamStorage.ObjectMetadata metadata = new StreamStorage.ObjectMetadata metadata();
    metadata.ContentDisposition = "attachment;filename=FriendlyFileName.txt";
    using (System.IO.Stream ms = new System.IO.MemoryStream())
    {
        byte[] buffer = System.Text.Encoding.UTF8.GetBytes("Hello, my friend!");
        ms.Write(buffer, 0, buffer.Length);
        ms.Position = 0;
        provider.PutObject(objectName, ms, true, metadata);
        ms.Close();
    }

    // get object
    using (var obj = provider.GetObject(objectName))
    {
        var content = obj.Content;
        var currentMetaData = obj.ObjectMetadata;
        // do it here
    }

    // get object metadata
    metadata = provider.GetObjectMetadata(objectName);

    // delete object
    provider.DeleteObject(objectName);

    // delete it and sub objects behind that.
    provider.DeleteObject("client_1");