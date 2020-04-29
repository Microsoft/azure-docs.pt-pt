---
title: Reagir a eventos de módulos de armazenamento blob - Azure Event Grid IoT Edge [ Microsoft Docs
description: Reagir a eventos de módulos de armazenamento blob
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77086602"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: Reagir a eventos de armazenamento de blob em IoT Edge (Pré-visualização)
Este artigo mostra-lhe como implementar o Armazenamento De Blob Azure no módulo IoT, que funcionaria como uma editora da Event Grid para enviar eventos sobre criação de Blob e eliminação de Blob para a Rede de Eventos.  

Para uma visão geral do Armazenamento De Blob Azure na Borda IoT, consulte [o Armazenamento Azure Blob no IoT Edge](../../iot-edge/how-to-store-data-blob.md) e as suas características.

> [!WARNING]
> Armazenamento azure blob na integração de IoT Edge com Grelha de Eventos está em pré-visualização

Para completar este tutorial, necessitará:

* **Assinatura Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implementar módulo De Borda da Grelha de Eventos IoT

Existem várias formas de implementar módulos para um dispositivo IoT Edge e todos eles trabalham para a Grelha de Eventos Azure em IoT Edge. Este artigo descreve os passos para implantar a Grelha de Eventos na Borda IoT do portal Azure.

>[!NOTE]
> Neste tutorial, irá implantar o módulo Da Grelha de Eventos sem persistência. Significa que quaisquer tópicos e subscrições que criar neste tutorial serão eliminados quando reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persistir no estado de Linux](persist-state-linux.md) ou Persistir no [Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que instale o módulo Da Grelha de Eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione o seu dispositivo IoT Edge

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Navegue para o seu Hub IoT.
1. Selecione **IoT Edge** do menu na secção De Gestão automática de **Dispositivos.** 
1. Clique na identificação do dispositivo alvo a partir da lista de dispositivos
1. Selecione **Módulos de Conjunto**. Mantenha a página aberta. Continuará com os passos na próxima secção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação, em vez de construir manualmente o documento JSON.  Tem três **passos: Adicionar módulos,** **especificar rotas,** e **rever a implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implementação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Forneça o nome, imagem, recipiente criar opções do recipiente:

   * **Nome**: eventgridmodule
   * **Imagem URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
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

 1. Clique em **Guardar**
 1. Continue na secção seguinte para adicionar o módulo de Assinante da Grelha de Eventos Azure antes de os colocar juntos.

    >[!IMPORTANT]
    > Neste tutorial, você aprenderá a implementar o módulo De Rede de Eventos para permitir tanto pedidos HTTP/HTTPs, autenticação do cliente desativado. Para cargas de trabalho de produção, recomendamos que apenas permita pedidos de HTTPs e assinantes com autenticação do cliente ativado. Para obter mais informações sobre como configurar o módulo de Rede de Eventos de forma segura, consulte [segurança e autenticação.](security-authentication.md)
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Implementar módulo de subscritor de rede de eventos IoT Edge

Esta secção mostra-lhe como implementar outro módulo IoT que funcionaria como um manipulador de eventos para o qual os eventos podem ser entregues.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implantação,** selecione **Adicionar** novamente. 
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Fornecer o nome, imagem e recipiente criar opções do recipiente:

   * **Nome**: assinante
   * **Imagem URI:**`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Recipiente Criar opções**: Nenhum
1. Clique em **Guardar**
1. Continue na secção seguinte para adicionar o módulo de armazenamento De Blob Azure

## <a name="deploy-azure-blob-storage-module"></a>Implementar módulo de armazenamento Azure Blob

Esta secção mostra-lhe como implementar o módulo de armazenamento De Blob Azure, que funcionaria como uma editora da Event Grid publicando eventos criados e apagados.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implementação,** selecione **Adicionar**
2. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
3. Fornecer o nome, imagem e recipiente criar opções do recipiente:

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
   > - O módulo de armazenamento Blob pode publicar eventos usando HTTPS e HTTP. 
   > - Se tiver ativado a autenticação baseada no cliente para eventGrid, certifique-se de `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`atualizar o valor do EVENTGRID_ENDPOINT permitir https, assim: .
   > - Adicione também outra `AllowUnknownCertificateAuthority=true` variável ambiental ao Json acima. Ao falar com o EventGrid sobre HTTPS, o **AllowUnknownCertificateAuthority** permite que o módulo de armazenamento confie em certificados de servidor EventGrid auto-assinados.

4. Atualize o JSON que copiou com as seguintes informações:

   - Substitua-o `<your storage account name>` por um nome de que se lembre. Os nomes das contas devem ter 3 a 24 caracteres de comprimento, com letras minúsculas e números. Sem espaços.

   - Substitua-a `<your storage account key>` por uma tecla base 64 byte64. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você usará estas credenciais para aceder ao armazenamento de bolhas de outros módulos.

   - Substitua-o `<event grid module name>` pelo nome do módulo Event Grid.
   - Substitua de `<storage mount>` acordo com o seu sistema de funcionamento do contentor.
     - Para os recipientes Linux, **o meu volume:/blobroot**
     - Para recipientes Windows,**my-volume:C:/BlobRoot**

5. Clique em **Guardar**
6. Clique **em próximo** para continuar na secção rotas

    > [!NOTE]
    > Se estiver a utilizar um VM Azure como dispositivo de borda, adicione uma regra de entrada para permitir o tráfego de entrada nas portas hospedeiras utilizadas neste tutorial: 4438, 5888, 8080 e 11002. Para obter instruções sobre a adição da regra, consulte [Como abrir portas a um VM](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Rotas de configuração

Mantenha as rotas predefinidas e selecione **Next** para continuar na secção de revisão

### <a name="review-deployment"></a>Revisão da implantação

1. A secção de revisão mostra-lhe o manifesto de implantação jSON que foi criado com base nas suas seleções na secção anterior. Confirme que vê os seguintes quatro módulos: **$edgeAgent,** **$edgeHub,** **eventgridmodule,** **assinante** e **azureblobstorageoniotedge** que todos estão a ser implantados.
2. Reveja as informações de implementação e, em seguida, selecione **Enviar**.

## <a name="verify-your-deployment"></a>Verifique a sua implantação

1. Depois de submeter a implementação, volte à página IoT Edge do seu hub IoT.
2. Selecione o **dispositivo IoT Edge** que apontou com a implementação para abrir os seus detalhes.
3. Nos detalhes do dispositivo, verifique se os módulos eventgrid, assinante e azureblobstorageoniotedge estão listados como **especificados na implementação** e **reportados pelo dispositivo**.

   Pode levar alguns momentos para o módulo ser iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicar eventos BlobCreated e BlobDeleted

1. Este módulo cria automaticamente o tópico **MicrosoftStorage**. Verifique se existe
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
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através da chave SAS, a tecla SAS especificada anteriormente deve ser adicionada como cabeçalho. Daí o pedido de caracóis será:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, o pedido de caracóis será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Os assinantes podem inscrever-se para eventos publicados num tópico. Para receber qualquer evento, terá de criar uma subscrição de Event Grid para o tópico **do MicrosoftStorage.**
    1. Crie blobsubscription.json com o seguinte conteúdo. Para mais detalhes sobre a carga útil, consulte a nossa [documentação da API](api.md)

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
       > A propriedade **endpointType** especifica que o assinante é um **Webhook**.  O **endpointUrl** especifica o URL no qual o assinante está a ouvir eventos. Este URL corresponde à amostra da Função Azure que implementou anteriormente.

    2. Executar o seguinte comando para criar uma subscrição para o tópico. Confirme se vê o `200 OK`código de estado HTTP .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através da chave SAS, a tecla SAS especificada anteriormente deve ser adicionada como cabeçalho. Daí o pedido de caracóis será:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, o pedido de caracóis será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. Http Status Code of 200 OK deve ser devolvido.

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
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através da chave SAS, a tecla SAS especificada anteriormente deve ser adicionada como cabeçalho. Daí o pedido de caracóis será:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, o pedido de caracóis será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Baixe [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [conecte-o ao seu armazenamento local](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

### <a name="verify-blobcreated-event-delivery"></a>Verificar a entrega do evento BlobCreated

1. Faça upload de ficheiros como blocos blobs para o armazenamento local do Azure Storage Explorer, e o módulo publicará automaticamente criar eventos. 
2. Confira os registos de subscritores para criar evento. Siga os passos para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída da amostra:

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

### <a name="verify-blobdeleted-event-delivery"></a>Verificar a entrega do evento BlobDeleted

1. Elimine as bolhas do armazenamento local utilizando o Azure Storage Explorer, e o módulo publicará automaticamente apagar eventos. 
2. Confira os registos de assinantes para eliminar o evento. Siga os passos para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída da amostra:
    
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

Parabéns! Completou o tutorial. As seguintes secções fornecem detalhes sobre as propriedades do evento.

### <a name="event-properties"></a>Propriedades do evento

Aqui está a lista de propriedades de eventos suportados e seus tipos e descrições. 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento de armazenamento blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que desencadeou o evento. Pode ser um dos seguintes valores: <ul><li>BlobCreated - valores `PutBlob` permitidos são: e`PutBlockList`</li><li>BlobDeleted - valores `DeleteAfterUpload` `AutoDelete`permitidos são, `DeleteBlob`e . <p>O `DeleteAfterUpload` evento é gerado quando a bolha é automaticamente eliminada porque a propriedade desejada a eliminar AfterUpload é definida como verdadeira. </p><p>`AutoDelete`evento é gerado quando a bolha é automaticamente eliminada porque eliminar AfterMinutes valor de propriedade desejado expirou.</p></li></ul>|
| clientRequestId | string | um PEDIDO de pedido fornecido pelo cliente para a operação API de armazenamento. Este ID pode ser usado para correlacionar com os registos de diagnóstico do Armazenamento Azure utilizando o campo "cliente-request-id" nos registos, e pode ser fornecido em pedidos de cliente utilizando o cabeçalho "x-ms-cliente-request-id". Para mais detalhes, consulte [formato de log](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID de pedido gerado pelo serviço para a operação API de armazenamento. Pode ser usado para correlacionar com os registos de diagnóstico do Armazenamento Azure utilizando o campo "request-id-header" nos registos e é devolvido a partir do início da chamada da API no cabeçalho 'x-ms-request-id'. Ver [Formato de Registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que pode utilizar para realizar operações condicionalmente. |
| conteúdoType | string | O tipo de conteúdo especificado para a bolha. |
| conteúdoComprimento | número inteiro | O tamanho da bolha em bytes. |
| blobType | string | O tipo de bolha. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para a bolha. <br>Se o cliente utilizar uma API Blob REST, então o url tem esta estrutura: * \<nome de\>arma de armazenamento .blob.core.windows.net/\<nome\>/\<\>de ficheiro de nome de contentor*. <br>Se o cliente utilizar uma API REST De armazenamento de data Lake, então o url tem esta estrutura: * \<\>nome de arma de armazenamento .dfs.core.windows.net/\<nome\>/\<\>de ficheiro do ficheiro do ficheiro*. |


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos da documentação de Armazenamento Blob:

- [Filtrar eventos de armazenamento blob](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Práticas recomendadas para consumir eventos de armazenamento blob](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Neste tutorial, publicou eventos criando ou apagando bolhas num Armazém Azure Blob. Veja os outros tutoriais para aprender a encaminhar eventos para a nuvem (Azure Event Hub ou Azure IoT Hub): 

- [Reencaminhar eventos para o Azure Event Grid](forward-events-event-grid-cloud.md)
- [Reencaminhar eventos para o Hub IoT do Azure](forward-events-iothub.md)
