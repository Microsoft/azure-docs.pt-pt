---
title: Compreenda o upload de ficheiros Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvimento - utilize a funcionalidade de upload de ficheiros do IoT Hub para gerir o upload de ficheiros de um dispositivo para um recipiente de armazenamento Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom: mqtt
ms.openlocfilehash: 35337a99706f25d62964e08a5b16cd8e81f315c6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730285"
---
# <a name="upload-files-with-iot-hub"></a>Carregar ficheiros com o Hub IoT

Conforme detalhado no artigo de [pontos finais do IoT Hub,](iot-hub-devguide-endpoints.md) um dispositivo pode iniciar um upload de ficheiro enviando uma notificação através de um ponto final virado para dispositivos **(/dispositivos/{deviceId}/ficheiros**). Quando um dispositivo notifica o IoT Hub de que um upload está completo, o IoT Hub envia uma mensagem de notificação de envio de ficheiros através do ponto final virado para o serviço **/mensagens/notificações de serviço/notificações de ficheiros.**

Em vez de intermediar mensagens através do próprio IoT Hub, o IoT Hub funciona como despachante para uma conta de Armazenamento Azure associada. Um dispositivo solicita um token de armazenamento do IoT Hub que é específico do ficheiro que o dispositivo pretende carregar. O dispositivo utiliza o SAS URI para fazer o upload do ficheiro para armazenamento, e quando o upload estiver concluído, o dispositivo envia uma notificação de conclusão para o IoT Hub. O IoT Hub verifica que o upload do ficheiro está completo e, em seguida, adiciona uma mensagem de notificação de envio de ficheiros ao ponto final de notificação de ficheiro virado para o serviço.

Antes de enviar um ficheiro para o IoT Hub a partir de um dispositivo, tem de configurar o seu hub [associando-lhe uma conta de Armazenamento Azure.](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)

