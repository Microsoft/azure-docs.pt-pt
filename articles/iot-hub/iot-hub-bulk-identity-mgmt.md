---
title: Importação/Exportação de identidades do dispositivo Azure IoT Hub [ Import/Exportação de identidades do dispositivo Azure IoT Hub ] Microsoft Docs
description: Como utilizar o serviço Azure IoT SDK para executar operações a granel contra o registo de identidade para identificar identidades de dispositivos de importação e exportação. As operações de importação permitem-lhe criar, atualizar e eliminar identidades do dispositivo a granel.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 2a0394e6e7c17e0a4954bbdddb1d5b2811959746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371584"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Importar e exportar identidades do dispositivo do Hub IoT em massa

Cada hub IoT tem um registo de identidade que pode usar para criar recursos por dispositivo no serviço. O registo de identidade também permite controlar o acesso aos pontos finais virados para o dispositivo. Este artigo descreve como importar e exportar identidades de dispositivos a granel de e para um registo de identidade. Para ver uma amostra de trabalho em C# e aprender como pode usar esta capacidade ao clonar um hub para outra região, veja [Como Clonar um Hub IoT](iot-hub-how-to-clone.md).

> [!NOTE]
> O IoT Hub adicionou recentemente suporte à rede virtual num número limitado de regiões. Esta funcionalidade assegura as operações de importação e exportação e elimina a necessidade de passar chaves para autenticação.  Inicialmente, o suporte à rede virtual só está disponível nestas regiões: *WestUS2*, *EastUS*, e *SouthCentralUS.* Para saber mais sobre o suporte à rede virtual e as chamadas da API para implementá-lo, consulte [o IoT Hub Support para redes virtuais.](virtual-network-support.md)

As operações de importação e exportação ocorrem no contexto do *Jobs* que lhe permite executar operações de serviço a granel contra um hub IoT.

A classe **RegistryManager** inclui os **métodos ExportDevicesAsync** e **ImportDevicesAsync** que utilizam a estrutura **de trabalho.** Estes métodos permitem-lhe exportar, importar e sincronizar a totalidade de um registo de identidade do hub IoT.

Este tópico discute a utilização da classe **RegistryManager** e do sistema **de emprego** para realizar importações a granel e exportações de dispositivos de e para o registo de identidade de um hub IoT. Também pode utilizar o Serviço de Provisionamento de Dispositivos Hub Azure IoT para permitir o fornecimento de zero toques, just-in-time a um ou mais hubs IoT sem necessitar de intervenção humana. Para saber mais, consulte a documentação do serviço de [fornecimento.](/azure/iot-dps)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="what-are-jobs"></a>O que são empregos?

As operações de registo de identidade utilizam o sistema **de trabalho** quando a operação:

* Tem um tempo de execução potencialmente longo em comparação com as operações padrão de tempo de execução.

* Devolve uma grande quantidade de dados ao utilizador.

Em vez de uma única chamada da API à espera ou ao bloqueio do resultado da operação, a operação cria assincronicamente um **Job** para esse centro IoT. A operação devolve imediatamente um objeto **JobProperties.**

O seguinte código C# snippet mostra como criar um trabalho de exportação:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Para utilizar a classe **RegistryManager** no seu código C#, adicione o pacote **NuGet Microsoft.Azure.Devices** nuGet ao seu projeto. A classe **RegistryManager** está no espaço de nome **Microsoft.Azure.Devices.**

Pode utilizar a classe **RegistryManager** para consultar o estado do **Trabalho** utilizando os metadados devolvidos do **JobProperties.** Para criar uma instância da classe **RegistryManager,** utilize o método **CreateFromConnectionString.**

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Para encontrar a corda de ligação para o seu hub IoT, no portal Azure:

- Navegue até ao seu hub IoT.

- Selecione políticas de **acesso partilhado**.

- Selecione uma apólice, tendo em conta as permissões de que necessita.

- Copie o fio de ligação do painel no lado direito do ecrã.

O seguinte código C# mostra como fazer sondagens a cada cinco segundos para ver se o trabalho terminou de executar:

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

