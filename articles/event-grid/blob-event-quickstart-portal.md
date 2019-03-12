---
title: Enviar eventos de armazenamento de BLOBs para o ponto final web - portal | Documentos da Microsoft
description: Utilize o Azure Event Grid e o portal do Azure para criar a conta de armazenamento de Blobs e subscrever os respetivos eventos. Envie eventos para um Webhook.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 10/17/2018
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ef821bd4c5abfb6fe89e585060b23b999b2bfa11
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547049"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Início rápido: Encaminhar eventos de armazenamento de BLOBs para o ponto final da web com o portal do Azure

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, irá utilizar o portal do Azure para criar uma conta de armazenamento de Blobs, subscrever eventos nesse armazenamento de blobs e acionar um evento para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

![Ver resultados](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Para criar um armazenamento de Blobs, selecione **Criar um recurso**. 

   ![Criar um recurso](./media/blob-event-quickstart-portal/create-resource.png)

1. Selecione o **Armazenamento** para filtrar as opções disponíveis e selecione **Conta de armazenamento - blob, ficheiro, tabela, fila**.

   ![Selecionar armazenamento](./media/blob-event-quickstart-portal/create-storage.png)

1. Para subscrever eventos, tem de criar uma conta de armazenamento de Blobs ou uma conta de armazenamento de fins gerais v2. Para obter mais informações, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

   ![Passos de início](./media/blob-event-quickstart-portal/provide-blob-values.png)

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever aos eventos do armazenamento de Blobs, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Subscrever a conta de armazenamento de Blobs

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. No portal, selecione o armazenamento de blobs e selecione **Eventos**.

   ![Selecionar eventos](./media/blob-event-quickstart-portal/select-events.png)

1. Para enviar eventos para a sua aplicação de visualizador, utilize um webhook para o ponto final. Selecione **Mais Opções**, e **Webhook**.

   ![Selecionar webhook](./media/blob-event-quickstart-portal/select-web-hook.png)

1. A subscrição de evento é pré-preenchida com os valores do armazenamento de Blobs. Para o ponto final do webhook, indique o URL da sua aplicação Web e adicione `api/updates` ao URL da home page. Nomeie a subscrição. Quando terminar, selecione **Criar**.

   ![Selecionar registos](./media/blob-event-quickstart-portal/create-subscription.png)

1. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

   ![Ver evento da subscrição](./media/blob-event-quickstart-portal/view-subscription-event.png)

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Aciona um evento para o armazenamento de Blobs ao carregar um ficheiro. O ficheiro não precisa de qualquer conteúdo específico. Os artigos partem do princípio de que tem um ficheiro denominado testfile.txt, mas pode utilizar qualquer ficheiro.

1. Para o armazenamento de Blobs, selecione **Blobs**.

   ![Selecionar blobs](./media/blob-event-quickstart-portal/select-blobs.png)

1. Selecionar **+ Contentor**. Nomeie o contentor e utilize qualquer nível de acesso.

   ![Adicionar contentor](./media/blob-event-quickstart-portal/add-container.png)

1. Selecione o novo contentor.

   ![Selecionar o contentor](./media/blob-event-quickstart-portal/select-container.png)

1. Para carregar um ficheiro, selecione **Carregar**.

   ![Selecionar a operação carregar](./media/blob-event-quickstart-portal/upload-file.png)

1. Navegue para o ficheiro de teste e carregue-o.

1. Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Veja a sua aplicação Web e repare que foi recebido um evento criado por um blob. 

  ```json
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/eventgroup/providers/Microsoft.Storage/storageAccounts/demoblob0625",
    "subject": "/blobServices/default/containers/eventcontainer/blobs/testfile.txt",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2018-06-25T22:50:41.1823131Z",
    "id": "89a2f9da-c01e-00bb-13d6-0c599506e4e3",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "41341a9b-e977-4a91-9000-c64125039047",
      "requestId": "89a2f9da-c01e-00bb-13d6-0c5995000000",
      "eTag": "0x8D5DAEE13C8F9ED",
      "contentType": "text/plain",
      "contentLength": 4,
      "blobType": "BlockBlob",
      "url": "https://demoblob0625.blob.core.windows.net/eventcontainer/testfile.txt",
      "sequencer": "00000000000000000000000000001C24000000000004712b",
      "storageDiagnostics": {
        "batchId": "ef633252-32fd-464b-8f5a-0d10d68885e6"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
  ```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, elimine os recursos que criou neste artigo.

Selecione o grupo de recursos e selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como criar tópicos personalizados e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
