---
title: Importar a exportação de identidades de dispositivos do IoT Hub do Azure | Documentos da Microsoft
description: Como utilizar o SDK do serviço IoT do Azure para realizar operações em massa em relação de registo de identidade para importar e Exportar identidades do dispositivo. Operações de importação permitem-lhe criar, atualizar e eliminar as identidades de dispositivos em massa.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robin.shahan
ms.openlocfilehash: 5ef34fb039d35ff714e249a6ac107e6ec615093e
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010995"
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Gerir as identidades de dispositivo do IoT Hub em massa

Cada hub IoT tem um registo de identidades, que pode usar para criar recursos por dispositivo no serviço. Também o registo de identidade permite-lhe controlar o acesso aos pontos finais do dispositivo com acesso à. Este artigo descreve como importar e Exportar identidades de dispositivos em massa de e para um registo de identidades.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Operações de importação e exportação ocorrem no contexto do *tarefas* que permitem que execute operações de serviço em massa em relação a um hub IoT.

O **RegistryManager** classe inclui o **ExportDevicesAsync** e **ImportDevicesAsync** métodos que utilizam o **tarefa** framework. Esses métodos permitem-lhe exportar, importar e sincronizar a totalidade de um registo de identidades do hub IoT.

Este tópico aborda o uso da **RegistryManager** classe e **tarefa** sistema para executar em massa importações e exportações de dispositivos de e para o registo de identidade de um hub IoT. Também pode utilizar o serviço de aprovisionamento de dispositivos do Azure IoT Hub para ativar o zero touch, just-in-time aprovisionamento um ou mais hubs IoT sem que seja necessária intervenção humana. Para obter mais informações, consulte a [documentação do serviço de aprovisionamento](/azure/iot-dps).


## <a name="what-are-jobs"></a>O que são tarefas?

A utilizar operações de registo de identidade a **tarefa** sistema quando a operação:

* Tem um tempo de execução potencialmente longo em comparação com operações de tempo de execução padrão.

* Devolve uma grande quantidade de dados para o usuário.

Em vez de uma única chamada à API à espera ou de bloqueio no resultado da operação, a operação cria de forma assíncrona uma **tarefa** para esse hub IoT. A operação, em seguida, imediatamente devolve um **JobProperties** objeto.

O seguinte trecho de código do c# mostra como criar uma tarefa de exportação:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Para utilizar o **RegistryManager** no seu código c#, adicione o **Microsoft.Azure.Devices** pacote NuGet ao seu projeto. O **RegistryManager** classe está no **Microsoft.Azure.Devices** espaço de nomes.

Pode utilizar o **RegistryManager** classe para consultar o estado da **tarefa** usando retornado **JobProperties** metadados. Para criar uma instância do **RegistryManager** de classe, utilize o **CreateFromConnectionString** método.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Para localizar a cadeia de ligação do hub IoT, no portal do Azure:

- Navegue até ao seu hub IoT.

- Selecione **políticas de acesso partilhado**.

- Selecione uma política, tendo em conta as permissões que necessárias.

- Copie o connectionstring do painel no lado direito da tela.

O seguinte trecho de código do c# mostra como consultar a cada cinco segundos para ver se a tarefa foi concluída em execução:

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

## <a name="export-devices"></a>Exportar dispositivos

