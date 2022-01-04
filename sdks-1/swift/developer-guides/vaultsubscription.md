# VaultSubscription

Hive SDK需要通过Vault实例与远程的Vault Service进行交互。用户预先在可信Hive Node上订阅创建Vault Service，然后才能将应用数据存取到对应的 Vault中.

VaultSubscription类就是用来定订阅创建Vault Service，Hive Node服务根据请求者的DID身份创建新的Vault Service。如果基于该DID身份的Vault Service已经存在，则返回已存在的 Vault Service元数据.

## Example

使用 vaultSubscription实例在可信Hive Node订阅创建新的Vault Service，返回一个 CompletableFuture 对象，包含已订阅的远端Vault Service的元数据信息。实际样例代码如下：

```swift
let subscription = try VaultSubscription(testData.appContext, testData.providerAddress)
subscription.subscribe()
.done { vault in
   print("Registered vault service successfully.")
   
   print("service DID ==> \(vaultInfo.serviceDid)")
   print("storage quota ==> " + vaultInfo.getStorageQuota());
   print("price plan ==> \(vaultInfo.pricePlan)")
}
.catch { error in
   print("Failed to register vault service")
   print(error)
}
```

一旦订阅成功，就可以使用实例化对象 vault 来存取应用数据。

## Subscription management

### Check Subscription

用户在可信Hive Node上订阅创建后，可以使用vault实例来存取应用中的数据，也可以在使用以下接口查看对应Vault Service的信息。

```swift
subscription.checkSubscription()
.done { vault in
   print("Dump remote vault service:")
   print("service DID ==> \(vaultInfo.serviceDid)")
   print("storage quota ==> \(vaultInfo.storageQuota)")
   print("price plan ==> \(vaultInfo.pricePlan)")
}
.catch { error in
   print("Failed to check vault subscription")
   print(error)
}
```

### Unsubscription

用户在使用Vault Service一段时间，根据需要，可以将已使用中Vault Service迁移到新的 Hive Node中，迁移完成后需要停止原来 Hive Node上的 Vault Service, 同时销毁其中数据。

```swift
subscription.unsubscribe()
.done { success in
   print("Unsubscribed from the vault service")
   print("Cleared all data in this vault.")
}
.catch { error in
   print("Failed to unsubscribe to vault service")
   print(error)
}
```

注意：调用此接口取消vault Service 后，该vault内持有数据从对应 Hive Node 上被永久删除，同时该 Vault Serivce 也停止服务。用户必须先保证已有新的 Vault service 完成迁移后，再可调用该接口取消 原来需要废弃的vault service。