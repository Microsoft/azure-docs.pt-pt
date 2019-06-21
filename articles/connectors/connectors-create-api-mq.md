---
title: Ligar ao servidor do IBM MQ - Azure Logic Apps
description: Enviar e receber mensagens com um servidor do IBM MQ do Azure ou no local e o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273117"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ligar a um servidor do IBM MQ a partir do Azure Logic Apps

O conector do IBM MQ envia e obtém mensagens armazenadas num servidor do IBM MQ no local ou no Azure. Este conector inclui um cliente do Microsoft MQ que comunica com um servidor remoto do IBM MQ através de uma rede TCP/IP. Este artigo fornece um guia de introdução para utilizar o conector MQ. Pode começar por uma única mensagem numa fila de navegação e, em seguida, tente outras ações.

O conector do IBM MQ inclui estas ações, mas não fornece nenhuma acionador:

- Procurar uma única mensagem sem a eliminação da mensagem do servidor do IBM MQ
- Procurar um lote de mensagens sem eliminar as mensagens a partir do servidor do IBM MQ
- Receber uma única mensagem e eliminar a mensagem do servidor do IBM MQ
- Receber um lote de mensagens e eliminar as mensagens a partir do servidor do IBM MQ
- Enviar uma única mensagem para o servidor do IBM MQ

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a utilizar um servidor MQ no local, [instalar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md) num servidor na sua rede. O servidor onde está instalado o gateway de dados no local tem de ter também o .NET Framework 4.6 instalado para o conector MQ para trabalhar. Tem também de criar um recurso no Azure para o gateway de dados no local. Para obter mais informações, consulte [configurar a ligação de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  No entanto, se o servidor MQ estiver publicamente disponíveis ou estão disponíveis no Azure, não tem de utilizar o gateway de dados.

* Oficialmente IBM WebSphere MQ as versões suportadas:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* A aplicação de lógica onde pretende adicionar a ação de MQ. Esta aplicação lógica terá de utilizar a mesma localização que a ligação de gateway de dados no local e já tem de ter um acionador que inicia o fluxo de trabalho. 

  O conector MQ não tem qualquer gatilhos, então precisa adicionar um acionador à sua aplicação lógica pela primeira vez. Por exemplo, pode utilizar o acionador de periodicidade. Se estiver familiarizado com aplicações lógicas, experimente isto [início rápido para criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Procurar uma única mensagem

1. Na sua aplicação lógica, sob o acionador ou outra ação, escolha **novo passo**. 

1. Na caixa de pesquisa, escreva "mq" e selecione a ação: **Procure a mensagem**

   ![Procure a mensagem](media/connectors-create-api-mq/Browse_message.png)

1. Se não tiver uma ligação de MQ existente, crie a ligação:  

   1. Na ação, selecione **ligar através do gateway de dados no local**.
   
   1. Introduza as propriedades do seu servidor MQ.  

      Para **servidor**, pode introduzir o nome do servidor MQ, ou introduza o endereço IP seguido por dois-pontos e o número de porta.
    
   1. Abra o **gateway** lista, que mostra a qualquer configurado anteriormente ligações do gateway. Selecione o seu gateway.
    
   1. Quando tiver terminado, escolha **Create** (Criar). 
   
      A ligação é semelhante a este exemplo:

      ![Propriedades de ligação](media/connectors-create-api-mq/Connection_Properties.png)

1. Configure propriedades da ação:

   * **fila**: Especifique uma fila que é diferente da conexão.

   * **MessageId**, **CorrelationId**, **GroupId**e outras propriedades: Procure uma mensagem com base nas propriedades de mensagem diferentes do MQ

   * **IncludeInfo**: Especifique **True** para incluir informações de mensagens adicionais no resultado. Em alternativa, especificar **False** para não incluir informações de mensagens adicionais no resultado.

   * **Tempo limite**: Introduza um valor para determinar o tempo aguardar que uma mensagem chegue numa fila vazia. Se nada for inserido, a primeira mensagem da fila é recuperada e não há tempo gasto a aguardar que uma mensagem seja exibida.

     ![Procurar propriedades da mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Guarde** as suas alterações e, em seguida **executar** a aplicação lógica.

   ![Guardar e executar](media/connectors-create-api-mq/Save_Run.png)

   Após a conclusão da execução, são apresentados os passos de execução e pode rever a saída.

1. Para rever os detalhes para cada passo, escolha a marca de verificação verde. Para obter mais informações sobre os dados de saída de rever, escolha **Mostrar saídas em bruto**.

   ![Procurar a saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

   Eis algumas saídas em bruto de exemplo:

   ![Procurar saída brutos de mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se definir **IncludeInfo** como true, o seguinte resultado é apresentado:

   ![Mensagem de procurar incluem informações](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

O **procurar mensagens** ação inclui um **BatchSize** opção para indicar o número de mensagens devem ser devolvidas da fila.  Se **BatchSize** não tem entradas, todas as mensagens são devolvidas. O resultado retornado é uma matriz de mensagens.

1. Quando adiciona a **procurar mensagens** ação, a primeira anteriormente ligação configurada está selecionada por predefinição. Para criar uma nova ligação, escolha **Alterar ligação**. Em alternativa, selecione uma ligação diferente.

1. Após a aplicação lógica execução estiver concluída, eis algumas saídas de exemplo do **procurar mensagens** ação:

   ![Procurar a saída de mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Receber mensagem única

O **mensagem de recebimento** ação tem as mesmas entradas e saídas como o **mensagem procurar** ação. Ao usar **mensagem de recebimento**, a mensagem é eliminada da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

O **receber mensagens** ação tem as mesmas entradas e saídas como o **procurar mensagens** ação. Ao usar **receber mensagens**, as mensagens são eliminadas da fila.

Se não existirem mensagens na fila ao efetuar uma procura ou receber, o passo falhar com este resultado:  

![Erro de mensagens não MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Enviar mensagem

Quando adiciona a **enviar mensagens** ação, a primeira anteriormente ligação configurada está selecionada por predefinição. Para criar uma nova ligação, escolha **Alterar ligação**. Em alternativa, selecione uma ligação diferente.

1. Selecione um tipo de mensagem válido: **Datagrama**, **resposta**, ou **do pedido**  

   ![Enviar mensagem de propriedades](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Depois da aplicação lógica concluída em execução, eis algumas saídas de exemplo do **mensagem de envio** ação:

   ![Enviar a mensagem de saída](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre limites e ações, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/mq/).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
