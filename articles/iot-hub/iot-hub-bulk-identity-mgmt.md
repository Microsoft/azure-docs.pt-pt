---
title: Importar exportação de identidades de dispositivo do Hub IoT do Azure | Microsoft Docs
description: Como usar o SDK do serviço IoT do Azure para executar operações em massa no registro de identidade para importar e exportar identidades de dispositivo. As operações de importação permitem criar, atualizar e excluir identidades de dispositivo em massa.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 0d0643adc56a3dcdeef163708c26f2425ab8af43
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429262"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Importar e exportar identidades de dispositivo do Hub IoT em massa

Cada Hub IoT tem um registro de identidade que você pode usar para criar recursos por dispositivo no serviço. O registro de identidade também permite que você controle o acesso aos pontos de extremidade voltados para o dispositivo. Este artigo descreve como importar e exportar identidades de dispositivo em massa de e para um registro de identidade. Para ver um exemplo funcional no C# e saber como você pode usar esse recurso ao clonar um hub para uma região diferente, consulte [como clonar um hub IOT](iot-hub-how-to-clone.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

As operações de importação e exportação ocorrem no contexto de *trabalhos* que permitem que você execute operações de serviço em massa em um hub IOT.

A classe **registrymanager** inclui os métodos **ExportDevicesAsync** e **ImportDevicesAsync** que usam a estrutura de **trabalho** . Esses métodos permitem exportar, importar e sincronizar todo o registro de identidade do Hub IoT.

Este tópico discute o uso da classe **registrymanager** e do sistema de **trabalho** para executar importações e exportações em massa de dispositivos de e para o registro de identidade de um hub IOT. Você também pode usar o serviço de provisionamento de dispositivos no Hub IoT do Azure para habilitar o provisionamento sem toque e Just-in-time para um ou mais hubs IoT sem a necessidade de intervenção humana. Para saber mais, consulte a [documentação do serviço de provisionamento](/azure/iot-dps).


## <a name="what-are-jobs"></a>O que são trabalhos?

As operações de registro de identidade usam o sistema de **trabalho** quando a operação:

* Tem um tempo de execução potencialmente longo em comparação com as operações de tempo de execução padrão.

* Retorna uma grande quantidade de dados para o usuário.

Em vez de uma única chamada à API aguardando ou bloqueando o resultado da operação, a operação cria de forma assíncrona um **trabalho** para o Hub IOT. Em seguida, a operação retorna imediatamente um objeto **JobProperties** .

O trecho C# de código a seguir mostra como criar um trabalho de exportação:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Para usar a classe **registrymanager** em seu C# código, adicione o pacote NuGet **Microsoft. Azure. Devices** ao seu projeto. A classe **registrymanager** está no namespace **Microsoft. Azure. Devices** .

Você pode usar a classe **registrymanager** para consultar o estado do **trabalho** usando os metadados de **JobProperties** retornados. Para criar uma instância da classe **registrymanager** , use o método **CreateFromConnectionString** .

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Para localizar a cadeia de conexão para o Hub IoT, na portal do Azure:

- Navegue até ao seu hub IoT.

- Selecione **políticas de acesso compartilhado**.

- Selecione uma política, levando em conta as permissões necessárias.

- Copie o ConnectionString do painel no lado direito da tela.

O trecho C# de código a seguir mostra como sondar a cada cinco segundos para ver se o trabalho concluiu a execução:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="device-importexport-job-limits"></a>Limites de trabalho de importação/exportação de dispositivo

Somente um trabalho de importação ou exportação de dispositivo ativo é permitido por vez para todas as camadas do Hub IoT. O Hub IoT também tem limites para a taxa de operações de trabalhos. Para saber mais, consulte [referência – cotas e limitação do Hub IOT](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Exportar dispositivos

Use o método **ExportDevicesAsync** para exportar todo o registro de identidade do Hub IOT para um contêiner de blobs de armazenamento do Azure usando uma SAS (assinatura de acesso compartilhado). Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md).

Esse método permite que você crie backups confiáveis de suas informações de dispositivo em um contêiner de BLOBs que você controla.

O método **ExportDevicesAsync** requer dois parâmetros:

