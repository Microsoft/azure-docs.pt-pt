---
title: Mensagens de processo de lote como um grupo
description: Envie e receba mensagens em grupos entre os seus fluxos de trabalho utilizando o processamento de lotes em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666759"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Enviar, receber e enviar mensagens de processo de lote em Aplicações Lógicas Azure

Para enviar e processar mensagens em conjunto de uma forma específica como grupos, pode criar uma solução de lote que colete mensagens num *lote* até que os seus critérios especificados sejam cumpridos para libertar e processar as mensagens lotadas. O loteamento pode reduzir a frequência com que a sua aplicação lógica processa mensagens. Este artigo mostra como construir uma solução de loteamento, criando duas aplicações lógicas dentro da mesma subscrição azure, região do Azure, e seguindo esta ordem específica: 

* A aplicação lógica ["recetor de lote",](#batch-receiver) que aceita e recolhe mensagens num lote até que os seus critérios especificados são cumpridos para a libertação e processamento dessas mensagens.

  Certifique-se de que cria primeiro o recetor do lote para que possa selecionar mais tarde o destino do lote quando criar o remetente do lote.

* Uma ou mais aplicações lógicas de ["remetente](#batch-sender) de lote", que enviam as mensagens para o recetor de lote previamente criado. 

   Também pode especificar uma chave única, como um número de cliente, que *divide* ou divide o lote alvo em subconjuntos lógicos com base nessa chave. Desta forma, a aplicação recetora pode recolher todos os itens com a mesma chave e processá-los em conjunto.

Certifique-se de que o seu recetor de lote e remetente de lote partilham a mesma assinatura Azure *e* a região azure. Se não o fizerem, não é possível selecionar o recetor do lote quando criar o remetente do lote porque não são visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [inscreva-se para uma subscrição pay-As-You-Go](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de e-mail com qualquer fornecedor de [e-mail suportado por Aplicações Lógicas Azure](../connectors/apis-list.md)

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Para utilizar o Visual Studio e não o portal Azure, certifique-se de configurar o [Visual Studio para trabalhar com aplicações lógicas.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Criar recetor de lote

Antes de poder enviar mensagens para um lote, esse lote deve existir primeiro como destino para enviar essas mensagens. Em primeiro lugar, tem de criar a aplicação lógica "recetor de lote", que começa com o gatilho do **Lote.** Desta forma, quando criar a aplicação lógica "remetente de lote", pode selecionar a aplicação lógica do recetor de lote. O recetor do lote continua a recolher mensagens até que os seus critérios especificados são cumpridos para a libertação e processamento dessas mensagens. Embora os recetores de lote não precisem de saber nada sobre os remetentes do lote, os remetentes devem saber o destino para onde enviam as mensagens. 

1. No [portal Azure](https://portal.azure.com) ou Estúdio Visual, crie uma aplicação lógica com este nome: "BatchReceiver" 

2. Em Logic Apps Designer, adicione o gatilho **do Lote,** que inicia o fluxo de trabalho da sua aplicação lógica. Na caixa de pesquisa, introduza "lote" como filtro. Selecione este gatilho: **Mensagens de lote**

   ![Adicionar gatilho "Mensagens de lote"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Detete estas propriedades para o recetor do lote: 

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Modo lote** | - **Linha inline**: Para definir critérios de libertação dentro do gatilho do lote <br>- **Conta de Integração**: Para definir múltiplas configurações de critérios de libertação através de uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Com uma conta de integração, pode manter estas configurações num só local e não em aplicações lógicas separadas. | 
   | **Nome do lote** | O nome do seu lote, que é "TestBatch" neste exemplo, e aplica-se apenas ao modo de lote **Inline** |  
   | **Critérios de Libertação** | Aplica-se apenas ao modo de lote **Inline** e seleciona os critérios a cumprir antes de processar cada lote: <p>- **Contagem de mensagens baseada na :** Liberte o lote com base no número de mensagens recolhidas pelo lote. <br>- **Baseado**no tamanho : Liberte o lote com base no tamanho total em bytes para todas as mensagens recolhidas por esse lote. <br>- **Horário**: Liberte o lote com base num calendário de recorrência, que especifica um intervalo e frequência. Nas opções avançadas, também pode selecionar um fuso horário e fornecer uma data e hora de início. <br>- **Selecione tudo:** Utilize todos os critérios especificados. | 
   | **Contagem de Mensagens** | O número de mensagens a recolher no lote, por exemplo, 10 mensagens. O limite de um lote é de 8.000 mensagens. | 
   | **Tamanho do lote** | O tamanho total em bytes para recolher no lote, por exemplo, 10 MB. O limite de tamanho de um lote é de 80 MB. | 
   | **Agenda** | O intervalo e a frequência entre os lançamentos do lote, por exemplo, 10 minutos. A recorrência mínima é de 60 segundos ou 1 minuto. Os minutos fracionados são efetivamente arredondados até 1 minuto. Para especificar um fuso horário ou uma data e hora de início, escolha **Mostrar opções avançadas**. | 
   ||| 

   > [!NOTE]
   > 
   > Se alterar os critérios de libertação enquanto o gatilho ainda tiver mensagens lotadas mas não enviadas, o gatilho utiliza os critérios de libertação atualizados para o manuseamento das mensagens não enviadas. 

   Este exemplo mostra todos os critérios, mas para os seus próprios testes, tente apenas um critério:

   ![Fornecer detalhes do gatilho do Lote](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Adicione agora uma ou mais ações que processem cada lote. 

   Para este exemplo, adicione uma ação que envia um e-mail quando o lote dispara. 
   O gatilho funciona e envia um e-mail quando o lote tem 10 mensagens, atinge 10 MB, ou passados 10 minutos.

   1. Sob o gatilho do lote, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro.
   Com base no seu fornecedor de e-mail, selecione um conector de e-mail.

      Por exemplo, se tiver uma conta @outlook.com @hotmail.compessoal, como ou , selecione o conector Outlook.com. 
      Se tiver uma conta Gmail, selecione o conector Gmail. 
      Este exemplo utiliza o Office 365 Outlook. 

   3. Selecione esta ação: **Envie um e-mail - <fornecedor> *de e-mail* **

      Por exemplo:

      ![Selecione ação "Enviar um e-mail" para o seu fornecedor de e-mail](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

6. Detete as propriedades para a ação que acrescentou.

   * Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

   * Na caixa **'Assunto',** quando aparecer a lista de conteúdos dinâmicos, selecione o campo **Nome da Partilha.**

     ![Da lista de conteúdos dinâmicos, selecione "Nome da Partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Mais tarde, no remetente do lote, pode especificar uma chave de partição única que divide o lote alvo em subconjuntos lógicos onde pode enviar mensagens. 
     Cada conjunto tem um número único que é gerado pela aplicação lógica do remetente do lote. 
     Esta capacidade permite-lhe utilizar um único lote com vários subconjuntos e definir cada subconjunto com o nome que fornece.

     > [!IMPORTANT]
     > Uma partição tem um limite de 5.000 mensagens ou 80 MB. Se qualquer uma das condições for satisfeita, as Aplicações Lógicas podem libertar o lote, mesmo quando a sua condição de libertação definida não for satisfeita.

   * Na caixa **Body,** quando aparecer a lista de conteúdos dinâmicos, selecione o campo **Message Id.** 

     O Logic Apps Designer adiciona automaticamente um loop "Para cada" em torno da ação de envio de e-mail porque essa ação trata a saída da ação anterior como uma coleção, em vez de um lote. 

     ![Para "Body", selecione "Message Id"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Guarde a aplicação lógica. Agora criou um recetor de lote.

    ![Guardar a aplicação lógica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Se estiver a utilizar o Visual Studio, certifique-se de [que implementa a sua aplicação lógica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)de recetor de lote para o Azure . Caso contrário, não é possível selecionar o recetor do lote quando criar o remetente do lote.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Criar remetente de lote

Agora crie uma ou mais aplicações lógicas de remetente sender que enviam mensagens para a aplicação lógica do recetor de lote. Em cada remetente de lote, especifice o recetor do lote e o nome do lote, o conteúdo da mensagem e quaisquer outras definições. Pode opcionalmente fornecer uma chave de partição única para dividir o lote em subconjuntos lógicos para recolher mensagens com essa chave. 

* Certifique-se de que já [criou o seu recetor](#batch-receiver) de lote para que, quando criar o remetente do lote, possa selecionar o recetor de lote existente como lote de destino. Enquanto os recetores de lote não precisam de saber nada sobre os remetentes do lote, os remetentes do lote devem saber onde enviar mensagens. 

* Certifique-se de que o seu recetor de lote e remetente de lote partilham a mesma região Azure *e* a assinatura Azure. Se não o fizerem, não é possível selecionar o recetor do lote quando criar o remetente do lote porque não são visíveis um para o outro.

1. Crie outra aplicação lógica com este nome: "BatchSender"

   1. Na caixa de pesquisa, introduza "recorrência" como filtro. 
   Selecione este gatilho: **Recurrence - Agendar**

      ![Adicione o gatilho "Recurrence - Agendar"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Detete a frequência e o intervalo para executar a aplicação lógica do remetente a cada minuto.

      ![Definir frequência e intervalo para o gatilho de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Adicione uma nova ação para enviar mensagens para um lote.

   1. Sob o gatilho de recorrência, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "lote" como filtro. 
   Selecione a lista **de Ações** e, em seguida, selecione esta ação: Escolha um fluxo de trabalho de **Aplicações Lógicas com gatilho de lote - Enviar mensagens para o lote**

      ![Selecione "Escolha um fluxo de trabalho de aplicações lógicas com gatilho de lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecione a sua aplicação lógica de recetor de lote que criou anteriormente.

      ![Selecione aplicativo lógico "recetor de lote"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista também mostra quaisquer outras aplicações lógicas que tenham gatilhos de lote. 
      > 
      > Se estiver a utilizar o Visual Studio e não vir nenhum recetor de lote para selecionar, verifique se deslocou o seu recetor de lote para o Azure. Se não o fez, aprenda a implementar a sua aplicação lógica de [recetor de lote para o Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Selecione esta ação: **Batch_messages - <o seu *recetor* > de lote**

      ![Selecione esta ação: "Batch_messages - <> da sua aplicação lógica"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Detete as propriedades do remetente do lote:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pela aplicação lógica recetora, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado no tempo de execução e deve corresponder ao nome especificado pela aplicação lógica do recetor. Mudar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo da mensagem** | O conteúdo da mensagem que pretende enviar | 
   ||| 

   Para este exemplo, adicione esta expressão, que insere a data e a hora atuais no conteúdo da mensagem que envia ao lote:

   1. Clique dentro da caixa de Conteúdo de **Mensagem.** 

   2. Quando aparecer a lista de conteúdos dinâmicos, escolha **Expressão**. 

   3. Introduza `utcnow()`a expressão e, em seguida, escolha **OK**. 

      ![Em "Message Content", escolha "Expression", insira "utcnow"," e escolha "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Agora, prepara uma divisória para o lote. Na ação "BatchReceiver", escolha **opções avançadas do Show** e detete estas propriedades:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome da partilha** | Uma chave de partição única opcional para a utilização para dividir o lote alvo em subconjuntos lógicos e recolher mensagens com base nessa chave | 
   | **Id mensagem** | Um identificador de mensagem opcional que é um identificador globalmente único gerado (GUID) quando vazio | 
   ||| 

   Para este exemplo, na caixa de **nome supor,** adicione uma expressão que gere um número aleatório entre um e cinco. Deixe a caixa **de identificação** da mensagem vazia.
   
   1. Clique dentro da caixa de **nome seleção** para que a lista de conteúdos dinâmicos apareça. 

   2. Na lista de conteúdo dinâmico, escolha **Expressão**.
   
   3. Introduza `rand(1,6)`a expressão e, em seguida, escolha **OK**.

      ![Configurar uma partição para o seu lote de alvo](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Esta função **rand** gera um número entre um e cinco. 
      Portanto, está a dividir este lote em cinco divisórias numeradas, que esta expressão define dinamicamente.

5. Guarde a aplicação lógica. A sua aplicação lógica de remetente agora se parece com este exemplo:

   ![Guarde a sua aplicação lógica de remetente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Teste as suas aplicações lógicas

Para testar a sua solução de loteamento, deixe as suas aplicações lógicas em funcionamento por alguns minutos. Em breve, começa-se a receber e-mails em grupos de cinco, todos com a mesma chave de partição.

A sua aplicação lógica de remetente de lote funciona a cada minuto, gera um número aleatório entre um e cinco, e usa este número gerado como chave de partição para o lote alvo onde as mensagens são enviadas. Cada vez que o lote tem cinco itens com a mesma chave de partição, a aplicação lógica do recetor do lote dispara e envia correio para cada mensagem.

> [!IMPORTANT]
> Quando terminar os testes, certifique-se de que desativa a aplicação lógica BatchSender para parar de enviar mensagens e evitar sobrecarregar a sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

* [Em lote e enviar mensagens EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Baseie-se em definições de aplicativos lógicos usando jSON](../logic-apps/logic-apps-author-definitions.md)
* [Construa uma aplicação sem servidores em Estúdio Visual com Aplicações e Funções da Lógica Azure](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceções e registo de erros para aplicações lógicas](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
