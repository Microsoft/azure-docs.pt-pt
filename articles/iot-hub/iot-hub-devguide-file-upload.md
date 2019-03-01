---
title: Compreender o carregamento de ficheiros do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – utilize a funcionalidade de carregamento de ficheiros do IoT Hub para gerir a carregar ficheiros a partir de um dispositivo para um contentor de BLOBs de armazenamento do Azure.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 7678c50cbda0ea112c737e8230586081efe61081
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008564"
---
# <a name="upload-files-with-iot-hub"></a>Carregar ficheiros com o IoT Hub

Conforme detalhado no [pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) artigo, um dispositivo pode iniciar o carregamento de ficheiros ao enviar uma notificação através de um ponto de final voltado para o dispositivo (**/devices/ {deviceId} / ficheiros**). Quando um dispositivo notifica o IoT Hub que um carregamento estiver concluído, o IoT Hub envia uma mensagem de notificação de carregamento de ficheiros através da **/messages/servicebound/filenotifications** voltado para o serviço de ponto de extremidade.

Em vez de mensagens de agente através do IoT Hub em si, o IoT Hub em vez disso, atua como um dispatcher para uma conta de armazenamento do Azure associada. Um dispositivo de solicita um token de armazenamento do IoT Hub que é específico para o ficheiro que dispositivo pretende carregar. O dispositivo utiliza o URI de SAS para carregar o ficheiro para o armazenamento e, quando o carregamento estiver concluído o dispositivo envia uma notificação de conclusão para o IoT Hub. IoT Hub verifica o carregamento de ficheiros estiver concluído e, em seguida, adiciona uma mensagem de notificação de carregamento de ficheiro para o ponto de final de notificação de ficheiro voltado para o serviço.

Antes de carregar um ficheiro para o IoT Hub a partir de um dispositivo, tem de configurar o seu hub pela [associar um armazenamento do Azure](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) conta ao mesmo.

