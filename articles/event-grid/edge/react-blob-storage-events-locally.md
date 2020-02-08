---
title: Reagir a eventos do módulo de armazenamento de BLOBs-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Reagir a eventos do módulo de armazenamento de BLOBs
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086602"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: reagir a eventos de armazenamento de BLOBs em IoT Edge (versão prévia)
Este artigo mostra como implantar o armazenamento de BLOBs do Azure no módulo IoT, que atuaria como um editor de grade de eventos para enviar eventos sobre a criação de BLOB e exclusão de BLOB para a grade de eventos.  

Para uma visão geral do Armazenamento De Blob Azure na Borda IoT, consulte [o Armazenamento Azure Blob no IoT Edge](../../iot-edge/how-to-store-data-blob.md) e as suas características.

> [!WARNING]
> O armazenamento de BLOBs do Azure na integração de IoT Edge com a grade de eventos está em versão prévia

Para concluir este tutorial, você precisará de:

* **Assinatura Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persistir no estado de Linux](persist-state-linux.md) ou Persistir no [Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até o Hub IoT.
1. Selecione **IoT Edge** do menu na secção De Gestão automática de **Dispositivos.** 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **Definir Módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Tem três **passos: Adicionar módulos,** **especificar rotas,** e **rever a implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implementação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: eventgridmodule
   * **Imagem URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **O recipiente cria opções:**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
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
 1. Continue na secção seguinte para adicionar o módulo de Assinante da Grelha de Eventos Azure antes de os colocar juntos.

    >[!IMPORTANT]
    > Neste tutorial, você aprenderá a implementar o módulo De Rede de Eventos para permitir tanto pedidos HTTP/HTTPs, autenticação do cliente desativado. Para cargas de trabalho de produção, recomendamos que você habilite somente solicitações HTTPs e assinantes com autenticação de cliente habilitada. Para obter mais informações sobre como configurar o módulo de Rede de Eventos de forma segura, consulte [segurança e autenticação.](security-authentication.md)
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Implementar módulo de subscritor de rede de eventos IoT Edge

Esta secção mostra-lhe como implementar outro módulo IoT que funcionaria como um manipulador de eventos para o qual os eventos podem ser entregues.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implantação,** selecione **Adicionar** novamente. 
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: assinante
   * **Imagem URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Recipiente Criar opções**: Nenhum
1. Clicar em **Guardar**
1. Continue na próxima seção para adicionar o módulo de armazenamento de BLOBs do Azure

## <a name="deploy-azure-blob-storage-module"></a>Implantar o módulo de armazenamento de BLOBs do Azure

Esta seção mostra como implantar o módulo de armazenamento de BLOBs do Azure, que atuaria como um Publicador de grade de eventos que publica eventos de blob criados e excluídos.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implementação,** selecione **Adicionar**
2. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
3. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: azureblobstorageoniotedge
   * **Imagem URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **O recipiente cria opções:**

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

   > [!IMPORTANT]
   > - O módulo de armazenamento de BLOBs pode publicar eventos usando HTTPS e HTTP. 
   > - Se tiver ativado a autenticação baseada no cliente para eventGrid, certifique-se de atualizar o valor do EVENTGRID_ENDPOINT para permitir https, como este: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`.
   > - Adicione também outra variável ambiental `AllowUnknownCertificateAuthority=true` ao Json acima. Ao falar com o EventGrid sobre HTTPS, o **AllowUnknownCertificateAuthority** permite que o módulo de armazenamento confie em certificados de servidor EventGrid auto-assinados.

4. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que se lembre. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Sem espaços.

   - Substitua `<your storage account key>` por uma tecla base 64 byte64. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Irá utilizar estas credenciais para aceder ao armazenamento de BLOBs a partir de outros módulos.

   - Substitua `<event grid module name>` com o nome do módulo Event Grid.
   - Substitua `<storage mount>` de acordo com o seu sistema operativo de contentores.
     - Para os recipientes Linux, **o meu volume:/blobroot**
     - Para recipientes Windows,**my-volume:C:/BlobRoot**

5. Clicar em **Guardar**
6. Clique **em próximo** para continuar na secção rotas

    > [!NOTE]
    > Se você estiver usando uma VM do Azure como o dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada nas portas de host usadas neste tutorial: 4438, 5888, 8080 e 11002. Para obter instruções sobre a adição da regra, consulte [Como abrir portas a um VM](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Rotas de instalação

Mantenha as rotas predefinidas e selecione **Next** para continuar na secção de revisão

### <a name="review-deployment"></a>Implementação de revisão

1. A seção revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme que vê os seguintes quatro módulos: **$edgeAgent,** **$edgeHub,** **eventgridmodule,** **assinante** e **azureblobstorageoniotedge** que todos estão a ser implantados.
2. Reveja as informações de implementação e, em seguida, selecione **Enviar**.

## <a name="verify-your-deployment"></a>Verificar sua implantação

1. Depois de enviar a implantação, você retornará para a página IoT Edge do Hub IoT.
2. Selecione o **dispositivo IoT Edge** que apontou com a implementação para abrir os seus detalhes.
3. Nos detalhes do dispositivo, verifique se os módulos eventgrid, assinante e azureblobstorageoniotedge estão listados como **especificados na implementação** e **reportados pelo dispositivo**.

   Pode levar alguns minutos para que o módulo seja iniciado no dispositivo e reportado de volta ao Hub IoT. Atualize a página para ver um status atualizado.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicar eventos BlobCreated e BlobDeleted

1. Este módulo cria automaticamente o tópico **MicrosoftStorage**. Verifique se ele existe
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

    > [!IMPORTANT]
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio da chave SAS, a chave SAS especificada anteriormente deverá ser adicionada como um cabeçalho. Daí que o pedido de caracóis seja: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, o pedido de caracóis será: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, terá de criar uma subscrição de Event Grid para o tópico **do MicrosoftStorage.**
    1. Crie blobsubscription. JSON com o conteúdo a seguir. Para mais detalhes sobre a carga útil, consulte a nossa [documentação da API](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > A propriedade **endpointType** especifica que o assinante é um **Webhook**.  O **endpointUrl** especifica o URL no qual o assinante está a ouvir eventos. Essa URL corresponde ao exemplo de Azure function que você implantou anteriormente.

    2. Execute o comando a seguir para criar uma assinatura para o tópico. Confirme se vê que o código de estado HTTP está `200 OK`.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio da chave SAS, a chave SAS especificada anteriormente deverá ser adicionada como um cabeçalho. Daí que o pedido de caracóis seja: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, o pedido de caracóis será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

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
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio da chave SAS, a chave SAS especificada anteriormente deverá ser adicionada como um cabeçalho. Daí que o pedido de caracóis seja: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, o pedido de caracóis será: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Baixe [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [conecte-o ao seu armazenamento local](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

### <a name="verify-blobcreated-event-delivery"></a>Verificar a entrega de eventos do BlobCreated

1. Carregue arquivos como BLOBs de blocos no armazenamento local de Gerenciador de Armazenamento do Azure e o módulo publicará automaticamente os eventos de criação. 
2. Confira os logs do assinante para criar evento. Siga os passos para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:

    ```json
            Received Event:
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
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verificar a entrega de eventos do BlobDeleted

1. Exclua os BLOBs do armazenamento local usando Gerenciador de Armazenamento do Azure e o módulo publicará automaticamente os eventos de exclusão. 
2. Confira os logs do assinante para excluir evento. Siga os passos para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:
    
    ```json
            Received Event:
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
    ```

Parabéns! Você concluiu o tutorial. As seções a seguir fornecem detalhes sobre as propriedades do evento.

### <a name="event-properties"></a>Propriedades do evento

Aqui está a lista de propriedades de eventos com suporte e seus tipos e descrições. 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. A Grelha de Eventos fornece este valor. |
| subject | string | Caminho definido pela editora para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta fonte do evento. |
| eventoTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de armazenamento de BLOBs. |
| dataVersion | string | A versão esquema do objeto de dados. A editora define a versão do esquema. |
| metadataVersion | string | A versão de esquema dos metadados do evento. A Grelha de Eventos define o esquema das propriedades de alto nível. A Grelha de Eventos fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. Pode ser um dos seguintes valores: <ul><li>BlobCreated - valores permitidos são: `PutBlob` e `PutBlockList`</li><li>BlobDeleted - valores permitidos são `DeleteBlob`, `DeleteAfterUpload` e `AutoDelete`. <p>O evento `DeleteAfterUpload` é gerado quando a bolha é automaticamente eliminada porque a propriedade desejada a eliminar AfterUpload é definida como verdadeira. </p><p>`AutoDelete` evento é gerado quando a bolha é automaticamente eliminada porque o valor de propriedade desejado afterAfterMinutes expirou.</p></li></ul>|
| clientRequestId | string | um PEDIDO de pedido fornecido pelo cliente para a operação API de armazenamento. Este ID pode ser usado para correlacionar com os registos de diagnóstico do Armazenamento Azure utilizando o campo "cliente-request-id" nos registos, e pode ser fornecido em pedidos de cliente utilizando o cabeçalho "x-ms-cliente-request-id". Para mais detalhes, consulte [formato de log](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID de pedido gerado pelo serviço para a operação API de armazenamento. Pode ser usado para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "solicitação-ID-cabeçalho" nos logs e é retornado de iniciar chamada à API no cabeçalho ' x-MS-Request-ID '. Ver [Formato de Registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | número inteiro | O tamanho do blob em bytes. |
| blobType | string | O tipo de BLOB. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para a bolha. <br>Se o cliente utilizar um API Blob REST, então o url tem esta estrutura: *\<nome de conta de armazenamento\>.blob.core.windows.net/\<nome de contentor\>/\<\>de nome de ficheiro* . <br>Se o cliente utilizar uma API de depósito de data Lake, então o url tem esta estrutura: *\<nome de conta de armazenamento\>.dfs.core.windows.net/\<nome do sistema de ficheiros\>/\<\>de nome de ficheiro* . |


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos da documentação do armazenamento de BLOBs:

- [Filtrar eventos de armazenamento blob](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Práticas recomendadas para consumir eventos de armazenamento blob](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Neste tutorial, você publicou eventos criando ou excluindo BLOBs em um armazenamento de BLOBs do Azure. Consulte os outros tutoriais para saber como encaminhar eventos para a nuvem (Hub de eventos do Azure ou Hub IoT do Azure): 

- [Eventos avançados para a Grelha de Eventos De Azure](forward-events-event-grid-cloud.md)
- [Eventos avançados para o Hub Azure IoT](forward-events-iothub.md)
