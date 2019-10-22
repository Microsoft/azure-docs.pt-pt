---
title: Processar mensagens em lote como um grupo – aplicativos lógicos do Azure
description: Enviar e receber mensagens como lotes em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: b56a50fceec8ac6be966c0c58a82e94e0c977143
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680427"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Enviar, receber e processar mensagens em lote nos aplicativos lógicos do Azure

Para enviar e processar mensagens de forma específica como grupos, você pode criar uma solução de envio em lote que coleta mensagens em um *lote* até que os critérios especificados sejam atendidos para liberar e processar as mensagens em lotes. O envio em lote pode reduzir a frequência com que seu aplicativo lógico processa as mensagens. Este artigo mostra como criar uma solução de envio em lote criando dois aplicativos lógicos dentro da mesma assinatura do Azure, região do Azure e seguindo esta ordem específica: 

* O aplicativo lógico ["destinatário do lote"](#batch-receiver) , que aceita e coleta mensagens em um lote até que os critérios especificados sejam atendidos para liberar e processar essas mensagens.

  Certifique-se de criar primeiro o receptor do lote para que você possa selecionar posteriormente o destino do lote ao criar o remetente do lote.

* Um ou mais aplicativos lógicos de ["remetente do lote"](#batch-sender) , que enviam as mensagens para o receptor do lote criado anteriormente. 

   Você também pode especificar uma chave exclusiva, como um número de cliente, que *particiona* ou divide o lote de destino em subconjuntos lógicos com base nessa chave. Dessa forma, o aplicativo receptor pode coletar todos os itens com a mesma chave e processá-los juntos.

Verifique se o receptor do lote e o remetente do lote compartilham a mesma assinatura do Azure *e* a região do Azure. Se não forem, você não poderá selecionar o receptor do lote ao criar o remetente do lote, pois eles não ficam visíveis entre si.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, você precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou então, [Inscreva-se para uma assinatura paga conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de email com qualquer [provedor de email com suporte dos aplicativos lógicos do Azure](../connectors/apis-list.md)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Para usar o Visual Studio em vez do portal do Azure, certifique-se de [Configurar o Visual Studio para trabalhar com aplicativos lógicos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Criar receptor de lote

Antes que você possa enviar mensagens para um lote, esse lote deve primeiro existir como o destino onde você envia essas mensagens. Primeiro, você deve criar o aplicativo lógico "receptor do lote", que começa com o gatilho **lote** . Dessa forma, ao criar o aplicativo lógico "remetente do lote", você pode selecionar o aplicativo lógico do destinatário do lote. O destinatário do lote continua coletando mensagens até que os critérios especificados sejam atendidos para liberar e processar essas mensagens. Embora os receptores de lote não precisem saber nada sobre remetentes do lote, os remetentes do lote devem saber o destino no qual enviam as mensagens. 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico com este nome: "destinatáriolote" 

2. No designer de aplicativos lógicos, adicione o gatilho de **lote** , que inicia o fluxo de trabalho do aplicativo lógico. Na caixa de pesquisa, digite "Batch" como filtro. Selecione este gatilho: **mensagens em lotes**

   ![Adicionar gatilho "mensagens em lotes"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Defina essas propriedades para o receptor do lote: 

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Modo de lote** | - **embutido**: para definir critérios de liberação dentro do gatilho de lote <br>- **conta de integração**: para definir várias configurações de critérios de liberação por meio de uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Com uma conta de integração, você pode manter essas configurações em um único lugar, e não em aplicativos lógicos separados. | 
   | **Nome do lote** | O nome do lote, que é "Loteteste" neste exemplo, e aplica-se somente ao modo de lote **embutido** |  
   | **Critérios de liberação** | Aplica-se somente ao modo de lote **embutido** e seleciona os critérios a serem atendidos antes de processar cada lote: <p>**com base na contagem de mensagens**- : Libere o lote com base no número de mensagens coletadas pelo lote. <br>**com base no tamanho**do - : Libere o lote com base no tamanho total em bytes para todas as mensagens coletadas por esse lote. <br>**agenda**de - : libera o lote com base em um agendamento de recorrência, que especifica um intervalo e uma frequência. Nas opções avançadas, você também pode selecionar um fuso horário e fornecer uma data e hora de início. <br>- **selecionar tudo**: Use todos os critérios especificados. | 
   | **Contagem de mensagens** | O número de mensagens a serem coletadas no lote, por exemplo, 10 mensagens. O limite de um lote é de 8.000 mensagens. | 
   | **Tamanho do lote** | O tamanho total em bytes para coletar no lote, por exemplo, 10 MB. O limite de tamanho de um lote é de 80 MB. | 
   | **Agenda** | O intervalo e a frequência entre as versões do lote, por exemplo, 10 minutos. A recorrência mínima é de 60 segundos ou 1 minuto. Os minutos fracionários são efetivamente arredondados para um minuto. Para especificar um fuso horário ou uma data e hora de início, escolha **Mostrar opções avançadas**. | 
   ||| 

   > [!NOTE]
   > 
   > Se você alterar os critérios de liberação enquanto o gatilho ainda tiver mensagens em lote, mas não enviadas, o gatilho usará os critérios de liberação atualizados para lidar com as mensagens não enviadas. 

   Este exemplo mostra todos os critérios, mas para seus próprios testes, tente apenas um critério:

   ![Fornecer detalhes do gatilho de lote](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Agora, adicione uma ou mais ações que processam cada lote. 

   Para este exemplo, adicione uma ação que envia um email quando o gatilho do lote é acionado. 
   O gatilho é executado e envia um email quando o lote tem 10 mensagens, atinge 10 MB ou após a aprovação de 10 minutos.

   1. No gatilho lote, escolha **nova etapa**.

   2. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro.
   Com base em seu provedor de email, selecione um conector de email.

      Por exemplo, se você tiver uma conta pessoal, como @outlook.com ou @hotmail.com, selecione o conector Outlook.com. 
      Se você tiver uma conta do Gmail, selecione o conector do gmail. 
      Este exemplo usa o Outlook do Office 365. 

   3. Selecione esta ação: **enviar um email <*provedor de email* >**

      Por exemplo:

      ![Selecione a ação "enviar um email" para seu provedor de email](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Se tal lhe for solicitado, inicie sessão na sua conta de e-mail. 

6. Defina as propriedades para a ação que você adicionou.

   * Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

   * Na caixa **assunto** , quando a lista conteúdo dinâmico for exibida, selecione o campo **nome da partição** .

     ![Na lista de conteúdo dinâmico, selecione "nome da partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Posteriormente, no remetente do lote, você pode especificar uma chave de partição exclusiva que divide o lote de destino em subconjuntos lógicos em que você pode enviar mensagens. 
     Cada conjunto tem um número exclusivo que é gerado pelo aplicativo lógico remetente do lote. 
     Esse recurso permite que você use um único lote com vários subconjuntos e defina cada um deles com o nome que você fornecer.

     > [!IMPORTANT]
     > Uma partição tem um limite de 5.000 mensagens ou 80 MB. Se qualquer condição for atendida, os aplicativos lógicos poderão liberar o lote, mesmo quando a condição de liberação definida não for atendida.

   * Na caixa **corpo** , quando a lista conteúdo dinâmico for exibida, selecione o campo **ID da mensagem** . 

     O designer de aplicativos lógicos adiciona automaticamente um loop "for each" em volta da ação enviar email, pois essa ação trata a saída da ação anterior como uma coleção, em vez de um lote. 

     ![Para "corpo", selecione "ID da mensagem"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Guarde a aplicação lógica. Agora você criou um receptor do lote.

    ![Guardar a aplicação lógica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Se você estiver usando o Visual Studio, certifique-se de [implantar seu aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Caso contrário, você não poderá selecionar o destinatário do lote ao criar o remetente do lote.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Criar remetente do lote

Agora, crie um ou mais aplicativos lógicos do remetente do lote que enviam mensagens para o aplicativo lógico do destinatário do lote. Em cada remetente do lote, você especifica o destinatário do lote e o nome do lote, o conteúdo da mensagem e outras configurações. Opcionalmente, você pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos lógicos para coletar mensagens com essa chave. 

* Certifique-se de que você já [criou o receptor do lote](#batch-receiver) , portanto, ao criar o remetente do lote, você pode selecionar o destinatário do lote existente como o lote de destino. Embora os receptores de lote não precisem saber nada sobre remetentes do lote, os remetentes do lote devem saber para onde enviar mensagens. 

* Verifique se o receptor do lote e o remetente do lote compartilham a mesma região do Azure *e* a assinatura do Azure. Se não forem, você não poderá selecionar o receptor do lote ao criar o remetente do lote, pois eles não ficam visíveis entre si.

1. Crie outro aplicativo lógico com este nome: "Remetentelote"

   1. Na caixa de pesquisa, digite "recorrência" como filtro. 
   Selecione este gatilho: **recorrência-agenda**

      ![Adicionar o gatilho "recorrência-agendamento"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Defina a frequência e o intervalo para executar o aplicativo lógico remetente a cada minuto.

      ![Definir a frequência e o intervalo para o gatilho de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Adicione uma nova ação para enviar mensagens a um lote.

   1. No gatilho de recorrência, escolha **nova etapa**.

   2. Na caixa de pesquisa, digite "Batch" como filtro. 
   Selecione a lista **ações** e, em seguida, selecione esta ação: **escolher um fluxo de trabalho de aplicativos lógicos com gatilho em lote – enviar mensagens para o lote**

      ![Selecione "escolher um fluxo de trabalho de aplicativos lógicos com gatilho de lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selecione o aplicativo lógico do receptor do lote que você criou anteriormente.

      ![Selecione o aplicativo lógico "destinatário do lote"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista também mostra todos os outros aplicativos lógicos que têm gatilhos em lote. 
      > 
      > Se você estiver usando o Visual Studio e não vir nenhum receptor de lote para selecionar, verifique se você implantou o receptor do lote no Azure. Se você ainda não fez isso, saiba como [implantar seu aplicativo lógico do receptor do lote no Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Selecione esta ação: **Batch_messages-<*seu receptor-Batch-* >**

      ![Selecione esta ação: "Batch_messages-< your-Logic-app >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Defina as propriedades do remetente do lote:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome do lote** | O nome do lote definido pelo aplicativo lógico do receptor, que é "Loteteste" neste exemplo <p>**Importante**: o nome do lote é validado em tempo de execução e deve corresponder ao nome especificado pelo aplicativo lógico do destinatário. Alterar o nome do lote faz com que o remetente do lote falhe. | 
   | **Conteúdo da mensagem** | O conteúdo da mensagem que você deseja enviar | 
   ||| 

   Para este exemplo, adicione esta expressão, que insere a data e a hora atuais no conteúdo da mensagem que você envia para o lote:

   1. Clique dentro da caixa **conteúdo da mensagem** . 

   2. Quando a lista de conteúdo dinâmico for exibida, escolha **expressão**. 

   3. Insira a expressão `utcnow()` e, em seguida, escolha **OK**. 

      ![Em "conteúdo da mensagem", escolha "expressão", digite "UtcNow ()" e escolha "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Agora, configure uma partição para o lote. Na ação "Destinatáriolote", escolha **Mostrar opções avançadas** e defina estas propriedades:

   | Propriedade | Descrição | 
   |----------|-------------| 
   | **Nome da partição** | Uma chave de partição exclusiva opcional a ser usada para dividir o lote de destino em subconjuntos lógicos e coletar mensagens com base nessa chave | 
   | **ID da mensagem** | Um identificador de mensagem opcional que é um GUID (identificador global exclusivo) gerado quando vazio | 
   ||| 

   Para este exemplo, na caixa **nome da partição** , adicione uma expressão que gera um número aleatório entre um e cinco. Deixe a caixa **ID da mensagem** vazia.
   
   1. Clique dentro da caixa **nome da partição** para que a lista de conteúdo dinâmico seja exibida. 

   2. Na lista de conteúdo dinâmico, escolha **Expressão**.
   
   3. Insira a expressão `rand(1,6)` e, em seguida, escolha **OK**.

      ![Configurar uma partição para o lote de destino](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Essa função **Rand** gera um número entre um e cinco. 
      Portanto, você está dividindo esse lote em cinco partições numeradas, que essa expressão define dinamicamente.

5. Guarde a aplicação lógica. O aplicativo lógico do remetente agora é semelhante a este exemplo:

   ![Salvar o aplicativo lógico do remetente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testar seus aplicativos lógicos

Para testar sua solução de envio em lote, deixe seus aplicativos lógicos em execução por alguns minutos. Em breve, você começa a receber emails em grupos de cinco, todos com a mesma chave de partição.

O aplicativo lógico do remetente do lote é executado a cada minuto, gera um número aleatório entre um e cinco e usa esse número gerado como a chave de partição para o lote de destino para o qual as mensagens são enviadas. Cada vez que o lote tem cinco itens com a mesma chave de partição, o aplicativo lógico do receptor do lote é acionado e envia o email para cada mensagem.

> [!IMPORTANT]
> Quando terminar de testar, certifique-se de desabilitar o aplicativo lógico Remetentelote para interromper o envio de mensagens e evitar sobrecarregar sua caixa de entrada.

## <a name="next-steps"></a>Passos seguintes

* [Lote e enviar mensagens EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Criar definições de aplicativo lógico usando JSON](../logic-apps/logic-apps-author-definitions.md)
* [Criar um aplicativo sem servidor no Visual Studio com funções e aplicativos lógicos do Azure](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Tratamento de exceção e log de erros para aplicativos lógicos](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
