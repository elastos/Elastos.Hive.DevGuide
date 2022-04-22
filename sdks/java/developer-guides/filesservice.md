# FilesService

Hive SDK uploads files to the corresponding Vault through the FilesService class. File is one of the data types supported by Hive SDK. The FileService class is one of the derived sub-services in Vault Service, which is used to support the operation of file types, such as uploading, downloading, and deleting. Once the data is uploaded to Hive Node, its file block data is hosted and saved in the corresponding IPFS Node, while its metadata information is hosted in the Vault internal database.

## Upload File

### Upload File Data by Writing File Stream Interface Writer

When uploading a file, you need to get the FilesService interface instance from the Vault instance, and then set the target path (REMOTE\_FILE\_PATH) to get the remote write file stream interface instance Writer. At the same time, you'll need to write the file content into the file stream. The process of writing file data is that of uploading file data.

The whole process of uploading files using FileService instance is connected in series through the CompletableFuture mode, and a CompletableFuture object is returned that indicates whether the file upload succeeds or fails and an exception occurs.

Here's an example of uploading files by using the write file stream interface:

```java
public CompletableFuture<Void> writeFileContent(Writer writer) {
    return CompletableFuture.runAsync(() -> {
        try {
            writer.write(REMOTE_FILE_CONTENT);
            writer.flush();
            writer.close();
        } catch (IOException e) {
            throw new CompletionException(e);
        }
    });
}

Vault vault = new Vault(context, getVaultProviderAddress());
FilesService filesService = vault.getFilesService();
filesService.getUploadWriter(YOUR_REMOTE_PATH)
    .thenCompose(this::writeFileContent)
    .thenAcceptAsync(result -> {
        System.out.println("Upload a file using writer.");
    }).exceptionally(ex -> {
        System.out.println("Failed to upload a file");
        ex.printStackTrace();
        return null;
    }
);
```

### Upload File by the Output Stream Method (OutputStream)

When uploading a file, you can also set the directory path (REMOTE\_FILE\_PATH) through the FileService interface instance to obtain the remote output stream OutputStream, and write the file content into the input stream OutputStream instance.

The whole process of uploading files of using FileService instance is connected in series through the CompletableFuture mode, and a CompletableFuture object is returned, which indicates whether the file upload succeeds or fails and an exception occurs.

Here's an example of uploading files by using the output stream interface:

```java
public CompletableFuture<Void> writeFileContent(OutputStream out) {
    return CompletableFuture.runAsync(() -> {
        try {
            out.write(REMOTE_FILE_CONTENT.getBytes());
            out.flush();
            out.close();
        } catch (IOException e) {
            throw new CompletionException(e);
        }
    });
}

Vault vault = new Vault(context, getVaultProviderAddress());
FilesService filesService = vault.getFilesService();
filesService.getUploadStream(YOUR_REMOTE_PATH)
.thenCompose(this::writeFileContent)
.thenAcceptAsync(result -> {
    System.out.println("Upload a file using OutputStream.");
}).exceptionally(ex -> {
    System.out.println("Failed to upload a file");
    ex.printStackTrace();
    return null;
});
```

## Download File

Similar to uploading files, when downloading them you can get the Reader or InputStream instance through the FileSerice instance, and then download the file data in the Vault based on the Reader instance or InputStream instance.

```java
Vault vault = new Vault(context, getVaultProviderAddress());
FilesService filesService = vault.getFilesService();

filesService.getDownloadStream(YOUR_REMOTE_PATH)
.thenCompose(this::readFileContent)
.thenAcceptAsync(result -> {
    System.out.println("download the file by input stream successfully");
}).exceptionally(ex -> {
    System.out.println("failed to download the file by input stream");
    ex.printStackTrace();
    return null;
});

filesService.getDownloadReader(YOUR_REMOTE_PATH)
.thenCompose(this::readFileContent)
.thenAcceptAsync(result -> {
    System.out.println("download the file by input stream successfully");
}).exceptionally(ex -> {
    System.out.println("failed to download the file by input stream");
    ex.printStackTrace();
    return null;
});
```

## List Files

List the files in the file directory. FileInfo is single file information, and path is the file directory:

```java
filesService.list(YOUR_REMOTE_PATH)
.thenAcceptAsync(list -> {
    System.out.println("list folder files successfully");
    for (FileInfo file in list) {
        System.out.println("file name => " + file.getName());
        System.out.println("file size => " + file.getSize());
    }
}).exceptionally(ex -> {
    System.out.println("failed to list folder files");
    ex.printStackTrace();
    return null;
});
```

## Stat

Retrieve the information of a single file:

```java
filesService.stat(YOUR_REMOTE_PATH)
.thenAcceptAsync(fileInfo -> {
    System.out.println("get the state of the file successfully");
    System.out.println("file name => " + fileInfo.getName());
    System.out.println("file size => " + fileInfo.getSize());
}).exceptionally(ex -> {
    System.out.println("failed to get the state of the file");
    ex.printStackTrace();
    return null;
});
```

## Move

Move a single file from source to target:

```java
filesService.move(YOUR_REMOTE_PATH, YOUR_REMOTE_NEW_PATH)
.thenAcceptAsync(result -> {
    System.out.println("Moved the file to new path in success.");
}).exceptionally(ex -> {
    System.out.println("failed to move the file");
    ex.printStackTrace();
    return null;
});
```

## Delete

Delete the file; the file is in the path:

```java
filesService.delete(YOUR_REMOTE_PATH)
.thenAcceptAsync(result -> {
    System.out.println("delete the file successfully");
}).exceptionally(ex -> {
    System.out.println("failed to delete of the file");
    ex.printStackTrace();
    return null;
});
```
