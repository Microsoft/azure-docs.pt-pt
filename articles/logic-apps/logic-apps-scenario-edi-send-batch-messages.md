---
title: Processo de lote mensagens EDI como um grupo
description: Enviar e receber mensagens EDI como lotes, grupos ou coleções utilizando o processamento de lotes em Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000407"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Trocar mensagens EDI como lotes ou grupos entre parceiros comerciais em Azure Logic Apps

Em cenários de negócios para negócios (B2B), os parceiros trocam frequentemente mensagens em grupos ou *lotes.* Ao construir uma solução de lote com Aplicações Lógicas, pode enviar mensagens a parceiros de negociação e processar essas mensagens em lotes. Este artigo mostra como pode processar mensagens EDI, usando x12 como exemplo, criando uma aplicação lógica de "remetente de lote" e uma aplicação lógica de "recetor de lote". 

O loteamento de mensagens X12 funciona como o lote de outras mensagens; utiliza um gatilho de lote que recolhe mensagens num lote e uma ação de lote que envia mensagens para o lote. Além disso, o lote X12 inclui um passo de codificação X12 antes que as mensagens vão para o parceiro de negociação ou outro destino. Para saber mais sobre o gatilho e a ação do lote, consulte [as mensagens de processo do Lote](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Neste artigo, você vai construir uma solução de loteing criando duas aplicações lógicas dentro da mesma subscrição Azure, região de Azure, e seguindo esta ordem específica:

* Uma aplicação lógica [de "recetor de lote",](#receiver) que aceita e recolhe mensagens num lote até que os critérios especificados sejam cumpridos para libertar e processar essas mensagens. Neste cenário, o recetor do lote também codifica as mensagens no lote utilizando o acordo X12 especificado ou identidades parceiras.

  Certifique-se de que cria primeiro o recetor do lote para que possa selecionar mais tarde o destino do lote quando criar o remetente do lote.

* Uma aplicação lógica ["remetente de lote",](#sender) que envia as mensagens para o recetor de lote previamente criado. 

Certifique-se de que o seu recetor de lote e remetente de lote partilham a mesma subscrição Azure *e* a região de Azure. Se não o fizerem, não é possível selecionar o recetor do lote quando se cria o remetente do lote porque não são visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [inscreva-se para uma subscrição pay-as-you-go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) existente que está associada à sua subscrição do Azure e está ligada às suas aplicações lógicas

* Pelo menos dois [parceiros existentes](../logic-apps/logic-apps-enterprise-integration-partners.md) na sua conta de integração. Cada parceiro deve utilizar o qualificador X12 (Standard Carrier Alpha Code) como identidade comercial nas propriedades do parceiro.

* Um [acordo X12](../logic-apps/logic-apps-enterprise-integration-x12.md) existente na sua conta de integração

* Para utilizar o Visual Studio em vez do portal Azure, certifique-se de [configurar o Visual Studio para trabalhar com as Aplicações Lógicas.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Criar recetor de lote X12

Antes de enviar mensagens para um lote, esse lote deve primeiro existir como destino para onde envia essas mensagens. Por isso, primeiro, tem de criar a aplicação lógica "recetor de lote", que começa com o gatilho **do Lote.** Assim, quando criar a aplicação lógica "batch sender", pode selecionar a aplicação lógica do recetor de lote. O recetor do lote continua a recolher mensagens até que os seus critérios especificados seja cumprido para libertar e processar essas mensagens. Embora os recetores de lotes não precisem de saber nada sobre os remetentes de lotes, os remetentes de lotes devem saber o destino onde enviam as mensagens. 

Para este recetor de lote, especifique o modo de lote, nome, critérios de libertação, acordo X12 e outras definições. 

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, crie uma aplicação lógica com este nome: "BatchX12Messages"

2. [Ligue a sua aplicação lógica à sua conta de integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

3. No Logic Apps Designer, adicione o gatilho **Batch,** que inicia o fluxo de trabalho da aplicação lógica. Na caixa de pesquisa, introduza "lote" como filtro. Selecione este gatilho: **Mensagens de lote**

   ![Adicione o gatilho do lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Definir as propriedades do recetor do lote: 

   | Propriedade | Valor | Notas | 
   |----------|-------|-------|
   | **Modo lote** | Inline |  |  
   | **Nome do lote** | TestBatch | Disponível apenas com o modo de lote **Inline** | 
   | **Critérios de libertação** | Base na contagem de mensagens, agendamento baseado | Disponível apenas com o modo de lote **Inline** | 
   | **Contagem de mensagens** | 10 | Disponível apenas com critérios de libertação **baseados na contagem de mensagens** | 
   | **Intervalo** | 10 | Disponível apenas com critérios de libertação **baseados na Agenda** | 
   | **Frequência** | minute | Disponível apenas com critérios de libertação **baseados na Agenda** | 
   ||| 

   ![Fornecer detalhes do gatilho do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Este exemplo não configura uma divisória para o lote, por isso cada lote usa a mesma chave de partição. Para saber mais sobre divisórias, consulte [as mensagens de processo do Lote](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Agora adicione uma ação que codifica cada lote: 

   1. Sob o gatilho do lote, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "X12 batch" como filtro e selecione esta ação (qualquer versão): **Código de lote <*versão*> - X12** 

      ![Selecione ação de codificação de lote X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Se não ligou previamente à sua conta de integração, crie agora a ligação. Forneça um nome para a sua ligação, selecione a conta de integração que deseja e, em seguida, escolha **Criar**.

      ![Criar ligação entre codificação de lote e conta de integração](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Desapedaça estas propriedades para a sua ação de codificação de lote:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Nome do acordo X12** | Abra a lista e selecione o seu acordo existente. <p>Se a sua lista estiver vazia, certifique-se de [que liga a sua aplicação lógica à conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) que tem o acordo que deseja. | 
      | **Nome do lote** | Clique dentro desta caixa e, depois de aparecer a lista de conteúdos dinâmicos, selecione o **token 'Nome** do Lote'. | 
      | **Nome de partição** | Clique dentro desta caixa e, depois de aparecer a lista de conteúdos dinâmicos, selecione o token **Nome de Partição.** | 
      | **Itens** | Feche a caixa de detalhes do item e, em seguida, clique dentro desta caixa. Depois da lista de conteúdo dinâmico aparecer, selecione o **token de itens em** lote. | 
      ||| 

      ![Detalhes de ação do Lote Encode](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Para a caixa **de itens:**

      ![Artigos de ação do Lote Encode](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Guarde a sua aplicação lógica. 

7. Se estiver a utilizar o Visual Studio, certifique-se de [que implementa a sua aplicação lógica recetora de lote para a Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, não é possível selecionar o recetor do lote quando criar o remetente do lote.

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para se certificar de que o recetor do lote funciona como esperado, pode adicionar uma ação HTTP para efeitos de teste e enviar uma mensagem em lote para o [serviço 'Bin's Request](https://requestbin.com/)Bin . 

1. Sob a ação de codificação X12, escolha **Novo passo**. 

2. Na caixa de pesquisa, introduza "http" como filtro. Selecione esta ação: **HTTP - HTTP**
    
   ![Selecione ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Definir as propriedades para a ação HTTP:

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Método** | A partir desta lista, selecione **POST**. | 
   | **Uri** | Gere um URI para o seu caixote de pedido e, em seguida, insira esse URI nesta caixa. | 
   | **Corpo** | Clique dentro desta caixa e, após a abertura da lista de conteúdos dinâmicos, selecione o token **Do Corpo,** que aparece na secção, **Código de Lote por nome de acordo**. <p>Se não vir o **token do Corpo,** ao lado do **código de lote por nome de acordo,** selecione Ver **mais**. | 
   ||| 

   ![Fornecer detalhes de ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Guarde a sua aplicação lógica. 

   A sua aplicação lógica recetora de lote parece este exemplo: 

   ![Guarde o seu aplicativo de lógica de recetor de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Criar remetente de lote X12

Agora crie uma ou mais aplicações lógicas que enviam mensagens para a aplicação lógica do recetor do lote. Em cada remetente de lote, especifica a aplicação lógica do recetor do lote e o nome do lote, o conteúdo da mensagem e quaisquer outras definições. Pode opcionalmente fornecer uma chave de partição única para dividir o lote em subconjuntos para recolher mensagens com essa chave. 

* Certifique-se de que já [criou o seu recetor](#receiver) de lote para que, quando criar o seu remetente de lote, possa selecionar o recetor de lote existente como lote de destino. Embora os recetores de lotes não precisem de saber nada sobre os remetentes de lotes, os remetentes de lotes devem saber para onde enviar mensagens. 

* Certifique-se de que o seu recetor de lote e o remetente do lote partilham a mesma região Azure *e* a subscrição do Azure. Se não o fizerem, não é possível selecionar o recetor do lote quando se cria o remetente do lote porque não são visíveis um para o outro.

1. Criar outra aplicação lógica com este nome: "SendX12MessagesToBatch" 

2. Na caixa de pesquisa, insira "quando um pedido http" como filtro. Selecione este gatilho: **Quando um pedido HTTP é recebido** 
   
   ![Adicione o gatilho do pedido](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Adicione uma ação para enviar mensagens a um lote.

   1. Ao abrigo da ação http request, escolha **Novo passo.**

   2. Na caixa de pesquisa, introduza "lote" como filtro. 
   Selecione a lista **de Ações** e, em seguida, selecione esta ação: **Escolha um fluxo de trabalho de Apps lógicas com gatilho de lote - Envie mensagens para lote**

      ![Selecione "Escolha um fluxo de trabalho de Apps lógicas com gatilho de lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Agora selecione a sua aplicação lógica "BatchX12Messages" que criou anteriormente.

      ![Selecione a aplicação lógica do "recetor de lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecione esta ação: **Batch_messages - <o seu *recetor* > de lote**

      ![Selecione ação "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Desa estale as propriedades do remetente do lote.

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pela app lógica do recetor, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado no tempo de execução e deve corresponder ao nome especificado pela aplicação lógica do recetor. Mudar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo de mensagens** | O conteúdo para a mensagem que quer enviar, que é o **símbolo do Corpo** neste exemplo | 
   ||| 
   
   ![Definir propriedades de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Guarde a sua aplicação lógica. 

   A sua aplicação lógica de remetente de lote parece este exemplo:

   ![Guarde a sua aplicação lógica de remetente de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Teste as suas aplicações lógicas

Para testar a sua solução de loteamento, publique mensagens X12 na sua aplicação lógica de remetente de lote a partir do [Carteiro](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. Em breve, começará a receber mensagens X12 no seu contentor de pedidos, a cada 10 minutos ou em lotes de 10, todos com a mesma chave de partição.

## <a name="next-steps"></a>Passos seguintes

* [Processar mensagens como lotes](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