O dispositivo pode então [inicializar um carregamento](iot-hub-devguide-file-upload.md#initialize-a-file-upload) e, em seguida [notificar o IoT hub](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) quando o carregamento estiver concluído. Opcionalmente, quando um dispositivo notifica o IoT Hub que o carregamento estiver concluído, o serviço pode gerar um [mensagem de notificação](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Quando utilizar

Utilize o carregamento de ficheiros para enviar ficheiros de multimédia e lotes de telemetria grandes carregados por dispositivos ligados intermitentemente ou comprimido para poupar largura de banda.

Consulte a [documentação de orientação do dispositivo-para-cloud comunicação](iot-hub-devguide-d2c-guidance.md) se estiver em dúvida entre o uso de propriedades comunicadas, mensagens do dispositivo para a cloud ou carregamento de ficheiros.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta de armazenamento do Azure com o IoT Hub

Para utilizar a funcionalidade de carregamento de ficheiros, primeiro tem de associar uma conta de armazenamento do Azure para o IoT Hub. Pode concluir esta tarefa através do portal do Azure, ou por meio de programação através de [fornecedor de recursos do IoT Hub REST APIs](/rest/api/iothub/iothubresource). Assim que tiver associado uma conta de armazenamento do Azure a seu IoT Hub, o serviço devolve um URI de SAS para um dispositivo quando o dispositivo é iniciado um pedido de carregamento do ficheiro.

O [carregar ficheiros a partir do seu dispositivo para a cloud com o IoT Hub](iot-hub-csharp-csharp-file-upload.md) guias de procedimentos fornecem instruções completas do processo de carregamento de ficheiros. Estes guias de procedimentos mostram-lhe como utilizar o portal do Azure para associar uma conta de armazenamento com um hub IoT.

> [!NOTE]
> O [do Azure IoT SDKs](iot-hub-devguide-sdks.md) processam automaticamente ao obter o URI de SAS, carregar o ficheiro e notificar o IoT Hub de um carregamento concluído.

## <a name="initialize-a-file-upload"></a>Inicializar o carregamento do ficheiro
IoT Hub tem um ponto de extremidade especificamente para os dispositivos para pedir um URI de SAS para o armazenamento para carregar um ficheiro. Para iniciar o processo de carregamento de ficheiros, o dispositivo envia um pedido POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files` com o corpo JSON seguinte:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub devolve os dados seguintes, que o dispositivo utiliza para carregar o ficheiro:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Preterido: inicializar o carregamento de um ficheiro com um GET

> [!NOTE]
> Esta secção descreve as funcionalidades preteridas para como receber um URI de SAS do IoT Hub. Utilize o método POST descrito anteriormente.

IoT Hub tem dois pontos de extremidade REST para oferecer suporte a carregamento de ficheiros, uma para obter o URI de SAS para armazenamento e outro para notificar o hub IoT de um carregamento concluído. O dispositivo é iniciado o processo de carregamento de ficheiro ao enviar um GET para o hub IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Devolve o hub IoT:

* Um URI de SAS específico para o ficheiro a ser carregado.

* Um ID de correlação para ser utilizado quando o carregamento estiver concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar o IoT Hub de carregamento do ficheiro concluído

O dispositivo carrega o ficheiro para o armazenamento com os SDKs de armazenamento do Azure. Quando o carregamento estiver concluído, o dispositivo envia um pedido POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o corpo JSON seguinte:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` é um valor booleano que indica se o ficheiro foi carregado com êxito. O código de estado `statusCode` é o estado para o carregamento do ficheiro para o armazenamento e o `statusDescription` corresponde ao `statusCode`.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem mais informações sobre como carregar ficheiros a partir de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de carregamento do ficheiro

Opcionalmente, quando um dispositivo notifica o IoT Hub que um carregamento estiver concluído, o IoT Hub gera uma mensagem de notificação. Essa mensagem contém a localização de armazenamento e o nome do ficheiro.

Conforme explicado [pontos de extremidade](iot-hub-devguide-endpoints.md), IoT Hub entregar notificações de carregamento de ficheiros através de um ponto de extremidade de serviço com acesso à (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de receção de notificações de carregamento de ficheiro são iguais aos mensagens cloud para o dispositivo e têm a mesma [ciclo de vida de mensagem](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Cada mensagem obtida a partir do ponto de final de notificação de carregamento do ficheiro é um registo JSON com as seguintes propriedades:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTimeUtc |Timestamp indicando quando a notificação foi criada. |
| DeviceId |**DeviceId** do dispositivo que carregou o ficheiro. |
| BlobUri |URI do arquivo carregado. |
| BlobName |Nome do arquivo carregado. |
| LastUpdatedTime |Timestamp indicando quando o última atualização do arquivo. |
| BlobSizeInBytes |Tamanho do arquivo carregado. |

**Exemplo**. Este exemplo mostra a mensagem de notificação de carregar o corpo de um ficheiro.

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

## <a name="file-upload-notification-configuration-options"></a>Opções de configuração de notificação de carregamento de ficheiro

Cada hub IoT tem as seguintes opções de configuração para o ficheiro a carregar notificações:

| Propriedade | Descrição | Intervalo e predefinido |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controla se as notificações de carregamento de ficheiros são escritas para o ponto de final de notificações do ficheiro. |Bool. Predefinição: VERDADEIRO. |
| **fileNotifications.ttlAsIso8601** |TTL predefinido para as notificações de carregamento do ficheiro. |ISO_8601 intervalo de até 48 horas (no mínimo 1 minuto). Predefinição: 1 hora. |
| **fileNotifications.lockDuration** |Duração do bloqueio para a fila de notificações de carregamento do ficheiro. |5 e 300 segundos (mínimo 5 segundos). Predefinição: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Contagem máxima de entrega para o ficheiro de carregar a fila de notificação. |1 a 100. Predefinição: 100. |

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador do IoT Hub incluem:

* [Pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve vários IoT hub pontos de extremidade para operações de gestão e de tempo de execução.

* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md) descreve as quotas e limitação comportamentos que se aplicam ao serviço IoT Hub.

* [Azure SDKs de dispositivo e de serviços de IoT](iot-hub-devguide-sdks.md) indica o idioma de vários SDKs, pode utilizar ao desenvolver aplicações de dispositivos e de serviços que interagem com o IoT Hub.

* [Linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md) descreve a linguagem de consulta, pode usar para recuperar informações a partir do IoT Hub sobre os seus dispositivos duplos e trabalhos.

* [Suporte para MQTT do IoT Hub](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como carregar ficheiros a partir de dispositivos através do IoT Hub, poderá estar interessado nos seguintes tópicos de guia de programadores de IoT Hub:

* [Gerir identidades de dispositivos no IoT Hub](iot-hub-devguide-identity-registry.md)

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Utilizar dispositivos duplos para sincronizar o estado e configurações](iot-hub-devguide-device-twins.md)

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o tutorial seguinte do IoT Hub:

* [Como carregar ficheiros a partir de dispositivos para a cloud com o IoT Hub](iot-hub-csharp-csharp-file-upload.md)