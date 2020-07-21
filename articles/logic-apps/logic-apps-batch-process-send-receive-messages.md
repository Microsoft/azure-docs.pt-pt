---
title: Mensagens de processo de lote como um grupo
description: Enviar e receber mensagens em grupos entre os seus fluxos de trabalho utilizando o processamento de lotes em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: f4863ff0005f09ca325125667062455357687fdd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521144"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Enviar, receber e processar mensagens em Azure Logic Apps

Para enviar e processar mensagens em conjunto de forma específica como grupos, pode criar uma solução de lote que recolhe mensagens num *lote* até que os seus critérios especificados sejam cumpridos para libertar e processar as mensagens em lote. O lote pode reduzir a frequência com que a sua aplicação lógica processa mensagens. Este artigo mostra como construir uma solução de loteamento criando duas aplicações lógicas dentro da mesma subscrição Azure, região de Azure, e seguindo esta ordem específica: 

* A aplicação lógica ["recetor de lote",](#batch-receiver) que aceita e recolhe mensagens num lote até que os critérios especificados sejam cumpridos para libertar e processar essas mensagens.

  Certifique-se de que cria primeiro o recetor do lote para que possa selecionar mais tarde o destino do lote quando criar o remetente do lote.

* Uma ou mais aplicações [lógicas de "remetente de lote",](#batch-sender) que enviam as mensagens para o recetor de lote previamente criado. 

   Também pode especificar uma chave única, como um número de cliente, que *divide* o lote-alvo em subconjuntos lógicos com base nessa tecla. Desta forma, a aplicação do recetor pode recolher todos os itens com a mesma chave e processá-los em conjunto.

Certifique-se de que o seu recetor de lote e remetente de lote partilham a mesma subscrição Azure *e* a região de Azure. Se não o fizerem, não é possível selecionar o recetor do lote quando se cria o remetente do lote porque não são visíveis um para o outro.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/).
Ou, [inscreva-se para uma subscrição pay-as-you-go](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de e-mail com qualquer [fornecedor de e-mail suportado pela Azure Logic Apps](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para utilizar o Visual Studio em vez do portal Azure, certifique-se de [configurar o Visual Studio para trabalhar com as Aplicações Lógicas.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Criar recetor de lote

Antes de enviar mensagens para um lote, esse lote deve primeiro existir como destino para onde envia essas mensagens. Por isso, primeiro, tem de criar a aplicação lógica "recetor de lote", que começa com o gatilho **do Lote.** Assim, quando criar a aplicação lógica "batch sender", pode selecionar a aplicação lógica do recetor de lote. O recetor do lote continua a recolher mensagens até que os seus critérios especificados seja cumprido para libertar e processar essas mensagens. Embora os recetores de lotes não precisem de saber nada sobre os remetentes de lotes, os remetentes de lotes devem saber o destino onde enviam as mensagens. 

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, crie uma aplicação lógica com este nome: "BatchReceiver" 

2. No Logic Apps Designer, adicione o gatilho **Batch,** que inicia o fluxo de trabalho da aplicação lógica. Na caixa de pesquisa, introduza "lote" como filtro. Selecione este gatilho: **Mensagens de lote**

   ![Adicionar gatilho de "mensagens de lote"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Desa esta aduso destas propriedades para o recetor do lote: 

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Modo lote** | - **Inline**: Para definir critérios de libertação dentro do gatilho do lote <br>- **Conta de Integração**: Para definir várias configurações de critérios de libertação através de uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Com uma conta de integração, pode manter estas configurações num só lugar e não em aplicações lógicas separadas. | 
   | **Nome do lote** | O nome do seu lote, que é "TestBatch" neste exemplo, e aplica-se apenas ao modo de lote **Inline** |  
   | **Critérios de libertação** | Aplica-se apenas ao modo de lote **Inline** e seleciona os critérios a cumprir antes de processar cada lote: <p>- **Contagem de mensagens com base**: Liberte o lote com base no número de mensagens recolhidas pelo lote. <br>- **Base de tamanho**: Liberte o lote com base no tamanho total dos bytes para todas as mensagens recolhidas por esse lote. <br>- **Horário**: Liberte o lote com base num calendário de recorrência, que especifica um intervalo e frequência. Nas opções avançadas, também pode selecionar um fuso horário e fornecer uma data e hora de início. <br>- **Selecione todos**: Utilize todos os critérios especificados. | 
   | **Contagem de mensagens** | O número de mensagens a recolher no lote, por exemplo, 10 mensagens. O limite de um lote é de 8.000 mensagens. | 
   | **Tamanho do lote** | O tamanho total em bytes para recolher no lote, por exemplo, 10 MB. O limite de tamanho de um lote é de 80 MB. | 
   | **Agenda** | O intervalo e a frequência entre os lançamentos do lote, por exemplo, 10 minutos. A recorrência mínima é de 60 segundos ou 1 minuto. Os minutos fracionados são efetivamente arredondados até 1 minuto. Para especificar um fuso horário ou uma data e hora de início, escolha **opções avançadas do Show**. | 
   ||| 

   > [!NOTE]
   > 
   > Se alterar os critérios de libertação enquanto o gatilho ainda tiver mensagens em lotadas, mas não solicitadas, o gatilho utiliza os critérios de libertação atualizados para manusear as mensagens não solicitadas. 

   Este exemplo mostra todos os critérios, mas para o seu próprio teste, experimente apenas um critério:

   ![Fornecer detalhes do gatilho do lote](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Agora adicione uma ou mais ações que processam cada lote. 

   Para este exemplo, adicione uma ação que envia um e-mail quando o lote dispara. 
   O gatilho corre e envia um e-mail quando o lote tem 10 mensagens, atinge 10 MB, ou após 10 minutos de passagem.

   1. Sob o gatilho do lote, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro.
   Com base no seu fornecedor de e-mail, selecione um conector de e-mail.

      Por exemplo, se tiver uma conta pessoal, como @outlook.com @hotmail.com ou, selecione o conector Outlook.com. Este exemplo utiliza o conector Do Office 365 Outlook.

   3. Selecione esta ação: **Enviar um e-mail - <fornecedor de > *e-mail* **

      Por exemplo:

      ![Selecione ação "Enviar um e-mail" para o seu fornecedor de e-mail](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

6. Desateia as propriedades para a ação que adicionou.

   * Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

   * Na caixa **Assunto,** quando aparecer a lista de conteúdos dinâmicos, selecione o campo **Nome de Partição.**

     ![Na lista de conteúdos dinâmicos, selecione "Nome de partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Mais tarde, no remetente do lote, pode especificar uma chave de partição única que divide o lote alvo em subconjuntos lógicos onde pode enviar mensagens. 
     Cada conjunto tem um número único que é gerado pela aplicação lógica do remetente de lotes. 
     Esta capacidade permite-lhe utilizar um único lote com vários subconjuntos e definir cada subconjunto com o nome que fornece.

     > [!IMPORTANT]
     > Uma divisória tem um limite de 5.000 mensagens ou 80 MB. Se uma das condições for cumprida, as Aplicações Lógicas poderão libertar o lote, mesmo quando a sua condição de libertação definida não for satisfeita.

   * Na caixa **'Corpo',** quando aparecer a lista de conteúdos dinâmicos, selecione o campo **de ID de mensagem.** 

     O Logic Apps Designer adiciona automaticamente um loop "Para cada um" em torno da ação de envio de e-mail porque essa ação trata a saída da ação anterior como uma coleção, em vez de um lote. 

     ![Para "Corpo", selecione "Message Id"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Guarde a sua aplicação lógica. Agora criaste um recetor de lote.

    ![Guardar a aplicação lógica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Se estiver a utilizar o Visual Studio, certifique-se de [que implementa a sua aplicação lógica recetora de lote para a Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, não é possível selecionar o recetor do lote quando criar o remetente do lote.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Criar remetente de lote

Agora crie uma ou mais aplicações lógicas de remetente de lote que enviam mensagens para a aplicação lógica do recetor do lote. Em cada remetente de lote, especifique o nome do recetor do lote e do lote, o conteúdo da mensagem e quaisquer outras definições. Pode opcionalmente fornecer uma chave de partição única para dividir o lote em subconjuntos lógicos para recolher mensagens com essa chave. 

* Certifique-se de que já [criou o seu recetor](#batch-receiver) de lote para que, quando criar o seu remetente de lote, possa selecionar o recetor de lote existente como lote de destino. Embora os recetores de lotes não precisem de saber nada sobre os remetentes de lotes, os remetentes de lotes devem saber para onde enviar mensagens. 

* Certifique-se de que o seu recetor de lote e o remetente do lote partilham a mesma região Azure *e* a subscrição do Azure. Se não o fizerem, não é possível selecionar o recetor do lote quando se cria o remetente do lote porque não são visíveis um para o outro.

1. Criar outra aplicação lógica com este nome: "BatchSender"

   1. Na caixa de pesquisa, introduza a "recorrência" como filtro. 
   Selecione este gatilho: **Recorrência - Agenda**

      ![Adicione o gatilho "Recorrência - Agendar"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Desaprote a frequência e o intervalo para executar a aplicação lógica do remetente a cada minuto.

      ![Definir frequência e intervalo para o gatilho de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Adicione uma nova ação para enviar mensagens a um lote.

   1. Sob o gatilho de recorrência, escolha **Novo passo**.

   2. Na caixa de pesquisa, introduza "lote" como filtro. 
   Selecione a lista **de Ações** e, em seguida, selecione esta ação: **Escolha um fluxo de trabalho de Apps lógicas com gatilho de lote - Envie mensagens para lote**

      ![Selecione "Escolha um fluxo de trabalho de Apps lógicas com gatilho de lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecione a sua aplicação lógica recetora de lote que criou anteriormente.

      ![Selecione a aplicação lógica do "recetor de lote"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista também mostra quaisquer outras aplicações lógicas que tenham gatilhos de lote. 
      > 
      > Se estiver a utilizar o Visual Studio e não vir nenhum recetor de lote para selecionar, verifique se implementou o recetor do lote para o Azure. Caso não o tenha, aprenda a implementar a [sua aplicação lógica recetora de lote para o Azure.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 

   4. Selecione esta ação: **Batch_messages - <o seu *recetor* > de lote**

      ![Selecione esta ação: "Batch_messages - <a sua aplicação lógica>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Desa cimente as propriedades do remetente do lote:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pela app lógica do recetor, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado no tempo de execução e deve corresponder ao nome especificado pela aplicação lógica do recetor. Mudar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo de mensagens** | O conteúdo da mensagem que deseja enviar | 
   ||| 

   Para este exemplo, adicione esta expressão, que insere a data e a hora atuais no conteúdo da mensagem que envia para o lote:

   1. Clique dentro da caixa de conteúdo de **mensagem.** 

   2. Quando aparecer a lista de conteúdos dinâmicos, escolha **Expressão**. 

   3. Introduza a expressão `utcnow()` , e depois escolha **OK**. 

      ![Em "Conteúdo de Mensagem", escolha "Expression", insira "utcnow()", e escolha "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Agora crie uma divisória para o lote. Na ação "BatchReceiver", escolha **opções avançadas do Show** e desemprete estas propriedades:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome da partição** | Uma chave de partição única opcional para dividir o lote alvo em subconjuntos lógicos e recolher mensagens com base nessa chave | 
   | **Id de mensagem** | Um identificador de mensagens opcional que é um identificador globalmente único (GUID) quando vazio | 
   ||| 

   Para este exemplo, na caixa Nome de **Partição,** adicione uma expressão que gere um número aleatório entre um e cinco. Deixe a caixa **de identificação** da mensagem vazia.
   
   1. Clique dentro da caixa Nome de **Partição** para que apareça a lista de conteúdos dinâmicos. 

   2. Na lista de conteúdo dinâmico, escolha **Expressão**.
   
   3. Introduza a expressão `rand(1,6)` , e depois escolha **OK**.

      ![Configurar uma divisória para o seu lote de destino](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Esta função **rand** gera um número entre um e cinco. 
      Então você está dividindo este lote em cinco divisórias numeradas, que esta expressão define dinamicamente.

5. Guarde a sua aplicação lógica. A sua aplicação lógica de remetente agora parece semelhante a este exemplo:

   ![Guarde o seu aplicativo de lógica remetente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Teste as suas aplicações lógicas

Para testar a sua solução de lote, deixe as suas aplicações lógicas em funcionamento durante alguns minutos. Em breve, começas a receber e-mails em grupos de cinco, todos com a mesma chave de partição.

A sua aplicação lógica de remetente de lote é executado a cada minuto, gera um número aleatório entre um e cinco, e utiliza este número gerado como a chave de partição para o lote-alvo onde as mensagens são enviadas. Cada vez que o lote tem cinco itens com a mesma tecla de partição, a sua aplicação lógica recetora de lote dispara e envia correio para cada mensagem.

> [!IMPORTANT]
> Quando terminar os testes, certifique-se de que desativa a aplicação lógica BatchSender para parar de enviar mensagens e evitar sobrecarregar a sua caixa de entrada.

## <a name="next-steps"></a>Próximos passos

* [Em lote e enviar mensagens EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Baseie-se nas definições de aplicativos lógicos utilizando o JSON](../logic-apps/logic-apps-author-definitions.md)
* [Construa uma aplicação sem servidor no Estúdio Visual com apps e funções Azure Logic](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceções e registo de erros para aplicações lógicas](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
