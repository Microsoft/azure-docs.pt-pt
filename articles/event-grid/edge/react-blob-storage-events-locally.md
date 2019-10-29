---
title: Reagir a eventos do módulo de armazenamento de BLOBs-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Reagir a eventos do módulo de armazenamento de BLOBs
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 10/02/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d61dc9d49053cb8a125362ac492f354fb64b79a5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992174"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: reagir a eventos de armazenamento de BLOBs em IoT Edge (versão prévia)

Este artigo mostra como reagir localmente aos eventos de criação de BLOB e exclusão de BLOB em IoT Edge usando a grade de eventos.

Cenários comuns de eventos de armazenamento de BLOBs incluem processamento de imagem ou vídeo, indexação de pesquisa ou qualquer fluxo de trabalho orientado a arquivo. Os carregamentos de arquivos assíncronos são uma ótima opção para eventos. Quando as alterações não são frequentes, mas seu cenário requer capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficiente.

Para obter uma visão geral do armazenamento de BLOBs do Azure no IoT Edge, consulte [armazenamento de BLOBs do Azure em IOT Edge](../../iot-edge/how-to-store-data-blob.md) e seus recursos.

> [!WARNING]
> O armazenamento de BLOBs do Azure na integração de IoT Edge com a grade de eventos está em versão prévia

Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Hub IOT do Azure e IOT Edge dispositivo** -siga as etapas no guia de início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [persistir estado no Linux](persist-state-linux.md) ou [persistir o estado no Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até o Hub IoT.
1. Selecione **IOT Edge** no menu da seção **Gerenciamento de dispositivo automático** . 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **Definir Módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados, como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Ele tem três etapas: **Adicionar módulos**, **especificar rotas**e **examinar a implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: eventgridmodule
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contêiner**:

    ```json
        {
          "Env": [
           "inbound:serverAuth:tlsPolicy=enabled",
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Clicar em **Guardar**
 1. Continue na próxima seção para adicionar o módulo Azure Functions

    >[!IMPORTANT]
    > Neste tutorial, você implantará o módulo de grade de eventos para permitir solicitações HTTP/HTTPs, autenticação de cliente desabilitada e permitir assinantes HTTP. Para cargas de trabalho de produção, recomendamos que você habilite somente solicitações HTTPs e assinantes com autenticação de cliente habilitada. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](security-authentication.md).
    

## <a name="deploy-azure-function-iot-edge-module"></a>Implantar o módulo IoT Edge do Azure function

Esta seção mostra como implantar o módulo Azure Functions IoT, que atuaria como um assinante de grade de eventos no qual os eventos podem ser entregues.

>[!IMPORTANT]
>Nesta seção, você implantará um exemplo de módulo de assinatura baseado em função do Azure. Naturalmente, pode ser qualquer módulo IoT personalizado que possa escutar solicitações HTTP POST.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar** novamente. 
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: assinante
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **Opções de criação de contêiner**:

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. Clicar em **Guardar**
1. Continue na próxima seção para adicionar o módulo de armazenamento de BLOBs do Azure

> [!NOTE]
> O módulo de armazenamento de BLOBs publica eventos usando HTTP. Confirme se o módulo de grade de eventos permite solicitações HTTP e HTTPS com a seguinte configuração: `inbound:serverAuth:tlsPolicy=enabled`.

## <a name="deploy-azure-blob-storage-module"></a>Implantar o módulo de armazenamento de BLOBs do Azure

Esta seção mostra como implantar o módulo de armazenamento de BLOBs do Azure, que atuaria como um Publicador de grade de eventos que publica eventos de blob criados e excluídos.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
2. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
3. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: azureblobstorageoniotedge
   * **URI da imagem**: MCR.Microsoft.com/Azure-Blob-Storage:1.2.2-Preview
   * **Opções de criação de contêiner**:

```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
```

4. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome que você possa lembrar. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Sem espaços.

   - Substitua `<your storage account key>` por uma chave Base64 de 64 bytes. Você pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você usará essas credenciais para acessar o armazenamento de blobs de outros módulos.

   - Substitua `<event grid module name>` pelo nome do seu módulo de grade de eventos.
   - Substitua `<storage mount>` de acordo com o sistema operacional do contêiner.
     - Para contêineres do Linux, **meu-volume:/blobroot**
     - Para contêineres do Windows,**meu-volume: C:/BlobRoot**

5. Clicar em **Guardar**
6. Clique em **Avançar** para continuar na seção rotas

 ### <a name="setup-routes"></a>Rotas de instalação

Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="review-deployment"></a>Examinar a implantação

1. A seção revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme que você vê os quatro módulos a seguir: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **Subscriber** e **azureblobstorageoniotedge** que todos estão sendo implantados.
2. Examine as informações de implantação e, em seguida, selecione **Enviar**.

## <a name="verify-your-deployment"></a>Verificar sua implantação

1. Depois de enviar a implantação, você retornará para a página IoT Edge do Hub IoT.
2. Selecione o **dispositivo de IOT Edge** de destino com a implantação para abrir seus detalhes.
3. Nos detalhes do dispositivo, verifique se os módulos eventgridmodule, subscriber e azureblobstorageoniotedge estão listados como ambos **especificados na implantação** e **relatados pelo dispositivo**.

   Pode levar alguns minutos para que o módulo seja iniciado no dispositivo e reportado de volta ao Hub IoT. Atualize a página para ver um status atualizado.

## <a name="publish-created-and-deleted-events"></a>Publicar eventos criados e excluídos

1. Esse módulo cria automaticamente o tópico **MicrosoftStorage**. Verifique se ele existe
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

2. Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, você precisará criar uma assinatura de grade de eventos para o tópico **MicrosoftStorage** .
    1. Crie blobsubscription. JSON com o conteúdo a seguir. Para obter detalhes sobre a carga, consulte nossa [documentação de API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A propriedade **EndpointType** especifica que o assinante é um **webhook**.  O **endpointUrl** especifica a URL na qual o assinante está escutando eventos. Essa URL corresponde ao exemplo de Azure function que você implantou anteriormente.

    2. Execute o comando a seguir para criar uma assinatura para o tópico. Confirme que você vê o código de status HTTP `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

    ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

2. Baixar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) e [conectá-lo ao armazenamento local](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

### <a name="verify-blobcreated-event-delivery"></a>Verificar a entrega de eventos do BlobCreated

1. Carregue arquivos como BLOBs de blocos no armazenamento local de Gerenciador de Armazenamento do Azure e o módulo publicará automaticamente os eventos de criação. 
2. Confira os logs do assinante para criar evento. Siga as etapas para [verificar a entrega de eventos](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:

    ```json
            Received event data [
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verificar a entrega de eventos do BlobDeleted

1. Exclua os BLOBs do armazenamento local usando Gerenciador de Armazenamento do Azure e o módulo publicará automaticamente os eventos de exclusão. 
2. Confira os logs do assinante para excluir evento. Siga as etapas para [verificar a entrega de eventos](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:
    
    ```json
            Received event data [
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

Parabéns! Você concluiu o tutorial. As seções a seguir fornecem detalhes sobre as propriedades do evento.

### <a name="event-properties"></a>Propriedades do evento

Aqui está a lista de propriedades de eventos com suporte e seus tipos e descrições. 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. A grade de eventos fornece esse valor. |
| Assunto | string | Caminho definido pelo Publicador para a entidade do evento. |
| EventType | string | Um dos tipos de evento registrados para essa origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| dado | objeto | Dados de evento de armazenamento de BLOBs. |
| versão de | string | A versão do esquema do objeto de dados. O Publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. A grade de eventos define o esquema das propriedades de nível superior. A grade de eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. Pode ser um dos seguintes valores: <ul><li>BlobCreated-os valores permitidos são: `PutBlob` e `PutBlockList`</li><li>BlobDeleted-os valores permitidos são `DeleteBlob`, `DeleteAfterUpload` e `AutoDelete`. <p>O evento `DeleteAfterUpload` é gerado quando o blob é excluído automaticamente porque a propriedade desejada deleteAfterUpload está definida como true. </p><p>`AutoDelete` evento é gerado quando o blob é excluído automaticamente porque o valor da propriedade desejada deleteAfterMinutes expirou.</p></li></ul>|
| clientRequestId | string | uma ID de solicitação fornecida pelo cliente para a operação da API de armazenamento. Essa ID pode ser usada para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "Client-Request-ID" nos logs e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-MS-Client-Request-ID". Para obter detalhes, consulte [formato de log](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID da solicitação gerada pelo serviço para a operação da API de armazenamento. Pode ser usado para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "solicitação-ID-cabeçalho" nos logs e é retornado de iniciar chamada à API no cabeçalho ' x-MS-Request-ID '. Consulte [formato de log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | número inteiro | O tamanho do blob em bytes. |
| BlobType | string | O tipo de BLOB. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para o blob. <br>Se o cliente usar uma API REST de BLOB, a URL terá essa estrutura: *\<storage-Account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Se o cliente usar uma API REST Data Lake Storage, a URL terá essa estrutura: *\<storage-Account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos da documentação do armazenamento de BLOBs:

- [Filtrar eventos de armazenamento de BLOBs](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Práticas recomendadas para consumo de eventos de armazenamento de BLOBs](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Neste tutorial, você publicou eventos criando ou excluindo BLOBs em um armazenamento de BLOBs do Azure. Consulte os outros tutoriais para saber como encaminhar eventos para a nuvem (Hub de eventos do Azure ou Hub IoT do Azure): 

- [Encaminhar eventos para a grade de eventos do Azure](forward-events-event-grid-cloud.md)
- [Encaminhar eventos para o Hub IoT do Azure](forward-events-iothub.md)