* Uma *cadeia de caracteres* que contém um URI de um contêiner de BLOB. Esse URI deve conter um token SAS que conceda acesso de gravação ao contêiner. O trabalho cria um blob de blocos nesse contêiner para armazenar os dados do dispositivo de exportação serializados. O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Um *booliano* que indica se você deseja excluir as chaves de autenticação dos dados de exportação. Se **for false**, as chaves de autenticação serão incluídas na saída de exportação. Caso contrário, as chaves são exportadas como **nulas**.

O trecho C# de código a seguir mostra como iniciar um trabalho de exportação que inclui chaves de autenticação de dispositivo nos dados de exportação e, em seguida, sondagem para conclusão:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

O trabalho armazena sua saída no contêiner de blob fornecido como um blob de blocos com o nome **Devices. txt**. Os dados de saída consistem em dados de dispositivo serializado JSON, com um dispositivo por linha.

O exemplo a seguir mostra os dados de saída:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se um dispositivo tiver dados de entrelaçamento, os dados de entrelaçamento também serão exportados junto com os dados do dispositivo. O exemplo a seguir mostra esse formato. Todos os dados da linha "twinETag" até o final são dados de linhas.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Se você precisar de acesso a esses dados no código, poderá desserializar facilmente esses dados usando a classe **ExportImportDevice** . O trecho C# de código a seguir mostra como ler as informações do dispositivo que foram exportadas anteriormente para um blob de blocos:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importar dispositivos

O método **ImportDevicesAsync** na classe **registrymanager** permite que você execute operações de importação e sincronização em massa em um registro de identidade do Hub IOT. Como o método **ExportDevicesAsync** , o método **ImportDevicesAsync** usa a estrutura de **trabalho** .

Tome cuidado com o método **ImportDevicesAsync** porque, além de provisionar novos dispositivos no registro de identidade, ele também pode atualizar e excluir dispositivos existentes.

> [!WARNING]
> Uma operação de importação não pode ser desfeita. Sempre faça backup dos dados existentes usando o método **ExportDevicesAsync** para outro contêiner de BLOBs antes de fazer alterações em massa no registro de identidade.

O método **ImportDevicesAsync** usa dois parâmetros:

* Uma *cadeia de caracteres* que contém um URI de um contêiner de blob de [armazenamento do Azure](../storage/index.yml) a ser usado como *entrada* para o trabalho. Esse URI deve conter um token SAS que conceda acesso de leitura ao contêiner. Esse contêiner deve conter um blob com o nome **Devices. txt** que contém os dados do dispositivo serializado para importar para o registro de identidade. Os dados de importação devem conter informações de dispositivo no mesmo formato JSON que o trabalho **ExportImportDevice** usa ao criar um blob **Devices. txt** . O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Uma *cadeia de caracteres* que contém um URI de um contêiner de blob de [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) a ser usado como *saída* do trabalho. O trabalho cria um blob de blocos nesse contêiner para armazenar qualquer informação de erro do **trabalho**de importação concluído. O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Os dois parâmetros podem apontar para o mesmo contêiner de BLOB. Os parâmetros separados simplesmente permitem mais controle sobre seus dados, pois o contêiner de saída requer permissões adicionais.

O trecho C# de código a seguir mostra como iniciar um trabalho de importação:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Esse método também pode ser usado para importar os dados para o dispositivo. O formato da entrada de dados é o mesmo que o formato mostrado na seção **ExportDevicesAsync** . Dessa forma, você pode reimportar os dados exportados. O **$Metadata** é opcional.

## <a name="import-behavior"></a>Comportamento de importação

Você pode usar o método **ImportDevicesAsync** para executar as seguintes operações em massa em seu registro de identidade:

* Registro em massa de novos dispositivos
* Exclusões em massa de dispositivos existentes
* Alterações de status em massa (habilitar ou desabilitar dispositivos)
* Atribuição em massa de novas chaves de autenticação de dispositivo
* Regeneração automática em massa de chaves de autenticação de dispositivo
* Atualização em massa de dados de entrelaçamento

Você pode executar qualquer combinação das operações anteriores em uma única chamada **ImportDevicesAsync** . Por exemplo, você pode registrar novos dispositivos e excluir ou atualizar dispositivos existentes ao mesmo tempo. Quando usado junto com o método **ExportDevicesAsync** , você pode migrar completamente todos os seus dispositivos de um hub IOT para outro.

