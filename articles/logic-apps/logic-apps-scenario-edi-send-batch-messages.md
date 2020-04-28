---
title: Processo de lote edi mensagens como um grupo
description: Envie e receba mensagens EDI como lotes, grupos ou coleções utilizando o processamento de lotes em Aplicações Lógicas Azure
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666674"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Trocar mensagens EDI como lotes ou grupos entre parceiros comerciais em Aplicações Lógicas Azure

Nos cenários de negócios para empresas (B2B), os parceiros trocam frequentemente mensagens em grupos ou *lotes*. Quando constrói uma solução de loteamento com Apps Lógicas, pode enviar mensagens a parceiros de negociação e processar essas mensagens em conjunto em lotes. Este artigo mostra como pode processar mensagens EDI em lote, utilizando x12 como exemplo, criando uma aplicação lógica de "remetente de lote" e uma aplicação lógica de "recetor de lote". 

O lote de mensagens X12 funciona como o lote de outras mensagens; utiliza-se um gatilho de lote que recolhe mensagens num lote e numa ação de lote que envia mensagens para o lote. Além disso, o lote X12 inclui um passo de codificação X12 antes das mensagens irem para o parceiro de negociação ou outro destino. Para saber mais sobre o gatilho e ação do lote, consulte [as mensagens](../logic-apps/logic-apps-batch-process-send-receive-messages.md)de processo do Lote .

Neste artigo, você vai construir uma solução de loteamento criando duas aplicações lógicas dentro da mesma subscrição Azure, região do Azure, e seguindo esta ordem específica:

