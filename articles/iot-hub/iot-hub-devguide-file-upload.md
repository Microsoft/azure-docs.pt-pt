---
title: Compreenda o upload do ficheiro Azure IoT Hub | Microsoft Docs
description: Developer guide - use a funcionalidade de upload de ficheiros do IoT Hub para gerir o upload de ficheiros de um dispositivo para um recipiente de blob de armazenamento Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: d106021d90304a06ea7c08494d626511bb903df0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553044"
---
# <a name="upload-files-with-iot-hub"></a>Carregar ficheiros com o Hub IoT

Conforme detalhado no artigo de [pontos finais do IoT Hub,](iot-hub-devguide-endpoints.md) um dispositivo pode iniciar um upload de ficheiros enviando uma notificação através de um ponto final virado para o dispositivo **(/dispositivos/{deviceId}/ficheiros).** Quando um dispositivo notifica o IoT Hub de que um upload está completo, o IoT Hub envia uma mensagem de notificação de upload de ficheiros através do ponto final de serviço **/messages/servicebound/filenotifications** face a um ponto final.

Em vez de intermediar mensagens através do próprio IoT Hub, o IoT Hub atua como um despachante para uma conta de Armazenamento Azure associada. Um dispositivo solicita um token de armazenamento do IoT Hub que é específico para o ficheiro que o dispositivo deseja carregar. O dispositivo utiliza o SAS URI para carregar o ficheiro para armazenamento, e quando o upload está completo, o dispositivo envia uma notificação de conclusão para o IoT Hub. O IoT Hub verifica se o upload do ficheiro está completo e, em seguida, adiciona uma mensagem de notificação de upload de ficheiros ao ponto final de notificação de ficheiros virado para o serviço.

Antes de enviar um ficheiro para o IoT Hub a partir de um dispositivo, tem de configurar o seu hub associando-lhe uma conta [de Armazenamento Azure.](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)