Utilize o **ExportDevicesAsync** método para exportar a totalidade de um registo de identidades do hub IoT para uma [armazenamento do Azure](../storage/index.yml) contentor de Blobs com uma [assinatura de acesso partilhado](../storage/common/storage-security-guide.md#data-plane-security).

Este método permite-lhe criar cópias de segurança confiáveis das suas informações de dispositivo num contentor de BLOBs que controla.

O **ExportDevicesAsync** método requer dois parâmetros:

* R *cadeia de caracteres* que contém um URI de um contentor de Blobs. Este URI tem de conter um token SAS que concede acesso de escrita ao contentor. A tarefa cria um blob de blocos nesse contêiner para armazenar os dados de dispositivo de exportação serializada. O token SAS tem de incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* R *booleano* que indica se pretende excluir as chaves de autenticação a partir dos seus dados de exportação. Se **false**, chaves de autenticação estão incluídas na saída de exportação. Caso contrário, as chaves são exportadas como **nulo**.

O seguinte trecho de código do c# mostra como iniciar uma tarefa de exportação que inclui as chaves de autenticação do dispositivo nos exportar dados e, em seguida, efetuar consultas para conclusão:

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

A tarefa armazena sua saída no contentor de BLOBs fornecido como um blob de blocos com o nome **devices.txt**. Os dados de saída consiste em dados de dispositivo JSON serializada, com um dispositivo por linha.

O exemplo seguinte mostra os dados de saída:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se um dispositivo tiver dados duplo, os dados de duplo também são exportados, juntamente com os dados do dispositivo. O exemplo seguinte mostra este formato. Todos os dados a partir da linha de "twinETag" até que o fim é dados duplo.

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

Se precisar de acesso a estes dados no código, pode facilmente anular a serialização este dados com o **ExportImportDevice** classe. O seguinte trecho de código do c# mostra como ler informações de dispositivo que tenha sido anteriormente exportadas para um blob de blocos:

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

O **ImportDevicesAsync** método na **RegistryManager** classe permite-lhe efetuar operações em massa para importação e sincronização de um registo de identidades do hub IoT. Como o **ExportDevicesAsync** método, o **ImportDevicesAsync** método utiliza o **tarefa** framework.

Tenha cuidado com o **ImportDevicesAsync** método porque além de aprovisionar novos dispositivos no seu registo de identidade, ele pode também atualizar e eliminar dispositivos existentes.

> [!WARNING]
> Uma operação de importação não pode ser anulada. Sempre faça backup dos dados existentes com o **ExportDevicesAsync** método para outro contentor de BLOBs, antes de fazer em massa altera para o seu registo de identidade.

O **ImportDevicesAsync** método usa dois parâmetros:

* R *cadeia de caracteres* que contém um URI de um [armazenamento do Azure](../storage/index.yml) contentor de BLOBs para utilizar como *entrada* para a tarefa. Este URI tem de conter um token SAS que concede acesso de leitura para o contentor. Este contentor pode conter um blob com o nome **devices.txt** que contém os dados de dispositivo serializada para importar para o seu registo de identidade. Os dados de importação tem de conter informações de dispositivos no mesmo formato de JSON que o **ExportImportDevice** tarefa utiliza quando cria um **devices.txt** blob. O token SAS tem de incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* R *cadeia de caracteres* que contém um URI de um [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) contentor de BLOBs para utilizar como *saída* da tarefa. A tarefa cria um blob de blocos neste contentor para armazenar as informações de erro de importação concluída **tarefa**. O token SAS tem de incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Os dois parâmetros podem apontar para o mesmo contentor de Blobs. Os parâmetros separados simplesmente permitem mais controle sobre seus dados à medida que o contentor de saída necessita de permissões adicionais.

O seguinte trecho de código do c# mostra como iniciar uma tarefa de importação:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Este método também pode ser utilizado para importar os dados para o dispositivo duplo. O formato para os dados de entrada é igual ao formato mostrado na **ExportDevicesAsync** secção. Dessa forma, pode reimportar os dados exportados. O **$metadata** é opcional.

## <a name="import-behavior"></a>Comportamento de importação

Pode utilizar o **ImportDevicesAsync** método para executar as seguintes operações em massa no seu registo de identidade:

* Registo em massa de novos dispositivos
* Eliminações em massa de dispositivos existentes
* Em massa alterações de estado (ativar ou desativar dispositivos)
* Atribuição de novas chaves de autenticação do dispositivo em massa
* Em massa auto-regeneração das chaves de autenticação do dispositivo
* Atualização em massa de dados duplo

Pode executar qualquer combinação das operações anteriores num único **ImportDevicesAsync** chamar. Por exemplo, pode registar novos dispositivos e eliminar ou atualizar dispositivos existentes ao mesmo tempo. Quando utilizado juntamente com o **ExportDevicesAsync** método, pode migrar completamente todos os seus dispositivos de um hub IoT para outro.

Se o ficheiro de importação inclui metadados de duplo, em seguida, estes metadados substitui os metadados de duplo existente. Se o ficheiro de importação não inclui metadados duplo, em seguida, apenas o `lastUpdateTime` metadados é atualizado com a hora atual.

Utilizar o opcional **importMode** propriedade nos dados de serialização de importação para cada dispositivo controlar o importação processo por dispositivo. O **importMode** propriedade tem as seguintes opções:

| importMode | Descrição |
| --- | --- |
| **createOrUpdate** |Se não existir um dispositivo com a especificado **id**, recentemente está registado. <br/>Se o dispositivo já existir, informações existentes serão substituídas com os dados de entrada fornecidos sem para o **ETag** valor. <br> O utilizador pode, opcionalmente, especificar dados duplo, juntamente com os dados do dispositivo. Etag do duplo, se for especificado, é processada independentemente da etag do dispositivo. Se existir um erro de correspondência com o etag o duplo existente, um erro é escrito para o ficheiro de registo. |
| **criar** |Se não existir um dispositivo com a especificado **id**, recentemente está registado. <br/>Se o dispositivo já existir, um erro é escrito para o ficheiro de registo. <br> O utilizador pode, opcionalmente, especificar dados duplo, juntamente com os dados do dispositivo. Etag do duplo, se for especificado, é processada independentemente da etag do dispositivo. Se existir um erro de correspondência com o etag o duplo existente, um erro é escrito para o ficheiro de registo. |
| **update** |Se já existe um dispositivo com a especificado **id**, informações existentes serão substituídas com os dados de entrada fornecidos sem para o **ETag** valor. <br/>Se o dispositivo não existir, um erro é escrito para o ficheiro de registo. |
| **updateIfMatchETag** |Se já existe um dispositivo com a especificado **id**, informações existentes serão substituídas com os dados de entrada fornecidos apenas se houver um **ETag** corresponder. <br/>Se o dispositivo não existir, um erro é escrito para o ficheiro de registo. <br/>Se houver uma **ETag** erro de correspondência, um erro é escrito para o ficheiro de registo. |
| **createOrUpdateIfMatchETag** |Se não existir um dispositivo com a especificado **id**, recentemente está registado. <br/>Se o dispositivo já existir, informações existentes serão substituídas com os dados de entrada fornecidos apenas se houver uma **ETag** corresponder. <br/>Se houver uma **ETag** erro de correspondência, um erro é escrito para o ficheiro de registo. <br> O utilizador pode, opcionalmente, especificar dados duplo, juntamente com os dados do dispositivo. Etag do duplo, se for especificado, é processada independentemente da etag do dispositivo. Se existir um erro de correspondência com o etag o duplo existente, um erro é escrito para o ficheiro de registo. |
| **eliminar** |Se já existe um dispositivo com a especificado **id**, este é eliminado sem para o **ETag** valor. <br/>Se o dispositivo não existir, um erro é escrito para o ficheiro de registo. |
| **deleteIfMatchETag** |Se já existe um dispositivo com o especificado **id**, é eliminado apenas se existir um **ETag** corresponder. Se o dispositivo não existir, um erro é escrito para o ficheiro de registo. <br/>Se existir um erro de correspondência de ETag, um erro é escrito para o ficheiro de registo. |

> [!NOTE]
> Se os dados de serialização não definem explicitamente uma **importMode** sinalizador para um dispositivo, assume como predefinição **createOrUpdate** durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importar do exemplo de dispositivos – em massa de aprovisionamento de dispositivos

O seguinte exemplo de código do c# ilustra como gerar várias identidades de dispositivo que:

* Incluem as chaves de autenticação.
* Gravar essa informação do dispositivo para um blob de blocos.
* Importe os dispositivos para o registo de identidade.

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

## <a name="import-devices-example--bulk-deletion"></a>Importar do exemplo de dispositivos – eliminação em massa

O exemplo de código seguinte mostra como eliminar os dispositivos que foi adicionado com o código de exemplo anterior:

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

## <a name="get-the-container-sas-uri"></a>Obter o URI de SAS do contentor

O exemplo de código seguinte mostra-lhe como gerar uma [URI de SAS](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) com leitura, escrita e eliminação de permissões para um contentor de BLOBs:

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

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a realizar operações em massa de registo de identidade num IoT hub. Siga estas ligações para saber mais sobre como gerir o IoT Hub do Azure:

* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar, utilizando o serviço de aprovisionamento de dispositivos do IoT Hub para ativar o aprovisionamento sem toque e just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