* Uma aplicação lógica [de "recetor de lote",](#receiver) que aceita e recolhe mensagens num lote até que os seus critérios especificados são cumpridos para a libertação e processamento dessas mensagens. Neste cenário, o recetor do lote também codifica as mensagens no lote utilizando o acordo x12 especificado ou identidades do parceiro.

  Certifique-se de que cria primeiro o recetor do lote para que possa selecionar mais tarde o destino do lote quando criar o remetente do lote.

* Uma aplicação lógica [de "remetente de lote",](#sender) que envia as mensagens para o recetor de lote previamente criado. 

Certifique-se de que o seu recetor de lote e remetente de lote partilham a mesma assinatura Azure *e* a região azure. Se não o fizerem, não é possível selecionar o recetor do lote quando criar o remetente do lote porque não são visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [inscreva-se para uma subscrição pay-As-You-Go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) existente que está associada à sua subscrição do Azure e está ligada às suas aplicações lógicas

* Pelo menos dois [parceiros existentes](../logic-apps/logic-apps-enterprise-integration-partners.md) na sua conta de integração. Cada parceiro deve utilizar o apuramento X12 (Standard Carrier Alpha Code) como identidade comercial nas propriedades do parceiro.

* Um [acordo X12](../logic-apps/logic-apps-enterprise-integration-x12.md) existente na sua conta de integração

* Para utilizar o Visual Studio e não o portal Azure, certifique-se de configurar o [Visual Studio para trabalhar com aplicações lógicas.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Criar recetor de lote X12

Antes de poder enviar mensagens para um lote, esse lote deve existir primeiro como destino para enviar essas mensagens. Em primeiro lugar, tem de criar a aplicação lógica "recetor de lote", que começa com o gatilho do **Lote.** Desta forma, quando criar a aplicação lógica "remetente de lote", pode selecionar a aplicação lógica do recetor de lote. O recetor do lote continua a recolher mensagens até que os seus critérios especificados são cumpridos para a libertação e processamento dessas mensagens. Embora os recetores de lote não precisem de saber nada sobre os remetentes do lote, os remetentes devem saber o destino para onde enviam as mensagens. 

Para este recetor de lote, especifice o modo de lote, nome, critérios de lançamento, acordo X12 e outras definições. 

1. No [portal Azure](https://portal.azure.com) ou Estúdio Visual, crie uma aplicação lógica com este nome: "BatchX12Messages"

2. [Ligue a sua aplicação lógica à sua conta de integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

3. Em Logic Apps Designer, adicione o gatilho **do Lote,** que inicia o fluxo de trabalho da sua aplicação lógica. Na caixa de pesquisa, introduza "lote" como filtro. Selecione este gatilho: **Mensagens de lote**

   ![Adicionar gatilho de lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Definir as propriedades do recetor do lote: 

   | Propriedade | Valor | Notas | 
   |----------|-------|-------|
   | **Modo lote** | Inline |  |  
   | **Nome do lote** | Testbatch | Disponível apenas com o modo de lote **Inline** | 
   | **Critérios de Libertação** | Contagem de mensagens baseada, com base em Agenda | Disponível apenas com o modo de lote **Inline** | 
   | **Contagem de Mensagens** | 10 | Disponível apenas com critérios de libertação **baseados na contagem de mensagens** | 
   | **Intervalo** | 10 | Disponível apenas com critérios de libertação **baseados em Agenda** | 
   | **Frequência** | minute | Disponível apenas com critérios de libertação **baseados em Agenda** | 
   ||| 

   ![Fornecer detalhes do gatilho do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Este exemplo não configura uma divisória para o lote, por isso cada lote usa a mesma chave de partição. Para saber mais sobre divisórias, consulte as mensagens de [processo do Lote.](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)

5. Adicione agora uma ação que codifica cada lote: 

   1. Sob o gatilho do lote, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "X12 batch" como filtro e selecione esta ação (qualquer versão): **Código de <*versão*> - X12** 

      ![Selecione ação de codificação de código de lote X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Se não se ligou previamente à sua conta de integração, crie a ligação agora. Forneça um nome para a sua ligação, selecione a conta de integração que deseja e, em seguida, escolha **Criar**.

      ![Criar ligação entre codificador de lote e conta de integração](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Defino estas propriedades para a sua ação codificadora:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Nome do acordo X12** | Abra a lista e selecione o seu acordo existente. <p>Se a sua lista estiver vazia, certifique-se de ligar a [sua aplicação lógica à conta](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) de integração que tem o acordo que deseja. | 
      | **Nome de lote** | Clique dentro desta caixa e, depois de aparecer a lista de conteúdos dinâmicos, selecione o token Nome do **Lote.** | 
      | **Nome da partilha** | Clique dentro desta caixa e, depois de aparecer a lista de conteúdos dinâmicos, selecione o token **Nome da Partição.** | 
      | **Itens** | Feche a caixa de detalhes do artigo e, em seguida, clique dentro desta caixa. Depois da lista de conteúdos dinâmicos aparecer, selecione o símbolo dos **itens emlota.** | 
      ||| 

      ![Detalhes de ação do Código do Lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Para a caixa **de itens:**

      ![Itens de ação de codificação de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Guarde a aplicação lógica. 

7. Se estiver a utilizar o Visual Studio, certifique-se de [que implementa a sua aplicação lógica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)de recetor de lote para o Azure . Caso contrário, não é possível selecionar o recetor do lote quando criar o remetente do lote.

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para se certificar de que o seu recetor de lote funciona como esperado, pode adicionar uma ação HTTP para efeitos de teste e enviar uma mensagem lotada para o [serviço Request Bin](https://requestbin.com/). 

1. Sob a ação x12 codificar, escolha **novo passo**. 

2. Na caixa de pesquisa, introduza "http" como filtro. Selecione esta ação: **HTTP - HTTP**
    
   ![Selecione ação HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Detete as propriedades para a ação HTTP:

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Método** | A partir desta lista, selecione **POST**. | 
   | **Uri** | Gere um URI para o seu caixote de pedidos e, em seguida, introduza esse URI nesta caixa. | 
   | **Corpo** | Clique nesta caixa e, após a abertura da lista de conteúdos dinâmicos, selecione o símbolo **Body,** que aparece na secção, **Código de lote por nome**de acordo . <p>Se não vir o símbolo **do Corpo,** ao lado do Código de Lote por nome de **acordo,** selecione **Ver mais**. | 
   ||| 

   ![Fornecer detalhes de ação http](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Guarde a aplicação lógica. 

   A sua aplicação lógica de recetor de lote parece este exemplo: 

   ![Guarde a sua aplicação lógica de recetor de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Criar remetente de lote X12

Agora crie uma ou mais aplicações lógicas que enviam mensagens para a aplicação lógica do recetor de lote. Em cada remetente de lote, especifica a aplicação lógica do recetor de lote e o nome do lote, conteúdo de mensagem e quaisquer outras definições. Pode opcionalmente fornecer uma chave de partição única para dividir o lote em subconjuntos para recolher mensagens com essa chave. 

* Certifique-se de que já [criou o seu recetor](#receiver) de lote para que, quando criar o remetente do lote, possa selecionar o recetor de lote existente como lote de destino. Enquanto os recetores de lote não precisam de saber nada sobre os remetentes do lote, os remetentes do lote devem saber onde enviar mensagens. 

* Certifique-se de que o seu recetor de lote e remetente de lote partilham a mesma região Azure *e* a assinatura Azure. Se não o fizerem, não é possível selecionar o recetor do lote quando criar o remetente do lote porque não são visíveis um para o outro.

1. Crie outra aplicação lógica com este nome: "SendX12MessagesToBatch" 

2. Na caixa de pesquisa, introduza "quando um pedido http" como filtro. Selecione este gatilho: **Quando um pedido HTTP é recebido** 
   
   ![Adicione o gatilho de Pedido](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Adicione uma ação para enviar mensagens para um lote.

   1. No âmbito da ação de pedido http, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "lote" como filtro. 
   Selecione a lista **de Ações** e, em seguida, selecione esta ação: Escolha um fluxo de trabalho de **Aplicações Lógicas com gatilho de lote - Enviar mensagens para o lote**

      ![Selecione "Escolha um fluxo de trabalho de aplicações lógicas com gatilho de lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Agora selecione a sua aplicação lógica "BatchX12Messages" que criou anteriormente.

      ![Selecione aplicativo lógico "recetor de lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecione esta ação: **Batch_messages - <o seu *recetor* > de lote**

      ![Selecione ação "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Detete as propriedades do remetente do lote.

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pela aplicação lógica recetora, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado no tempo de execução e deve corresponder ao nome especificado pela aplicação lógica do recetor. Mudar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo da mensagem** | O conteúdo da mensagem que pretende enviar, que é o símbolo do **Corpo** neste exemplo | 
   ||| 
   
   ![Definir propriedades do lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Guarde a aplicação lógica. 

   A sua aplicação lógica de remetente de lote parece este exemplo:

   ![Guarde a sua aplicação lógica de remetente de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Teste as suas aplicações lógicas

Para testar a sua solução de loteamento, poste mensagens X12 na sua aplicação lógica de remetente de lote do [Carteiro](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. Em breve, começa a receber mensagens X12 no seu caixote do pedido, a cada 10 minutos ou em lotes de 10, todas com a mesma chave de partição.

## <a name="next-steps"></a>Passos seguintes

* [Processar mensagens como lotes](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