> [!NOTE]
> Se a sua conta de armazenamento tiver configurações de firewall que restringem a conectividade do IoT Hub, considere utilizar a [microsoft confiável primeira exceção de primeira parte](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (disponível em regiões selecionadas para centros IoT com identidade de serviço gerida).


## <a name="device-importexport-job-limits"></a>Limites de importação/exportação de dispositivos

Apenas 1 trabalho ativo de importação ou exportação de dispositivos é permitido de cada vez para todos os níveis IoT Hub. O IoT Hub também tem limites para a taxa de operações de emprego. Para saber mais, consulte [referência - Quotas IoT Hub e estrangulamento](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Dispositivos de exportação

Utilize o método **ExportDevicesAsync** para exportar a totalidade de um registo de identidade do hub IoT para um recipiente de blob Azure Storage utilizando uma assinatura de acesso partilhado (SAS). Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

Este método permite-lhe criar cópias de segurança fiáveis da informação do seu dispositivo num recipiente de bolha que controla.

O método **ExportDevicesAsync** requer dois parâmetros:

* Uma *corda* que contém um URI de um recipiente de bolhas. Este URI deve conter um símbolo SAS que concede acesso escrito ao recipiente. O trabalho cria uma bolha de bloco neste recipiente para armazenar os dados do dispositivo de exportação serializado. O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Uma *booleana* que indica se pretende excluir chaves de autenticação dos seus dados de exportação. Se **forem falsas,** as chaves de autenticação estão incluídas na produção de exportação. Caso contrário, as chaves são exportadas como **nulas.**

O seguinte código C# mostra como iniciar um trabalho de exportação que inclui chaves de autenticação de dispositivos nos dados de exportação e, em seguida, pesquisa para conclusão:

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

O trabalho armazena a sua saída no recipiente de bolhas fornecido como uma bolha de bloco com o nome **dispositivos.txt**. Os dados de saída são compostos por dados do dispositivo serializado JSON, com um dispositivo por linha.

O exemplo que se segue mostra os dados de saída:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se um dispositivo tiver dados duplos, os dados gémeos também são exportados juntamente com os dados do dispositivo. O exemplo que se segue mostra este formato. Todos os dados da linha "twinETag" até ao fim são dados duplos.

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

Se necessitar de acesso a estes dados em código, pode facilmente desserializar estes dados utilizando a classe **ExportImportDevice.** O seguinte código C# snippet mostra como ler informações do dispositivo que foram previamente exportadas para uma bolha de bloco:

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

## <a name="import-devices"></a>Dispositivos de importação

O método **ImportDevicesAsync** na classe **RegistryManager** permite-lhe realizar operações de importação e sincronização a granel num registo de identidade de hub IoT. Tal como o método **ExportDevicesAsync,** o método **ImportDevicesAsync** utiliza a estrutura **de trabalho.**

Tenha cuidado utilizando o método **ImportDevicesAsync** porque além de fornecer novos dispositivos no seu registo de identidade, também pode atualizar e eliminar os dispositivos existentes.

> [!WARNING]
> Uma operação de importação não pode ser desfeita. Faça sempre o backup dos dados existentes utilizando o método **ExportDevicesAsync** para outro recipiente blob antes de fazer alterações a granel no seu registo de identidade.

O método **ImportDevicesAsync** leva dois parâmetros:

* Uma *corda* que contém um URI de um recipiente de blob [Azure Storage](../storage/index.yml) para usar como *entrada* para o trabalho. Este URI deve conter um símbolo SAS que concede o acesso lido ao recipiente. Este recipiente deve conter uma bolha com o nome **dispositivos.txt** que contenha os dados do dispositivo serializado para importar para o seu registo de identidade. Os dados de importação devem conter informações do dispositivo no mesmo formato JSON que o trabalho **exportImportDevice** utiliza quando cria uma bolha **de dispositivos.txt.** O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Uma *corda* que contém um URI de um recipiente de blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) para usar como *saída* do trabalho. O trabalho cria uma bolha de bloco neste recipiente para armazenar qualquer informação de erro do **trabalho**de importação concluído . O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Os dois parâmetros podem apontar para o mesmo recipiente de bolhas. Os parâmetros separados simplesmente permitem um maior controlo sobre os seus dados, uma vez que o recipiente de saída requer permissões adicionais.

O seguinte código C# snippet mostra como iniciar um trabalho de importação:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Este método também pode ser utilizado para importar os dados para o dispositivo twin. O formato para a entrada de dados é o mesmo que o formato mostrado na secção **ExportDevicesAsync.** Desta forma, pode reimportar os dados exportados. O **$metadata** é opcional.

## <a name="import-behavior"></a>Comportamento de importação

Pode utilizar o método **ImportDevicesAsync** para efetuar as seguintes operações a granel no seu registo de identidade:

* Registo a granel de novos dispositivos
* Supressão a granel dos dispositivos existentes
* Alterações no estado a granel (dispositivos de ativação ou desativação)
* Atribuição a granel de novas chaves de autenticação de dispositivos
* Regeneração automática a granel das teclas de autenticação do dispositivo
* Atualização a granel de dados gémeos

Pode executar qualquer combinação das operações anteriores dentro de uma única chamada **importDevicesAsync.** Por exemplo, pode registar novos dispositivos e eliminar ou atualizar os dispositivos existentes ao mesmo tempo. Quando utilizado juntamente com o método **ExportDevicesAsync,** pode migrar completamente todos os seus dispositivos de um hub IoT para outro.

Se o ficheiro de importação inclui metadados duplos, então estes metadados sobrepor-se aos metadados gémeos existentes. Se o ficheiro de importação não incluir `lastUpdateTime` metadados duplos, apenas os metadados são atualizados utilizando o tempo atual.

Utilize a propriedade opcional de **importaçãoMode** nos dados de serialização de importação para cada dispositivo para controlar o processo de importação por dispositivo. A propriedade **importMode** tem as seguintes opções:

| importMode | Descrição |
| --- | --- |
| **criaorAtualizar** |Se um dispositivo não existir com o **ID**especificado, este é recentemente registado. <br/>Se o dispositivo já existir, as informações existentes são sobreescritas com os dados de entrada fornecidos sem ter em conta o valor **ETag.** <br> O utilizador pode especificar opcionalmente dados duplos juntamente com os dados do dispositivo. O veado do gémeo, se especificado, é processado independentemente do etag do dispositivo. Se houver um desfasamento com o etag do gémeo existente, um erro é escrito no ficheiro de registo. |
| **criar** |Se um dispositivo não existir com o **ID**especificado, este é recentemente registado. <br/>Se o dispositivo já existir, um erro está escrito no ficheiro de registo. <br> O utilizador pode especificar opcionalmente dados duplos juntamente com os dados do dispositivo. O veado do gémeo, se especificado, é processado independentemente do etag do dispositivo. Se houver um desfasamento com o etag do gémeo existente, um erro é escrito no ficheiro de registo. |
| **atualização** |Se um dispositivo já existir com o **ID**especificado, as informações existentes são substituídas com os dados de entrada fornecidos sem ter em conta o valor **Do ETag.** <br/>Se o dispositivo não existir, um erro está escrito no ficheiro de registo. |
| **atualizaçõesIfMatchETag** |Se um dispositivo já existir com o **ID**especificado, as informações existentes só são substituídas com os dados de entrada fornecidos se houver uma correspondência **eTag.** <br/>Se o dispositivo não existir, um erro está escrito no ficheiro de registo. <br/>Se houver uma incompatibilidade **eTag,** um erro é escrito no ficheiro de registo. |
| **criarOrUpdateIfMatchETag** |Se um dispositivo não existir com o **ID**especificado, este é recentemente registado. <br/>Se o dispositivo já existir, as informações existentes só são substituídas com os dados de entrada fornecidos se houver uma correspondência **eTag.** <br/>Se houver uma incompatibilidade **eTag,** um erro é escrito no ficheiro de registo. <br> O utilizador pode especificar opcionalmente dados duplos juntamente com os dados do dispositivo. O veado do gémeo, se especificado, é processado independentemente do etag do dispositivo. Se houver um desfasamento com o etag do gémeo existente, um erro é escrito no ficheiro de registo. |
| **excluir** |Se um dispositivo já existir com o **ID**especificado, é eliminado sem ter em conta o valor **ETag.** <br/>Se o dispositivo não existir, um erro está escrito no ficheiro de registo. |
| **excluirIfMatchETag** |Se um dispositivo já existir com o **ID**especificado, só é eliminado se houver uma correspondência **eTag.** Se o dispositivo não existir, um erro está escrito no ficheiro de registo. <br/>Se houver uma incompatibilidade eTag, um erro é escrito no ficheiro de registo. |

> [!NOTE]
> Se os dados de serialização não definirem explicitamente uma bandeira de **importmode** para um dispositivo, não se defina para **criarOrUpdate** durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exemplo de dispositivos de importação - fornecimento de dispositivos a granel

A seguinte amostra de código C# ilustra como gerar múltiplas identidades do dispositivo que:

* Inclua chaves de autenticação.
* Escreva a informação do dispositivo para uma bolha de bloco.
* Importar os dispositivos para o registo de identidade.

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

## <a name="import-devices-example--bulk-deletion"></a>Exemplo de dispositivos de importação – eliminação a granel

A seguinte amostra de código mostra-lhe como eliminar os dispositivos que adicionou utilizando a amostra de código anterior:

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

## <a name="get-the-container-sas-uri"></a>Pegue o recipiente SAS URI

A seguinte amostra de código mostra-lhe como gerar um [SAS URI](../storage/common/storage-dotnet-shared-access-signature-part-1.md) com leitura, escrita e exclusão de permissões para um recipiente de bolhas:

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

Neste artigo, aprendeu a realizar operações a granel contra o registo de identidade num centro ioT. Muitas destas operações, incluindo como mover dispositivos de um hub para outro, são utilizadas nos [dispositivos de Gestão registados na secção do hub IoT de How to Clone a IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

O artigo de clonagem tem uma amostra de trabalho associada a ele, que está localizada nas amostras IoT C# nesta página: [Amostras Azure IoT para C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), sendo o projeto ImportExportDevicesSample. Você pode baixar a amostra e experimentá-la; existem instruções no [artigo "Como Clonar um Hub IoT".](iot-hub-how-to-clone.md)

Para saber mais sobre a gestão do Azure IoT Hub, confira os seguintes artigos:

* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Troncos do Hub IoT](iot-hub-monitor-resource-health.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar a utilização do Serviço de Provisionamento de Dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
