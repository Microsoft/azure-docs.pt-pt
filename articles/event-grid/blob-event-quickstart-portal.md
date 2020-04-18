---
title: 'Quickstart: Envie eventos de armazenamento blob para web endpoint - portal'
description: 'Quickstart: Use Azure Event Grid e portal Azure para criar conta de armazenamento Blob e subscrever os seus eventos. Envie os eventos para um Webhook.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 04/16/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ada451b6bb3578a2903e9bd832b98981d7029d1d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605626"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Quickstart: Route Blob storage events to web endpoint with the Azure portal

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, irá utilizar o portal do Azure para criar uma conta de armazenamento de Blobs, subscrever eventos nesse armazenamento de blobs e acionar um evento para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

![Ver resultados](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Para criar um armazenamento de Blobs, selecione **Criar um recurso**. 

1. Selecione o **Armazenamento** para filtrar as opções disponíveis e selecione **Conta de armazenamento - blob, ficheiro, tabela, fila**.

   ![Selecionar armazenamento](./media/blob-event-quickstart-portal/create-storage.png)

   Para subscrever eventos, tem de criar uma conta de armazenamento de Blobs ou uma conta de armazenamento de fins gerais v2.
   
1. Na página da **conta de armazenamento Criar,** faça os seguintes passos:
    1. Selecione a sua subscrição do Azure. 
    2. Para **o grupo Recursos,** crie um novo grupo de recursos ou selecione um existente. 
    3. Introduza o nome da conta de armazenamento. 
    4. Selecione **Rever + criar**. 

       ![Passos de início](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. Na página **Review + criar,** rever as definições e selecionar **Criar**. 

        >[!NOTE]
        > Apenas contas de armazenamento do tipo **StorageV2 (finalidade geral v2)** e integração de eventos de suporte **blobStorage.** **O armazenamento (propósito genral v1)** *não* suporta a integração com a Grelha de Eventos.

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever aos eventos do armazenamento de Blobs, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Na página de **implementação personalizada,** faça os seguintes passos: 
    1. Para o **grupo Recursos,** selecione o grupo de recursos que criou ao criar a conta de armazenamento. Será mais fácil para si limpar depois de terminar o tutorial eliminando o grupo de recursos.  
    2. Para nome do **site,** introduza um nome para a aplicação web.
    3. Para hospedar o nome do **plano,** insira um nome para o plano do Serviço de Aplicações para hospedar a aplicação web.
    4. Selecione a caixa de verificação para **eu concordar com os termos e condições acima indicados**. 
    5. Selecione **Comprar**. 

       ![Parâmetros de implantação](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. A implementação pode demorar alguns minutos. Selecione Alertas (ícone do sino) no portal e, em seguida, selecione **Ir para o grupo de recursos**. 

    ![Alerta - navegue para o grupo de recursos](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Na página do **grupo Recursos,** na lista de recursos, selecione a aplicação web que criou. Você também vê o plano de Serviço de Aplicações e a conta de armazenamento nesta lista. 

    ![Selecione o web site](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. Na página do Serviço de **Aplicações** para a sua aplicação web, selecione o URL para navegar para o site. O URL deve estar `https://<your-site-name>.azurewebsites.net`neste formato: .
    
    ![Navegue para o site](./media/blob-event-quickstart-portal/web-site.png)

6. Confirme que vê o site, mas ainda não foram publicados eventos.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Subscrever a conta de armazenamento de Blobs

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. No portal, navegue para a sua conta de Armazenamento Azure que criou anteriormente. No menu esquerdo, selecione **Todos os recursos** e selecione a sua conta de armazenamento. 
2. Na página da **conta de Armazenamento,** selecione **Eventos** no menu esquerdo.
1. Selecione **Mais Opções**, e **Webhook**. Está a enviar eventos para a sua aplicação de visualização usando um gancho web para o ponto final. 

   ![Selecionar webhook](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Na página de Subscrição de **Eventos Create,** faça os seguintes passos: 
    1. Insira um **nome** para a subscrição do evento.
    2. Selecione **Web Hook** para **o tipo endpoint**. 

       ![Selecione o tipo de ponto final do gancho web](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. Para **endpoint,** clique **em Selecionar um ponto final**, `api/updates` e introduza o URL `https://spegridsite.azurewebsites.net/api/updates`da sua aplicação web e adicione ao URL da página inicial (por exemplo: ), e, em seguida, selecione **Confirmar Selection**.

   ![Confirmar a seleção de pontos finais](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Agora, na página de Subscrição de **Eventos Create,** selecione **Create** para criar a subscrição do evento. 

   ![Selecionar registos](./media/blob-event-quickstart-portal/create-subscription.png)

1. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

   ![Ver evento da subscrição](./media/blob-event-quickstart-portal/view-subscription-event.png)

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Aciona um evento para o armazenamento de Blobs ao carregar um ficheiro. O ficheiro não precisa de qualquer conteúdo específico. Os artigos partem do princípio de que tem um ficheiro denominado testfile.txt, mas pode utilizar qualquer ficheiro.

1. No portal Azure, navegue para a sua conta de armazenamento Blob e selecione **Recipientes** na página **'Visão Geral'.**

   ![Selecionar blobs](./media/blob-event-quickstart-portal/select-blobs.png)

1. Selecionar **+ Contentor**. Dê-lhe um nome para contentores e use qualquer nível de acesso, e selecione **Criar**. 

   ![Adicionar contentor](./media/blob-event-quickstart-portal/add-container.png)

1. Selecione o novo contentor.

   ![Selecionar o contentor](./media/blob-event-quickstart-portal/select-container.png)

1. Para carregar um ficheiro, selecione **Carregar**. Na página **'Upload',** navegue e selecione um ficheiro que pretende enviar para teste e, em seguida, selecione **Upload** nessa página. 

   ![Selecionar a operação carregar](./media/blob-event-quickstart-portal/upload-file.png)

1. Navegue para o ficheiro de teste e carregue-o.

1. Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. A mensagem está no formato JSON e contém uma matriz com um ou mais eventos. No exemplo seguinte, a mensagem JSON contém uma matriz com um evento. Consulte a sua aplicação web e note que foi recebido um evento criado pela **Blob.** 

   ![Evento criado pela Blob](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, elimine os recursos que criou neste artigo.

Selecione o grupo de recursos e selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos personalizados e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
