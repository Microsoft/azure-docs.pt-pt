---
title: Mensagens EDI do processo em lote como um grupo ou uma coleção-aplicativos lógicos do Azure | Microsoft Docs
description: Enviar mensagens EDI para processamento em lotes em aplicativos lógicos
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: c2b0e2ed801724b682e0c4a60d6d7dff9645aab3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827428"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Enviar mensagens EDI em lotes para parceiros comerciais com aplicativos lógicos do Azure

Em cenários B2B (entre empresas), os parceiros geralmente trocam mensagens em grupos ou *lotes*. Ao criar uma solução de envio em lote com aplicativos lógicos, você pode enviar mensagens para parceiros comerciais e processar essas mensagens em lotes. Este artigo mostra como você pode processar mensagens EDI do processo em lote, usando X12 como um exemplo, criando um aplicativo lógico "remetente do lote" e um aplicativo lógico "receptor do lote". 

O envio em lote de mensagens X12 funciona como o envio em lote de outras mensagens; Você usa um gatilho de lote que coleta mensagens em um lote e uma ação em lote que envia mensagens para o lote. Além disso, o envio em lote do X12 inclui uma etapa de codificação de X12 antes que as mensagens vá para o parceiro comercial ou outro destino. Para saber mais sobre o gatilho e a ação do lote, confira [mensagens de processo em lote](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Neste artigo, você criará uma solução de envio em lote criando dois aplicativos lógicos dentro da mesma assinatura do Azure, região do Azure e seguindo esta ordem específica:

* Um aplicativo lógico ["receptor do lote"](#receiver) , que aceita e coleta mensagens em um lote até que os critérios especificados sejam atendidos para liberar e processar essas mensagens. Nesse cenário, o receptor do lote também codifica as mensagens no lote usando o contrato X12 ou as identidades de parceiro especificadas.

  Certifique-se de criar primeiro o receptor do lote para que você possa selecionar posteriormente o destino do lote ao criar o remetente do lote.

* Um aplicativo lógico ["remetente do lote"](#sender) , que envia as mensagens para o receptor do lote criado anteriormente. 

Verifique se o receptor do lote e o remetente do lote compartilham a mesma assinatura do Azure *e* a região do Azure. Se não forem, você não poderá selecionar o receptor do lote ao criar o remetente do lote, pois eles não ficam visíveis entre si.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, você precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou então, [Inscreva-se para uma assinatura paga conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) existente associada à sua assinatura do Azure e está vinculada aos seus aplicativos lógicos

* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) existentes em sua conta de integração. Cada parceiro deve usar o qualificador X12 (código alfa de operador padrão) como uma identidade de negócios nas propriedades do parceiro.

* Um [contrato de X12](../logic-apps/logic-apps-enterprise-integration-x12.md) existente em sua conta de integração

* Para usar o Visual Studio em vez do portal do Azure, certifique-se de [Configurar o Visual Studio para trabalhar com aplicativos lógicos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Criar receptor do lote X12

Antes que você possa enviar mensagens para um lote, esse lote deve primeiro existir como o destino onde você envia essas mensagens. Primeiro, você deve criar o aplicativo lógico "receptor do lote", que começa com o gatilho **lote** . Dessa forma, ao criar o aplicativo lógico "remetente do lote", você pode selecionar o aplicativo lógico do destinatário do lote. O destinatário do lote continua coletando mensagens até que os critérios especificados sejam atendidos para liberar e processar essas mensagens. Embora os receptores de lote não precisem saber nada sobre remetentes do lote, os remetentes do lote devem saber o destino no qual enviam as mensagens. 

Para esse receptor do lote, você especifica o modo de lote, o nome, os critérios de versão, o contrato de X12 e outras configurações. 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico com este nome: "BatchX12Messages"

2. [Vincule seu aplicativo lógico à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. No designer de aplicativos lógicos, adicione o gatilho de **lote** , que inicia o fluxo de trabalho do aplicativo lógico. Na caixa de pesquisa, digite "Batch" como filtro. Selecione este gatilho: **Mensagens em lotes**

   ![Adicionar gatilho de lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Defina as propriedades do destinatário do lote: 

   | Propriedade | Value | Notas | 
   |----------|-------|-------|
   | **Modo de lote** | Inline |  |  
   | **Nome do lote** | TestBatch | Disponível somente com o modo de lote embutido | 
   | **Critérios de liberação** | Contagem de mensagens com base em agendamento | Disponível somente com o modo de lote embutido | 
   | **Contagem de mensagens** | 10 | Disponível somente com os critérios de liberação **baseados na contagem de mensagens** | 
   | **Intervalo** | 10 | Disponível somente com os critérios de liberação **com base na agenda** | 
   | **Frequência** | minuto | Disponível somente com os critérios de liberação **com base na agenda** | 
   ||| 

   ![Fornecer detalhes do gatilho de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Este exemplo não configura uma partição para o lote, portanto, cada lote usa a mesma chave de partição. Para saber mais sobre partições, confira [mensagens de processo em lote](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Agora, adicione uma ação que codifica cada lote: 

   1. No gatilho lote, escolha **nova etapa**.

   2. Na caixa de pesquisa, digite "X12 batch" como filtro e selecione esta ação (qualquer versão): **< De codificação de lote*versão*>-X12** 

      ![Selecionar ação de codificação em lote X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Se você não se conectou anteriormente à sua conta de integração, crie a conexão agora. Forneça um nome para a conexão, selecione a conta de integração desejada e, em seguida, escolha **criar**.

      ![Criar conexão entre o codificador do lote e a conta de integração](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Defina essas propriedades para a ação do codificador do lote:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Nome do contrato de X12** | Abra a lista e selecione o contrato existente. <p>Se sua lista estiver vazia, certifique-se de [vincular seu aplicativo lógico à conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) que tem o contrato desejado. | 
      | **BatchName** | Clique dentro dessa caixa e, depois que a lista de conteúdo dinâmico for exibida, selecione o token de **nome de lote** . | 
      | **PartitionName** | Clique dentro dessa caixa e, depois que a lista de conteúdo dinâmico for exibida, selecione o token de **nome de partição** . | 
      | **Los** | Feche a caixa detalhes do item e clique dentro dessa caixa. Depois que a lista de conteúdo dinâmico for exibida, selecione o token **itens em lote** . | 
      ||| 

      ![Detalhes da ação de codificação em lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Para a caixa **itens** :

      ![Itens de ação de codificação em lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Guarde a aplicação lógica. 

7. Se você estiver usando o Visual Studio, certifique-se de [implantar seu aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, você não poderá selecionar o destinatário do lote ao criar o remetente do lote.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para garantir que o receptor do lote funcione conforme o esperado, você pode adicionar uma ação HTTP para fins de teste e enviar uma mensagem em lote para o [serviço de compartimento de solicitação](https://requestbin.com/). 

1. Na ação de codificação X12, escolha **nova etapa**. 

2. Na caixa de pesquisa, digite "http" como seu filtro. Selecione esta ação: **HTTP-HTTP**
    
   ![Selecionar ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Defina as propriedades para a ação HTTP:

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Método** | Nessa lista, selecione **post**. | 
   | **URI** | Gere um URI para sua bin de solicitação e, em seguida, insira esse URI nesta caixa. | 
   | **Corpo** | Clique dentro dessa caixa e, depois que a lista de conteúdo dinâmico for aberta, selecione o token de **corpo** , que aparece na seção, **codifique em lote por nome do contrato**. <p>Se você não vir o token de **corpo** , ao lado de **codificação em lote por nome do contrato**, selecione **Ver mais**. | 
   ||| 

   ![Fornecer detalhes da ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Guarde a aplicação lógica. 

   O aplicativo lógico do receptor do lote é semelhante a este exemplo: 

   ![Salvar o aplicativo lógico do receptor do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Criar remetente do lote X12

Agora, crie um ou mais aplicativos lógicos que enviam mensagens para o aplicativo lógico do destinatário do lote. Em cada remetente do lote, você especifica o aplicativo lógico do destinatário do lote e o nome do lote, o conteúdo da mensagem e outras configurações. Opcionalmente, você pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos para coletar mensagens com essa chave. 

* Certifique-se de que você já [criou o receptor do lote](#receiver) , portanto, ao criar o remetente do lote, você pode selecionar o destinatário do lote existente como o lote de destino. Embora os receptores de lote não precisem saber nada sobre remetentes do lote, os remetentes do lote devem saber para onde enviar mensagens. 

* Verifique se o receptor do lote e o remetente do lote compartilham a mesma região do Azure *e* a assinatura do Azure. Se não forem, você não poderá selecionar o receptor do lote ao criar o remetente do lote, pois eles não ficam visíveis entre si.

1. Crie outro aplicativo lógico com este nome: "SendX12MessagesToBatch" 

2. Na caixa de pesquisa, digite "quando uma solicitação HTTP" como seu filtro. Selecione este gatilho: **Quando uma solicitação HTTP é recebida** 
   
   ![Adicionar o gatilho de solicitação](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Adicione uma ação para enviar mensagens a um lote.

   1. Na ação solicitação HTTP, escolha **nova etapa**.

   2. Na caixa de pesquisa, digite "Batch" como filtro. 
   Selecione a lista **ações** e, em seguida, selecione esta ação: **Escolha um fluxo de trabalho de aplicativos lógicos com gatilho de lote – enviar mensagens para o lote**

      ![Selecione "escolher um fluxo de trabalho de aplicativos lógicos com gatilho de lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Agora, selecione o aplicativo lógico "BatchX12Messages" que você criou anteriormente.

      ![Selecione o aplicativo lógico "destinatário do lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecione esta ação: **Batch_messages-<*seu-receptor-batch*>**

      ![Selecione a ação "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Defina as propriedades do remetente do lote.

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pelo aplicativo lógico do receptor, que é "Loteteste" neste exemplo <p>**Importante**: O nome do lote é validado em tempo de execução e deve corresponder ao nome especificado pelo aplicativo lógico receptor. Alterar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo da mensagem** | O conteúdo da mensagem que você deseja enviar, que é o token de **corpo** neste exemplo | 
   ||| 
   
   ![Definir propriedades do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Guarde a aplicação lógica. 

   O aplicativo lógico do remetente do lote é semelhante a este exemplo:

   ![Salvar o aplicativo lógico do remetente do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar sua solução de envio em lote, poste mensagens X12 para o aplicativo lógico do remetente do lote do [postmaster](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. Em breve, você começa a obter mensagens X12 em seu compartimento de solicitação, seja a cada 10 minutos ou em lotes de 10, tudo com a mesma chave de partição.

## <a name="next-steps"></a>Passos Seguintes

* [Processar mensagens como lotes](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