O seu dispositivo pode então [inicializar um upload](iot-hub-devguide-file-upload.md#initialize-a-file-upload) e, em seguida, notificar o [hub IoT](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) quando o upload estiver concluído. Opcionalmente, quando um dispositivo notifica o IoT Hub de que o upload está completo, o serviço pode gerar uma [mensagem de notificação](iot-hub-devguide-file-upload.md#file-upload-notifications).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>Quando utilizar

Utilize o upload de ficheiros para enviar ficheiros de mídia e grandes lotes de telemetria carregados por dispositivos intermitentemente ligados ou comprimidos para salvar a largura de banda.

Consulte a [orientação de comunicação dispositivo-nuvem](iot-hub-devguide-d2c-guidance.md) em caso de dúvida entre a utilização de propriedades relatadas, mensagens de dispositivo para nuvem ou upload de ficheiros.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associe uma conta de Armazenamento Azure com o IoT Hub

Para utilizar a funcionalidade de upload de ficheiros, tem primeiro de ligar uma conta de Armazenamento Azure ao IoT Hub. Pode completar esta tarefa através do portal Azure, ou programáticamente através do [fornecedor de recursos IoT Hub REST APIs](/rest/api/iothub/iothubresource). Depois de ter associado uma conta de Armazenamento Azure com o seu IoT Hub, o serviço devolve um SAS URI a um dispositivo quando o dispositivo inicia um pedido de upload de ficheiros.

Os ficheiros upload do seu dispositivo para a nuvem com guias [IoT Hub](iot-hub-csharp-csharp-file-upload.md) como fazer fornecem uma passagem completa do processo de upload de ficheiros. Estes guias de como usar o portal Azure para associar uma conta de armazenamento a um hub IoT.

> [!NOTE]
> Os [Azure IoT SDKs](iot-hub-devguide-sdks.md) manuseiam automaticamente a recuperação da assinatura de acesso partilhado URI, o upload do ficheiro e a notificação do IoT Hub de um upload concluído. Se uma firewall bloquear o acesso ao ponto final do Blob Storage mas permitir o acesso ao ponto final do IoT Hub, o processo de upload de ficheiros falha e mostra o seguinte erro para o dispositivo IoT C# SDK:
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> Para que a funcionalidade de upload de ficheiros funcione, o acesso ao ponto final do IoT Hub e ao ponto final do Blob Storage deve estar disponível para o dispositivo.
> 


## <a name="initialize-a-file-upload"></a>Inicialize um upload de ficheiros
O IoT Hub tem um ponto final especificamente para os dispositivos solicitarem um SAS URI para armazenamento para carregar um ficheiro. Para iniciar o processo de upload de ficheiros, o dispositivo envia um pedido DEM para `{iot hub}.azure-devices.net/devices/{deviceId}/files` com o seguinte corpo JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

O IoT Hub devolve os seguintes dados, que o dispositivo utiliza para carregar o ficheiro:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Precatado: inicialize um upload de ficheiros com um GET

> [!NOTE]
> Esta secção descreve a funcionalidade prevadizada de como receber um SAS URI do IoT Hub. Utilize o método POST descrito anteriormente.

O IoT Hub tem dois pontos finais REST para suportar o upload de ficheiros, um para obter o SAS URI para armazenamento e o outro para notificar o hub IoT de um upload completo. O dispositivo inicia o processo de upload de ficheiros enviando um GET para o hub IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . O hub IoT regressa:

* Um SAS URI específico do ficheiro a ser carregado.

* Uma identificação de correlação a ser usada uma vez que o upload esteja concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifique o IoT Hub de um upload de ficheiros concluído

O dispositivo envia o ficheiro para armazenamento utilizando os SDKs de armazenamento Azure. Quando o upload estiver concluído, o dispositivo envia um pedido DEM para `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o seguinte corpo JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` um Boolean que indica se o ficheiro foi carregado com sucesso. O código de estado `statusCode` para é o estado para o upload do ficheiro para armazenamento, e o `statusDescription` corresponde ao `statusCode` .

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre o upload de ficheiros a partir de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de upload de ficheiros

Opcionalmente, quando um dispositivo notifica o IoT Hub de que um upload está completo, o IoT Hub gera uma mensagem de notificação. Esta mensagem contém o nome e o local de armazenamento do ficheiro.

Tal como explicado em [Endpoints](iot-hub-devguide-endpoints.md), o IoT Hub entrega notificações de upload de ficheiros através de um ponto final virado para o serviço (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de receção para notificações de upload de ficheiros é a mesma que para mensagens cloud-to-device e tem o mesmo ciclo de vida da [mensagem](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Cada mensagem recuperada do ponto final de notificação de upload de ficheiros é um registo JSON com as seguintes propriedades:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTimeUtc |Timetamp indicando quando a notificação foi criada. |
| DeviceId |**DeviceId** do dispositivo que carregou o ficheiro. |
| Rio BlobUri |URI do ficheiro carregado. |
| Nome Blob |Nome do ficheiro carregado. |
| LastUpdatedTime |Timetamp indicando quando o ficheiro foi atualizado pela última vez. |
| BlobSizeInBytes |Tamanho do ficheiro carregado. |

**Exemplo.** Este exemplo mostra o corpo de uma mensagem de notificação de upload de ficheiros.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opções de configuração de notificação de upload de ficheiros

Cada hub IoT tem as seguintes opções de configuração para notificações de upload de ficheiros:

| Propriedade | Descrição | Alcance e incumprimento |
| --- | --- | --- |
| **activarFileUploadNotifications** |Controla se as notificações de upload de ficheiros são escritas para o ponto final das notificações de ficheiros. |O Bool. Padrão: Verdade. |
| **fileNotifications.ttlAsIso8601** |TTL padrão para notificações de upload de ficheiros. |ISO_8601 intervalo até 48H (mínimo 1 minuto). Padrão: 1 hora. |
| **fileNotifications.lockDuration** |A duração do bloqueio para a fila de notificações de upload de ficheiros. |5 a 300 segundos (no mínimo 5 segundos). Padrão: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Contagem máxima de entrega para a fila de notificação de upload de ficheiros. |De 1 a 100. Padrão: 100. |

Pode definir estas propriedades no seu hub IoT utilizando o portal Azure CLI ou PowerShell. Para saber como, consulte os tópicos no upload de [ficheiros Configure](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais do hub IoT para operações de gestão e tempo de execução.

* [O estrangulamento e as quotas](iot-hub-devguide-quotas-throttling.md) descrevem as quotas e comportamentos de estrangulamento que se aplicam ao serviço IoT Hub.

* [O dispositivo e serviço Azure IoT lista](iot-hub-devguide-sdks.md) os vários SDKs de linguagem que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* [IoT Hub idioma de consulta](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte do IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a enviar ficheiros a partir de dispositivos que utilizam o IoT Hub, poderá estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Gerir identidades do dispositivo no IoT Hub](iot-hub-devguide-identity-registry.md)

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Use gémeos do dispositivo para sincronizar estado e configurações](iot-hub-devguide-device-twins.md)

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o seguinte tutorial IoT Hub:

* [Como carregar ficheiros de dispositivos para a nuvem com ioT Hub](iot-hub-csharp-csharp-file-upload.md)