O seu dispositivo pode então [inicializar um upload](iot-hub-devguide-file-upload.md#initialize-a-file-upload) e, em seguida, notificar o [hub IoT](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) quando o upload estiver concluído. Opcionalmente, quando um dispositivo notifica o IoT Hub de que o upload está completo, o serviço pode gerar uma [mensagem de notificação](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Quando utilizar

Utilize o upload de ficheiros para enviar ficheiros de mídia e grandes lotes de telemetria carregados por dispositivos intermitentemente ligados ou comprimidos para salvar a largura de banda.

Consulte a orientação de [comunicação Dispositivo-nuvem,](iot-hub-devguide-d2c-guidance.md) em caso de dúvida entre a utilização de propriedades reportadas, mensagens dispositivo-nuvem ou upload de ficheiros.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta de Armazenamento Azure com o IoT Hub

Para utilizar a funcionalidade de upload de ficheiros, tem primeiro de ligar uma conta de Armazenamento Azure ao IoT Hub. Pode completar esta tarefa através do portal Azure, ou programáticamente através do fornecedor de [recursos Do IoT Hub REST APIs](/rest/api/iothub/iothubresource). Depois de associar uma conta de Armazenamento Azure ao seu Hub IoT, o serviço devolve um SAS URI a um dispositivo quando o dispositivo inicia um pedido de upload de ficheiros.

Os [ficheiros Upload do seu dispositivo para a nuvem com guias IoT Hub](iot-hub-csharp-csharp-file-upload.md) fornecem uma passagem completa do processo de upload de ficheiros. Estes guias de como-fazer mostram-lhe como usar o portal Azure para associar uma conta de armazenamento com um hub IoT.

> [!NOTE]
> Os [SDKs Azure IoT](iot-hub-devguide-sdks.md) lidam automaticamente com a recuperação do SAS URI, fazendo o upload do ficheiro e notificando o IoT Hub de um upload concluído.

## <a name="initialize-a-file-upload"></a>Inicializar um upload de ficheiro
O IoT Hub tem um ponto final especificamente para os dispositivos solicitarem um SAS URI para armazenamento para fazer o upload de um ficheiro. Para iniciar o processo de upload de `{iot hub}.azure-devices.net/devices/{deviceId}/files` ficheiros, o dispositivo envia um pedido post para com o seguinte corpo JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

O IoT Hub devolve os seguintes dados, que o dispositivo utiliza para fazer o upload do ficheiro:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Precated: inicializar um upload de ficheiro com um GET

> [!NOTE]
> Esta secção descreve a funcionalidade depreciada para como receber um SAS URI do IoT Hub. Utilize o método POST descrito anteriormente.

O IoT Hub tem dois pontos finais REST para suportar o upload de ficheiros, um para obter o SAS URI para armazenamento e o outro para notificar o centro IoT de um upload concluído. O dispositivo inicia o processo de upload de ficheiros `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`enviando um GET para o hub IoT em . O hub ioT retorna:

* Um SAS URI específico do ficheiro a ser carregado.

* Uma identificação de correlação a ser utilizada uma vez que o upload esteja concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifique o IoT Hub de um upload de ficheiro completo

O dispositivo envia o ficheiro para armazenamento utilizando os SDKs de Armazenamento Azure. Quando o upload estiver concluído, o `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` dispositivo envia um pedido post para com o seguinte corpo JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor `isSuccess` de é um Boolean que indica se o ficheiro foi carregado com sucesso. O código `statusCode` de estado para é o estado do `statusDescription` upload do `statusCode`ficheiro para armazenamento, e o corresponde ao .

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre o upload de ficheiros a partir de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de upload de ficheiros

Opcionalmente, quando um dispositivo notifica o IoT Hub de que um upload está completo, o IoT Hub gera uma mensagem de notificação. Esta mensagem contém o nome e a localização de armazenamento do ficheiro.

Como explicado em [Endpoints,](iot-hub-devguide-endpoints.md)o IoT Hub entrega notificações de upload de ficheiros através de um ponto final virado para o serviço **(/mensagens/notificações de**envio de ficheiros / serviços/ notificações de upload de ficheiros) como mensagens. A semântica recebida para notificações de upload de ficheiros é a mesma que para mensagens cloud-to-device e tem o mesmo ciclo de vida de [mensagem](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Cada mensagem obtida a partir do ponto final de notificação de envio de ficheiros é um registo JSON com as seguintes propriedades:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTimeUtc |Carimbo de tempo indicando quando a notificação foi criada. |
| DeviceId |**DispositivoId** do dispositivo que carregou o ficheiro. |
| BlobUri |URI do ficheiro carregado. |
| BlobName |Nome do ficheiro enviado. |
| LastUpdatedTime |Carimbo de tempo indicando quando o ficheiro foi atualizado pela última vez. |
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

| Propriedade | Descrição | Alcance e padrão |
| --- | --- | --- |
| **ativarNotificas FileUpload** |Controla se as notificações de upload de ficheiros estão escritas no ponto final das notificações do ficheiro. |Bool, bool. Padrão: Verdadeiro. |
| **ficheiroNotificas.ttlAsIso8601** |TTL padrão para notificações de upload de ficheiros. |intervalo ISO_8601 até 48H (mínimo 1 minuto). Padrão: 1 hora. |
| **ficheiroSNotificações.lockDura** |Bloqueie a duração da fila de notificações de upload de ficheiros. |5 a 300 segundos (mínimo 5 segundos). Predefinição: 60 segundos. |
| **ficheiroSNotificações.maxDeliveryCount** |Contagem máxima de entrega para a fila de notificação de upload de ficheiros. |1 a 100. Padrão: 100. |

Pode definir estas propriedades no seu hub IoT utilizando o portal Azure, Azure CLI ou PowerShell. Para saber como, consulte os tópicos sob upload de [ficheiros Configure](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais do hub IoT para operações de execução e gestão.

* [O estrangulamento e](iot-hub-devguide-quotas-throttling.md) as quotas descrevem as quotas e comportamentos de estrangulamento que se aplicam ao serviço IoT Hub.

* O [dispositivo e o serviço Azure IoT](iot-hub-devguide-sdks.md) listam os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A linguagem de [consulta ioT Hub](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a carregar ficheiros a partir de dispositivos que usam o IoT Hub, pode estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Gerir identidades do dispositivo no IoT Hub](iot-hub-devguide-identity-registry.md)

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Use gémeos dispositivos para sincronizar estado e configurações](iot-hub-devguide-device-twins.md)

* [Invoque um método direto num dispositivo](iot-hub-devguide-direct-methods.md)

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o seguinte tutorial IoT Hub:

* [Como carregar ficheiros de dispositivos para a nuvem com ioT Hub](iot-hub-csharp-csharp-file-upload.md)