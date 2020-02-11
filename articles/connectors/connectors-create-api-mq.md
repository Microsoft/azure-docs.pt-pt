---
title: Ligar ao servidor IBM MQ
description: Envie e recupere mensagens com um servidor IBM MQ Azure ou no local e aplicações lógicas Azure
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 489f53a4f4c1c0d5bd782f42a9daf73217234793
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118047"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ligue-se a um servidor IBM MQ a partir de Aplicações Lógicas Azure

O conector IBM MQ envia e recupera mensagens armazenadas num servidor IBM MQ nas instalações ou no Azure. Este conector inclui um cliente Microsoft MQ que comunica com um servidor IBM MQ remoto através de uma rede TCP/IP. Este artigo fornece um guia de arranque para utilizar o conector MQ. Pode começar por navegar numa única mensagem numa fila e depois experimentar outras ações.

O conector IBM MQ inclui estas ações, mas não fornece gatilhos:

- Navegue numa única mensagem sem apagar a mensagem do servidor IBM MQ
- Navegue num lote de mensagens sem apagar as mensagens do servidor IBM MQ
- Receba uma única mensagem e apague a mensagem do servidor IBM MQ
- Receba um lote de mensagens e elimine as mensagens do servidor IBM MQ
- Envie uma única mensagem para o servidor IBM MQ

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a utilizar um servidor MQ no local, [instale a porta de dados no local](../logic-apps/logic-apps-gateway-install.md) num servidor dentro da sua rede. O servidor onde o portal de dados no local está instalado também deve ter .NET Framework 4.6 instalado para o conector MQ funcionar. Deve também criar um recurso em Azure para a porta de dados no local. Para mais informações, consulte [Configurar a ligação de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  No entanto, se o seu servidor MQ estiver disponível publicamente ou disponível dentro do Azure, não tem de utilizar o portal de dados.

* Versões mQ da IBM WebSphere suportadas oficialmente:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* A aplicação lógica onde pretende adicionar a ação MQ. Esta aplicação lógica deve utilizar a mesma localização que a sua ligação de gateway de dados no local e já deve ter um gatilho que inicie o seu fluxo de trabalho. 

  O conector MQ não tem gatilhos, por isso deve adicionar primeiro um gatilho à sua aplicação lógica. Por exemplo, pode utilizar o gatilho de recorrência. Se você é novo em aplicativos lógicos, experimente este [quickstart para criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Navegue numa única mensagem

1. Na sua aplicação lógica, sob o gatilho ou outra ação, escolha **novo passo**. 

1. Na caixa de pesquisa, escreva "mq", e selecione esta ação: **Navegue na mensagem**

   ![Navegue na mensagem](media/connectors-create-api-mq/Browse_message.png)

1. Se não tiver uma ligação MQ existente, crie a ligação:  

   1. Na ação, selecione **Connect via gateway de dados no local**.
   
   1. Introduza as propriedades para o seu servidor MQ.  

      Para **o Servidor,** pode introduzir o nome do servidor MQ ou introduzir o endereço IP seguido de um cólon e o número de porta.
    
   1. Abra a lista de **gateways,** que mostra quaisquer ligações de gateway previamente configuradas. Selecione o seu portal.
    
   1. Quando tiver terminado, escolha **Create** (Criar). 
   
      A sua ligação parece-se com este exemplo:

      ![Propriedades de Ligação](media/connectors-create-api-mq/Connection_Properties.png)

1. Configurar as propriedades da ação:

   * **Fila**: Especifique uma fila diferente da ligação.

   * **MessageId**, **CorrelationId,** **GroupId,** e outras propriedades: Procure uma mensagem baseada nas diferentes propriedades da mensagem MQ

   * **IncludeInfo**: Especifique **true** para incluir informações adicionais de mensagem na saída. Ou, especifique **Falso** para não incluir informações adicionais de mensagem na saída.

   * **Tempo de saída**: Insira um valor para determinar quanto tempo esperar que uma mensagem chegue numa fila vazia. Se nada for introduzido, a primeira mensagem na fila é recuperada, e não há tempo gasto à espera que apareça uma mensagem.

     ![Navegue propriedades de mensagens](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Guarde** as suas alterações e, em seguida, **execute** a sua aplicação lógica.

   ![Salvar e correr](media/connectors-create-api-mq/Save_Run.png)

   Após o acabamento da corrida, os passos da corrida são mostrados, e você pode rever a saída.

1. Para rever os detalhes para cada passo, escolha a marca de verificação verde. Para rever mais informações sobre os dados de saída, escolha **Mostrar saídas cruas**.

   ![Ver saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

   Aqui está uma amostra de saída crua:

   ![Navegue na saída bruta da mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se definir o **IncludeInfo** como verdadeiro, a seguinte saída é visualizada:

   ![A mensagem de navegação inclua informações](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Navegue em várias mensagens

A ação de **mensagens Browse** inclui uma opção **BatchSize** para indicar quantas mensagens devem ser devolvidas da fila.  Se **o BatchSize** não tiver entrada, todas as mensagens são devolvidas. A saída devolvida é uma série de mensagens.

1. Quando adiciona a ação de **mensagens Browse,** a primeira ligação previamente configurada é selecionada por padrão. Para criar uma nova ligação, escolha a **ligação Change**. Ou, selecione uma ligação diferente.

1. Após o acabamento da aplicação lógica, aqui está alguma saída de amostra da ação de **mensagens Browse:**

   ![Ver a saída de mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Receber uma única mensagem

A ação **de mensagem Receber** tem as mesmas inputs e saídas que a ação da mensagem **Browse.** Ao utilizar a **mensagem Receber,** a mensagem é apagada da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **de mensagens Receber** tem as mesmas inputs e saídas que a ação de **mensagens Browse.** Ao utilizar **mensagens de receção,** as mensagens são apagadas da fila.

Se não houver mensagens na fila ao fazer uma navegação ou receber, o passo falha com esta saída:  

![MQ Sem erro de mensagem](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Enviar mensagem

Quando adiciona a ação **Enviar mensagens,** a primeira ligação previamente configurada é selecionada por defeito. Para criar uma nova ligação, escolha a **ligação Change**. Ou, selecione uma ligação diferente.

1. Selecione um tipo de mensagem válido: **Datagram,** **Resposta**ou **Pedido**  

   ![Enviar Adereços Msg](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Depois que a aplicação lógica termina em execução, aqui está alguma saída de amostra da ação **enviar mensagem:**

   ![Enviar Saída Msg](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre ações e limites, descritos pela descrição openapi do conector (ex-Swagger), reveja a página de [referência](/connectors/mq/)do conector .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
