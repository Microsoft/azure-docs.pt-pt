---
title: Mensagens de processamento de EDI do batch como um grupo ou uma coleção - Azure Logic Apps | Documentos da Microsoft
description: Enviar mensagens EDI para processamento em lote em aplicações lógicas
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: d6d3a7111f3a5e49e32eba8ca4f09d692538cb87
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "64715799"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Enviar mensagens EDI em lotes para parceiros com o Azure Logic Apps comerciais

Em cenários de empresa-empresa (B2B), parceiros, muitas vezes, trocam mensagens em grupos ou *lotes*. Quando compila uma solução de criação de batches com o Logic Apps, pode enviar mensagens para parceiros comerciais e processar essas mensagens em conjunto em lotes. Este artigo mostra como pode batch mensagens de processamento de EDI, X12 a utilizar como exemplo, através da criação de uma aplicação de lógica do "remetente do batch" e uma aplicação de lógica do "destinatário do batch". 

X12 de criação de batches de mensagens funciona como a criação de batches de outras mensagens; utilizar um acionador de lote que recolhe as mensagens num lote e uma ação de batch que envia mensagens para o batch. Além disso, X12 criação de batches inclui um X12 de codificação passo antes das mensagens passam para o parceiro comercial ou outro destino. Para saber mais sobre o acionador de lote e a ação, veja [mensagens de processamento de Batch](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Neste artigo, irá criar uma solução de criação de batches ao criar duas aplicações de lógica dentro da mesma subscrição do Azure, a região do Azure e o seguinte nesta ordem específica:

* R ["recetor de batch"](#receiver) aplicação lógica, que aceita e recolhe as mensagens num lote até que os critérios especificados seja atendida para lançar e processar essas mensagens. Neste cenário, o recetor de batch codifica também as mensagens no batch com o X12 especificado identidades de contrato ou de parceiro.

  Certifique-se de que primeiro criar o recetor de lote, de modo mais tarde pode selecionar o destino de batch ao criar o remetente de batch.

* R ["remetente do batch"](#sender) aplicação lógica, que envia as mensagens para o destinatário de batch criado anteriormente. 

Certifique-se do recetor de batch e o remetente de batch partilham a mesma subscrição do Azure *e* região do Azure. Caso contrário, não é possível selecionar o recetor de batch ao criar o remetente de batch porque eles não são visíveis para si.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [Inscreva-se uma subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Existente [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição do Azure e está associado às suas aplicações lógicas

* Existente, pelo menos, dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) na sua conta de integração. Cada parceiro tem de utilizar o X12 (código de alfa de operadora padrão) qualificador como uma identidade de negócio nas propriedades do parceiro.

* Existente [X12 contrato](../logic-apps/logic-apps-enterprise-integration-x12.md) na sua conta de integração

* Para usar o Visual Studio, em vez do portal do Azure, certifique-se de que [configurar o Visual Studio para trabalhar com o Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Criar X12 recetor de batch

Antes de pode enviar mensagens para um lote, esse lote primeiro tem de existir como o destino onde envia essas mensagens. Primeiro, tem de criar a aplicação de lógica do "destinatário do batch", que começa com o **Batch** acionador. Dessa forma, ao criar a aplicação de lógica do "remetente do batch", pode selecionar a aplicação de lógica de destinatário do batch. O destinatário de batch continua a recolher as mensagens, até que os critérios especificados seja atendida para lançar e processar essas mensagens. Enquanto os recetores de batch não precisam saber nada sobre os remetentes de lote, os remetentes de batch tem de saber o destino em que enviam as mensagens. 

Para este destinatário do batch, especifique o modo de lote, o nome, critérios de lançamento, X12 contrato e outras definições. 

1. Na [portal do Azure](https://portal.azure.com) ou o Visual Studio, criar uma aplicação lógica com este nome: "BatchX12Messages"

2. [Ligar a sua aplicação lógica à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. No estruturador de aplicações lógicas, adicione a **Batch** acionador, que inicia o fluxo de trabalho de aplicação lógica. Na caixa de pesquisa, introduza "batch" como o filtro. Selecione este acionador: **Mensagens de batch**

   ![Adicionar o acionador de lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Defina propriedades de recetor de lote: 

   | Propriedade | Value | Notas | 
   |----------|-------|-------|
   | **Modo de lote** | Inline |  |  
   | **Nome do lote** | TestBatch | Disponível apenas com **Inline** modo de lote | 
   | **Critérios de versão** | Agenda de contagem de mensagens com base, com base | Disponível apenas com **Inline** modo de lote | 
   | **Contagem de mensagens** | 10 | Disponível apenas com **com base de contagem de mensagens** critérios de versão | 
   | **Intervalo** | 10 | Disponível apenas com **agenda com base** critérios de versão | 
   | **Frequência** | Minuto | Disponível apenas com **agenda com base** critérios de versão | 
   ||| 

   ![Fornecer detalhes do acionador de lote](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Neste exemplo não define uma partição para o batch, para que cada lote utiliza a mesma chave de partição. Para saber mais sobre as partições, veja [mensagens de processamento de Batch](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Agora, adicione uma ação que codifica cada lote: 

   1. Sob o acionador de lote, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "12 batch X" como o filtro e selecione a ação (qualquer versão): **Codificação do lote <*versão*>-X12** 

      ![Selecione X12 ação de codificação do lote](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Se anteriormente não ligar à sua conta de integração, crie agora a ligação. Forneça um nome para a sua ligação, selecione a conta de integração que pretende e, em seguida, escolha **criar**.

      ![Criar ligação entre a conta do batch codificador e integração](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Defina essas propriedades para a ação de codificador de batch:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Nome de X12 contrato** | Abra a lista e selecione o seu contrato existente. <p>Se a lista estiver vazia, certifique-se de que [ligar a sua aplicação lógica para a conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) que tem o contrato que pretende. | 
      | **BatchName** | Clique no interior esta caixa e, depois de aparece na lista de conteúdo dinâmica, selecione o **nome do lote** token. | 
      | **PartitionName** | Clique no interior esta caixa e, depois de aparece na lista de conteúdo dinâmica, selecione o **nome da partição** token. | 
      | **itens** | Fechar a caixa de detalhes do item e, em seguida, clique no interior esta caixa. Depois de aparece na lista de conteúdo dinâmica, selecione o **itens em lotes** token. | 
      ||| 

      ![Detalhes da ação de codificar do batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Para o **itens** caixa:

      ![Itens de ação de codificar de batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Guarde a aplicação lógica. 

7. Se estiver a utilizar o Visual Studio, certifique-se de que [implementar a aplicação de lógica de destinatário do batch no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, não é possível selecionar o recetor de batch ao criar o remetente de batch.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para garantir que seu funciona de recetor de batch conforme esperado, pode adicionar uma ação de HTTP para fins de teste e enviar uma mensagem em lote para o [serviço de pedido Bin](https://requestbin.fullcontact.com/). 

1. Sob o X12 codificar ação, escolha **novo passo**. 

2. Na caixa de pesquisa, introduza "http" como o filtro. Selecione a ação: **HTTP - HTTP**
    
   ![Selecione a ação de HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Defina as propriedades para a ação de HTTP:

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Método** | Nesta lista, selecione **POST**. | 
   | **Uri** | Gerar um URI para o contentor de pedido e, em seguida, introduza esse URI nesta caixa. | 
   | **Corpo** | Clique no interior esta caixa e, depois de abre a lista de conteúdo dinâmico, selecione o **corpo** token, que aparece na secção, **codificação do lote por nome do contrato**. <p>Se não vir a **corpo** token, junto a **codificação do lote por nome do contrato**, selecione **ver mais**. | 
   ||| 

   ![Fornecer detalhes da ação de HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Guarde a aplicação lógica. 

   A aplicação de lógica de recetor de batch é semelhante a este exemplo: 

   ![Guarde a aplicação de lógica de recetor de batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Criar X12 remetente de batch

Agora, crie um ou mais aplicações lógicas que enviam mensagens para a aplicação de lógica de destinatário do batch. Em cada remetente do batch, especifique a aplicação de lógica de destinatário do batch e nome do lote, conteúdo da mensagem e quaisquer outras definições. Opcionalmente, pode fornecer uma chave de partição única para dividir o batch em subconjuntos recolher mensagens com essa chave. 

* Certifique-se de que já [criado do recetor de batch](#receiver) pelo quando cria o remetente do batch, pode selecionar o receptor do lote existente como o lote de destino. Enquanto recetores de lote não precisam saber nada sobre os remetentes de lote, os remetentes de batch tem de saber para onde enviar as mensagens. 

* Certifique-se do recetor de batch e o remetente de batch partilham a mesma região do Azure *e* subscrição do Azure. Caso contrário, não é possível selecionar o recetor de batch ao criar o remetente de batch porque eles não são visíveis para si.

1. Crie outra aplicação de lógica com este nome: "SendX12MessagesToBatch" 

2. Na caixa de pesquisa, introduza "quando um pedido de http" como o filtro. Selecione este acionador: **Quando é recebido um pedido HTTP** 
   
   ![Adicionar o acionador de pedido](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Adicione uma ação para enviar mensagens para um lote.

   1. Sob a ação de pedido HTTP, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "batch" como o filtro. 
   Selecione o **ações** lista e, em seguida, selecione a ação: **Escolha um fluxo de trabalho do Logic Apps com o acionador de lote - enviar mensagens para o lote**

      ![Selecione "Escolher um fluxo de trabalho do Logic Apps com o acionador de lote"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Agora, selecione a aplicação de lógica de "BatchX12Messages" que criou anteriormente.

      ![Selecione a aplicação de lógica do "destinatário do batch"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecione a ação: **Batch_messages - <*receptor do batch*>**

      ![Selecione a ação de "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Defina o batch de propriedades do remetente.

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pela aplicação de lógica receptor, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado em tempo de execução e tem de corresponder ao nome especificado pela aplicação de lógica do recetor. Alterar o nome do lote faz com que o remetente de batch efetuar a ativação. | 
   | **Conteúdo da mensagem** | O conteúdo para a mensagem que pretende enviar, que é o **corpo** token neste exemplo | 
   ||| 
   
   ![Definir as propriedades do batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Guarde a aplicação lógica. 

   A aplicação de lógica de remetente do batch é semelhante a este exemplo:

   ![Guarde a aplicação de lógica de remetente do batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar as suas aplicações lógicas

Para testar a sua solução de criação de batches, post nas mensagens X12 para a aplicação de lógica de remetente do lote da [Postman](https://www.getpostman.com/postman) ou uma ferramenta semelhante. Em breve, iniciar a obtenção de X12 mensagens no seu contentor de pedido, a cada 10 minutos ou em lotes de 10, tudo com a mesma chave de partição.

## <a name="next-steps"></a>Passos Seguintes

* [Processar mensagens como lotes](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
