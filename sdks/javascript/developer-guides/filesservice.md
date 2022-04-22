# FilesService

Hive SDK uploads files to the corresponding Vault through the FilesService class. File is one of the data types supported by Hive SDK. The FileService class is one of the derived sub-services in Vault Service, which is used to support the operation of file types, such as uploading, downloading, and deleting. Once the data is uploaded to Hive Node, its file block data is hosted and saved in the corresponding IPFS Node, while its metadata information is hosted in the Vault internal database.

## Upload File

### Upload File Data by Writing File Stream Interface Writer

When uploading a file, you need to get the FilesService interface instance from the VaultServices instance, and then set the target path (REMOTE\_FILE\_PATH) and the file content.

Here's an example of uploading files by using the upload interface:

```javascript
const vaultServices = new VaultServices(context, getProviderAddress());
const filesService = vaultServices.getFilesService();

const file = new File(YOUR_LOCAL_PATH);
await filesService.upload(YOUR_REMOTE_PATH, file.read());
```

## Download File

Similar to uploading files, when downloading file you've uploaded through the FilesService instance, directly call download method with the remote file path.

```javascript
const vaultServices = new VaultServices(context, getProviderAddress());
const filesService = vaultServices.getFilesService();

const data: Buffer = await filesService.download(YOUR_REMOTE_PATH);
```

## List Files

List the files in the file directory. FileInfo is single file information, and path is the file directory:

```javascript
const fileInfos = await filesService.list(YOUR_REMOTE_PATH);
```

## Stat

Retrieve the information of a single file:

```javascript
const fileInfo = await filesService.stat(YOUR_REMOTE_PATH);
```

## Move

Move a single file from source to target:

```javascript
const newPath = await filesService.move(YOUR_REMOTE_PATH, YOUR_REMOTE_NEW_PATH);
```

## Delete

Delete the file; the file is in the path:

```javascript
await filesService.delete(YOUR_REMOTE_PATH);
```
