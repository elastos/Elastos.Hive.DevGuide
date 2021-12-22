# Coding App

本章通过实现最基础的文件数据上传功能来指导如何开发使用 Hive Java SDK（本章内简称 SDK），并通过关键代码段展示在应用中对于相应接口调用和对象维护，

## 准备 DID

开发者应该需要了解到，在向后端存储 Vault 数据时，会涉及到四个DID：
* 应用程序 DID
* 应用程序实例 DID
* 用户 DID
* 后端Hive Node服务 DID

其中，应用程序 DID 由开发人员通过在 Essentials 为该应用程序生成一个DID，并需要将该 DID 公开上链，编译后续该应用程序需求向Essentials 授权时验证其应用程序的合规性。而应用程序实例DID 表示应此应用程序运行在Android 或者 iOS 设备上一个独一无二应用实例。该应用实例 DID 需要开发人员在应用程序内安装时一次生成，但不需要公开上链，仅在应用程序作为用户的 Agent 在登录 Hive Node服务时作登入时验证时使用。

每个使用应用程序具有唯一的DID身份，用户需要在 Essentials 中生成该 DID，同时需要公开上链。完成上链后保管在 Essentials 中，用户后续应用登录时授权使用。后端 Hive Node 服务 DID，跟应用程序实例 DID类似，也是唯一代表该Hive 服务的实例 DID，但是需要公开上链供前端应用交互时双向验证使用。

用于通过在 Essentials 中授权使用指定 DID 身份登录应用程序。在向 后端 Hive Node 请求登录时，需要通过 Essnetials 授权向应用程序实例（实例DID）颁发一个请求登录的凭证。Hive Node 通过验证该登录凭证的真实性，才授权向前端应用发布一个 access token，后续应用通过该 access token来存储和访问 Vault 中的数据。

## 创建 Vault

用户通过 Android Studio 工具创建应用框架后，通过 SDK 实例化 Vault 对象，然后通过 Vault 对象来获取对应的子服务接口。在实例化 Vault 对象时，需要准备好一定的程序上下文环境。这里我们需要使用 AppContext 和 Hive Node Provider URL地址。创建 AppContext 会用到 Application Instance DID 和 User DID 。

```java
AppContext appContext = AppContext.build(new AppContextProvider() {
    @Override
    public String getLocalDataDir() {
        // return local location for storing data.
        return getLocalStorePath();
    }

    @Override
    public DIDDocument getAppInstanceDocument() {
        // return the application instance DID document.
        try {
            return appInstanceDid.getDocument();
        } catch (DIDException e) {
            e.printStackTrace();
        }
        return null;
    }

    @Override
    public CompletableFuture<String> getAuthorization(String jwtToken) {
        // return the authorization string for auth API.
        return CompletableFuture.supplyAsync(() -> {
            try {
                Claims claims = new JwtParserBuilder().build().parseClaimsJws(jwtToken).getBody();
                if (claims == null)
                    throw new HiveException("Invalid jwt token as authorization.");
                return appInstanceDid.createToken(appInstanceDid.createPresentation(
                        userDid.issueDiplomaFor(appInstanceDid),
                        claims.getIssuer(), (String) claims.get("nonce")), claims.getIssuer());
            } catch (Exception e) {
                throw new CompletionException(new HiveException(e.getMessage()));
            }
        });
    }
}, userDid.toString());
```

同时通过选择可信的Hive Node 服务节点，填充对应的 Hive Node 地址作为 Provider address 参数。有了 AppContext 对象和 Provider address，就可以实例化已存在的 Vault service 对象：

```java
Vault vault = new Vault(appContext, getVaultProviderAddress());
```

## 获取 FilesService 接口

因为示例中仅涉及文件上传功能，这里只需获取FilesService 接口即可，具体调用接口如下：

```java
FilesService filesService = vault.getFilesService());
```

## 上传文件

一旦获取 FilesService 接口实例后，便可以通过该接口来将文件数据上传到 Vault serivce中。在实现上传文件时，先获取用于写的 Writer ，然后将文件内容写入完成整个上传文件数据的流程。

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

filesService.getUploadWriter(REMOTE_FILE_PATH)
    .thenCompose(this::writeFileContent)
    .thenAcceptAsync(result -> hideLoadingWithMessage(null))
    .exceptionally(ex -> {
        hideLoadingWithMessage(ex);
        return null;
    });
```