Se o arquivo de importação incluir metadados de entrelaçamento, esses metadados substituirão os metadados de entrelaçamento existentes. Se o arquivo de importação não incluir metadados de entrelaçamento, somente os metadados de `lastUpdateTime` serão atualizados usando a hora atual.

Use a propriedade **importmode** opcional nos dados de serialização de importação para cada dispositivo para controlar o processo de importação por dispositivo. A propriedade **importmode** tem as seguintes opções:

| importMode | Descrição |
| --- | --- |
| **createOrUpdate** |Se um dispositivo não existir com a **ID**especificada, ele será registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag** . <br> Opcionalmente, o usuário pode especificar o entrelaçar dados junto com os dados do dispositivo. O ETag de ' s ', se especificado, é processado independentemente da eTag do dispositivo. Se houver uma incompatibilidade com a ETag de ' s existente, um erro será gravado no arquivo de log. |
| **criar** |Se um dispositivo não existir com a **ID**especificada, ele será registrado recentemente. <br/>Se o dispositivo já existir, um erro será gravado no arquivo de log. <br> Opcionalmente, o usuário pode especificar o entrelaçar dados junto com os dados do dispositivo. O ETag de ' s ', se especificado, é processado independentemente da eTag do dispositivo. Se houver uma incompatibilidade com a ETag de ' s existente, um erro será gravado no arquivo de log. |
| **update** |Se já existir um dispositivo com a **ID**especificada, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag** . <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **updateIfMatchETag** |Se já existir um dispositivo com a **ID**especificada, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência **ETag** . <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se houver uma incompatibilidade de **ETag** , um erro será gravado no arquivo de log. |
| **createOrUpdateIfMatchETag** |Se um dispositivo não existir com a **ID**especificada, ele será registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência de **ETag** . <br/>Se houver uma incompatibilidade de **ETag** , um erro será gravado no arquivo de log. <br> Opcionalmente, o usuário pode especificar o entrelaçar dados junto com os dados do dispositivo. O ETag de ' s ', se especificado, é processado independentemente da eTag do dispositivo. Se houver uma incompatibilidade com a ETag de ' s existente, um erro será gravado no arquivo de log. |
| **eliminar** |Se um dispositivo já existir com a **ID**especificada, ele será excluído sem considerar o valor de **ETag** . <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **deleteIfMatchETag** |Se já existir um dispositivo com a **ID**especificada, ele será excluído somente se houver uma correspondência **ETag** . Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se houver uma incompatibilidade de ETag, um erro será gravado no arquivo de log. |

> [!NOTE]
> Se os dados de serialização não definirem explicitamente um sinalizador **importmode** para um dispositivo, o padrão será **createOrUpdate** durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exemplo de importação de dispositivos – provisionamento de dispositivos em massa

O exemplo C# de código a seguir ilustra como gerar várias identidades de dispositivo que:

* Incluir chaves de autenticação.
* Grave essas informações de dispositivo em um blob de blocos.
* Importe os dispositivos para o registro de identidade.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemplo de importação de dispositivos – exclusão em massa

O exemplo de código a seguir mostra como excluir os dispositivos que você adicionou usando o exemplo de código anterior:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Obter o URI de SAS do contêiner

O exemplo de código a seguir mostra como gerar um [URI de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) com permissões de leitura, gravação e exclusão para um contêiner de blob:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a executar operações em massa em relação ao registro de identidade em um hub IoT. Muitas dessas operações, incluindo como mover dispositivos de um hub para outro, são usadas na [seção Gerenciando dispositivos registrados para o Hub IOT de como clonar um hub IOT](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

O artigo de clonagem tem um exemplo funcional associado a ele, que está localizado nos C# exemplos de IOT nesta página: [exemplos de IOT C#do Azure para ](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), com o projeto sendo ImportExportDevicesSample. Você pode baixar o exemplo e experimentá-lo; Há instruções no artigo [como clonar um hub IOT](iot-hub-how-to-clone.md) .

Para saber mais sobre como gerenciar o Hub IoT do Azure, confira os seguintes artigos:

* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Logs do Hub IoT](iot-hub-monitor-resource-health.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar o uso do serviço de provisionamento de dispositivos no Hub IoT para habilitar o provisionamento sem toque e Just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
