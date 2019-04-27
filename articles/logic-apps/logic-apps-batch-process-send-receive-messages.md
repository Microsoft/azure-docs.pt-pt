---
title: Processar mensagens do batch como um grupo ou uma coleção - Azure Logic Apps | Documentos da Microsoft
description: Enviar e receber mensagens como lotes no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: c33b1d46ecf710f050fc998ce27f6448337c6b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683774"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Enviar, receber e processar mensagens no Azure Logic Apps do batch

Para enviar e processar mensagens em conjunto de uma forma específica como grupos, pode criar uma solução de criação de batches que recolhe as mensagens para um *batch* até que os critérios especificados forem correspondidos para lançar e processar as mensagens em lote. Processamento em lote pode reduzir a frequência com que a sua aplicação lógica processa as mensagens. Este artigo mostra como criar uma solução de criação de batches ao criar duas aplicações de lógica dentro da mesma subscrição do Azure, região do Azure e seguir esta ordem específica: 

* O ["recetor de batch"](#batch-receiver) aplicação lógica, que aceita e recolhe as mensagens num lote até que os critérios especificados seja atendida para lançar e processar essas mensagens.

  Certifique-se de que primeiro criar o recetor de lote, de modo mais tarde pode selecionar o destino de batch ao criar o remetente de batch.

* Um ou mais ["remetente do batch"](#batch-sender) logic apps, o que enviam as mensagens para o destinatário de batch criado anteriormente. 

   Também pode especificar uma chave exclusiva, tais como um número de clientes, que *partições* ou divide o lote de destino em subconjuntos lógicos com base nessa chave. Dessa forma, a aplicação de recetor pode recolher todos os itens com a mesma chave e processá-las em conjunto.

Certifique-se do recetor de batch e o remetente de batch partilham a mesma subscrição do Azure *e* região do Azure. Caso contrário, não é possível selecionar o recetor de batch ao criar o remetente de batch porque eles não são visíveis para si.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [Inscreva-se uma subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de e-mail com qualquer [fornecedor de e-mail suportada pelo Azure Logic Apps](../connectors/apis-list.md)

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Para usar o Visual Studio, em vez do portal do Azure, certifique-se de que [configurar o Visual Studio para trabalhar com o Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Criar o recetor de batch

Antes de pode enviar mensagens para um lote, esse lote primeiro tem de existir como o destino onde envia essas mensagens. Primeiro, tem de criar a aplicação de lógica do "destinatário do batch", que começa com o **Batch** acionador. Dessa forma, ao criar a aplicação de lógica do "remetente do batch", pode selecionar a aplicação de lógica de destinatário do batch. O destinatário de batch continua a recolher as mensagens, até que os critérios especificados seja atendida para lançar e processar essas mensagens. Enquanto os recetores de batch não precisam saber nada sobre os remetentes de lote, os remetentes de batch tem de saber o destino em que enviam as mensagens. 

1. Na [portal do Azure](https://portal.azure.com) ou o Visual Studio, criar uma aplicação lógica com este nome: "BatchReceiver" 

2. No estruturador de aplicações lógicas, adicione a **Batch** acionador, que inicia o fluxo de trabalho de aplicação lógica. Na caixa de pesquisa, introduza "batch" como o filtro. Selecione este acionador: **Mensagens de batch**

   ![Adicionar acionador "Mensagens do Batch"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Defina essas propriedades para o recetor de batch: 

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Modo de lote** | - **Inline**: Para definir critérios de versão do acionador de lote <br>- **Conta de integração**: Para definir várias configurações de critérios de lançamento por meio de um [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Com uma conta de integração, pode manter estas configurações, tudo num único local, em vez de em aplicações lógicas separadas. | 
   | **Nome do lote** | O nome para o batch, o que é "TestBatch" neste exemplo e aplica-se apenas ao **Inline** modo de lote |  
   | **Critérios de versão** | Aplica-se apenas ao **Inline** modo e seleciona os critérios para atender às antes do processamento de cada lote do batch: <p>- **Com base de contagem de mensagens**: O batch com base no número de mensagens recolhidas pelo lote de versão. <br>- **Tamanho com base**: O batch com base no tamanho total em bytes para todas as mensagens recolhidas por esse lote de versão. <br>- **Agenda**: O batch com base numa agenda de periodicidade, que especifica um intervalo e a frequência de versão. Nas opções avançadas, também pode selecionar um fuso horário e forneça uma data de início e hora. <br>- **Selecionar tudo**: Utilize os critérios especificados. | 
   | **Contagem de mensagens** | O número de mensagens para recolher no lote, por exemplo, 10 mensagens. Limite de um lote é de 8000 mensagens. | 
   | **Tamanho do lote** | O tamanho total em bytes para recolher no lote, por exemplo, 10 MB. Limite de tamanho de um lote é 80 MB. | 
   | **Agenda** | O intervalo e a frequência entre as versões do batch, por exemplo, 10 minutos. A periodicidade mínima é de 60 segundos ou 1 minuto. Minutos fracionados efetivamente são arredondados para 1 minuto. Para especificar um fuso horário ou uma data de início e hora, escolha **Mostrar opções avançadas**. | 
   ||| 

   > [!NOTE]
   > 
   > Se alterar os critérios de versão, enquanto o acionador ainda tem em lotes, mas as mensagens não enviadas, o acionador utiliza os critérios de versão atualizada para lidar com as mensagens não enviadas. 

   Este exemplo mostra todos os critérios, mas para seu próprio teste, tente apenas um critério:

   ![Fornecer detalhes do acionador de lote](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Agora, adicione uma ou mais ações que processam cada lote. 

   Neste exemplo, adicione uma ação que envia um e-mail quando o acionador de lote é acionado. 
   O acionador é executado e envia um e-mail quando o lote tem 10 mensagens, atinge a 10 MB ou após 10 minutos passa.

   1. Sob o acionador de lote, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro.
   Com base no seu fornecedor de e-mail, selecione um conector de e-mail.

      Por exemplo, se tem uma conta pessoal, tal como @outlook.com ou @hotmail.com, seleccione o conector do Outlook.com. 
      Se tiver uma conta do Gmail, selecione o conector do Gmail. 
      Este exemplo utiliza o Outlook do Office 365. 

   3. Selecione a ação: **Enviar um e-mail - <*fornecedor de e-mail*>**

      Por exemplo:

      ![Selecione a ação "Enviar e-mail" para o seu fornecedor de e-mail](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

6. Defina as propriedades para a ação que adicionou.

   * Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

   * Na **assunto** caixa, quando for apresentada a lista de conteúdo dinâmico, selecione a **nome da partição** campo.

     ![Na lista de conteúdo dinâmico, selecione "Nome da partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Mais tarde, em que o remetente do batch, pode especificar uma chave de partição exclusiva que divide o lote de destino em subconjuntos lógicos em que pode enviar mensagens. 
     Cada conjunto tem um número exclusivo que é gerado pela aplicação de lógica de remetente do batch. 
     Esta capacidade permite-lhe utilizar um único lote com vários subconjuntos e definir cada subconjunto com o nome que fornece.

     > [!IMPORTANT]
     > Uma partição tem um limite de 5.000 mensagens ou 80 MB. Se a condição for cumprida, o Logic Apps pode versão lote, mesmo quando não for cumprida a condição de versão definido.

   * Na **corpo** caixa, quando for apresentada a lista de conteúdo dinâmico, selecione a **Id de mensagem** campo. 

     O estruturador de aplicações lógicas adiciona automaticamente um loop "For each" em torno da ação de e-mail de envio, porque essa ação trata a saída da ação anterior como uma coleção, em vez de um lote. 

     ![Para "Corpo", selecione "Id da mensagem"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Guarde a aplicação lógica. Acabou de criar um receptor de batch.

    ![Guardar a aplicação lógica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Se estiver a utilizar o Visual Studio, certifique-se de que [implementar a aplicação de lógica de destinatário do batch no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, não é possível selecionar o recetor de batch ao criar o remetente de batch.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Criar o remetente de batch

Agora, crie um ou mais aplicações do lógicas de remetente batch que enviam mensagens para a aplicação de lógica de destinatário do batch. Em cada remetente do batch, especifique o destinatário de batch e nome do lote, conteúdo da mensagem e quaisquer outras definições. Opcionalmente, pode fornecer uma chave de partição única para dividir o batch em subconjuntos lógicos para a recolha de mensagens com essa chave. 

* Certifique-se de que já [criado do recetor de batch](#batch-receiver) pelo quando cria o remetente do batch, pode selecionar o receptor do lote existente como o lote de destino. Enquanto recetores de lote não precisam saber nada sobre os remetentes de lote, os remetentes de batch tem de saber para onde enviar as mensagens. 

* Certifique-se do recetor de batch e o remetente de batch partilham a mesma região do Azure *e* subscrição do Azure. Caso contrário, não é possível selecionar o recetor de batch ao criar o remetente de batch porque eles não são visíveis para si.

1. Crie outra aplicação de lógica com este nome: "BatchSender"

   1. Na caixa de pesquisa, introduza "recurrence" como o filtro. 
   Selecione este acionador: **Periodicidade - agenda**

      ![Adicionar o acionador "– de periodicidade"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Defina a frequência e o intervalo para o remetente de executar a aplicação lógica a cada minuto.

      ![Definir a frequência e o intervalo para o acionador de periodicidade](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Adicione uma nova ação para enviar mensagens para um lote.

   1. No acionador de periodicidade, escolha **novo passo**.

   2. Na caixa de pesquisa, introduza "batch" como o filtro. 
   Selecione o **ações** lista e, em seguida, selecione a ação: **Escolha um fluxo de trabalho do Logic Apps com o acionador de lote - enviar mensagens para o lote**

      ![Selecione "Escolher um fluxo de trabalho do Logic Apps com o acionador de lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecione a aplicação de lógica de recetor de batch que criou anteriormente.

      ![Selecione a aplicação de lógica do "destinatário do batch"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista mostra também a outras aplicações lógicas com acionadores de lote. 
      > 
      > Se estiver a utilizar o Visual Studio, e não vir qualquer recetores de batch para selecionar, verifique se implementou do recetor de batch para o Azure. Se ainda não o, saiba como [implementar a aplicação de lógica de destinatário do batch no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Selecione a ação: **Batch_messages - <*receptor do batch*>**

      ![Selecione a ação: "Batch_messages - < your-logic-app >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Conjunto do batch de propriedades do remetente:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pela aplicação de lógica receptor, que é "TestBatch" neste exemplo <p>**Importante**: O nome do lote é validado em tempo de execução e tem de corresponder ao nome especificado pela aplicação de lógica do recetor. Alterar o nome do lote faz com que o remetente de batch efetuar a ativação. | 
   | **Conteúdo da mensagem** | O conteúdo para a mensagem que pretende enviar | 
   ||| 

   Neste exemplo, adicione esta expressão, que insere a data e hora atuais no conteúdo da mensagem que envia para o batch:

   1. Clique no interior da **o conteúdo da mensagem** caixa. 

   2. Quando for apresentada a lista de conteúdo dinâmico, escolha **expressão**. 

   3. Introduza a expressão `utcnow()`e, em seguida, escolha **OK**. 

      ![Em "O conteúdo da mensagem", escolha "Expressão", em seguida, introduza "utcnow" e selecionar "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Configure agora uma partição para o batch. Na ação "BatchReceiver", escolha **Mostrar opções avançadas** e defina estas propriedades:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome da partição** | Uma chave de partição exclusiva opcionais a utilizar para dividir o lote de destino em subconjuntos de lógicos e recolher mensagens com base nessa chave | 
   | **Id da mensagem** | Um identificador de mensagem opcional que é um identificador global exclusivo gerado (GUID) quando vazio | 
   ||| 

   Neste exemplo, no **nome da partição** caixa, adicione uma expressão que gera um número aleatório entre um e cinco. Deixe o **Id da mensagem** caixa vazia.
   
   1. Clique no interior da **nome da partição** caixa para que a lista de conteúdo dinâmico apareça. 

   2. Na lista de conteúdo dinâmico, escolha **Expressão**.
   
   3. Introduza a expressão `rand(1,6)`e, em seguida, escolha **OK**.

      ![Configurar uma partição para seu lote de destino](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Isso **rand** função gera um número entre um e cinco. 
      Então, são dividindo esse lote em cinco partições numeradas, dinamicamente define essa expressão.

5. Guarde a aplicação lógica. A aplicação de lógica de remetente é agora semelhante a este exemplo:

   ![Guarde a aplicação de lógica de remetente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar as suas aplicações lógicas

Para testar a sua solução de criação de batches, deixe as logic apps em execução durante alguns minutos. Em breve, começa a receber e-mails em grupos de cinco, tudo com a mesma chave de partição.

A aplicação de lógica de remetente do batch é executado a cada minuto, gera um número aleatório entre um e cinco e utiliza este número gerado como a chave de partição para o lote de destino em que as mensagens são enviadas. Sempre que o batch tem cinco itens com a mesma chave de partição, a aplicação de lógica de recetor de batch é acionado e envia correio para cada mensagem.

> [!IMPORTANT]
> Quando terminar de teste, certifique-se de que desative a aplicação de lógica de BatchSender para parar o envio de mensagens e evitar a sobrecarga de sua caixa de entrada.

## <a name="next-steps"></a>Passos Seguintes

* [Lote e enviar mensagens EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Criar em definições de aplicação lógica com o JSON](../logic-apps/logic-apps-author-definitions.md)
* [Criar uma aplicação sem servidor no Visual Studio com o Azure Logic Apps e funções](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Registo de erros para o logic apps e manipulação de exceção](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
