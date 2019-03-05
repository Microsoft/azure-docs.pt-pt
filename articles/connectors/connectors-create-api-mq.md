---
title: Ligar ao MQ server - Azure Logic Apps | Documentos da Microsoft
description: Enviar e receber mensagens com um Azure ou o servidor MQ no local e o Azure Logic Apps
author: valthom
manager: jeconnoc
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 59ef41b6d7bfcf8831eaa7d8d7e6af4ea279e415
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342246"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Ligar a um servidor do IBM MQ, a partir das aplicações lógicas com o conector MQ

Microsoft Connector para MQ envia e obtém mensagens armazenadas num MQ Server no local ou no Azure. Este conector inclui um cliente do Microsoft MQ que comunica com um servidor remoto do IBM MQ através de uma rede TCP/IP. Este documento é um guia de introdução para utilizar o conector MQ. Recomendamos que começa pela navegação de uma única mensagem numa fila e, em seguida, tentar outras ações.

O conector MQ inclui as seguintes ações. Não há nenhuma acionador.

- Procurar uma única mensagem sem a eliminação da mensagem do servidor IBM MQ
- Procurar um lote de mensagens sem eliminar as mensagens a partir do servidor do IBM MQ
- Receber uma única mensagem e eliminar a mensagem do servidor IBM MQ
- Receber um lote de mensagens e eliminar as mensagens a partir do servidor do IBM MQ
- Enviar uma única mensagem para o servidor do IBM MQ

## <a name="prerequisites"></a>Pré-requisitos

* Se utilizar um servidor MQ no local, [instalar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md) num servidor na sua rede. Se o servidor de MQ estiver publicamente disponível, ou disponíveis no Azure, em seguida, o gateway de dados não é utilizado ou necessárias.

    > [!NOTE]
    > O servidor onde está instalado o Gateway de dados no local tem de ter também o .NET Framework 4.6 instalado para o conector de MQ à função.

* Criar o recurso do Azure para o gateway de dados no local - [configurar a ligação de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

* Oficialmente IBM WebSphere MQ as versões suportadas:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. Na **quadro de início do Azure**, selecione **+** (sinal), **Web + móvel**e, em seguida **aplicação lógica**.
2. Introduza o **Name**, como MQTestApp, **subscrição**, **grupo de recursos**, e **localização** (utilizar a localização onde no local Ligação de Gateway de dados está configurada). Selecione **afixar ao dashboard**e selecione **criar**.  
![Criar aplicação lógica](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Adicionar um acionador

> [!NOTE]
> O conector de MQ não tem qualquer gatilhos. Então, usar outro acionador para iniciar a sua aplicação lógica, como o **periodicidade** acionador.

1. O **estruturador de aplicações lógicas** se abrir, selecione **periodicidade** na lista de acionadores comuns.
2. Selecione **editar** dentro do acionador de periodicidade.
3. Definir o **frequência** ao **dia**e defina o **intervalo** para **7**.

## <a name="browse-a-single-message"></a>Procurar uma única mensagem
1. Selecione **+ novo passo**e selecione **adicionar uma ação**.
2. Na caixa de pesquisa, escreva `mq`e, em seguida, selecione **MQ - mensagem de procurar**.  
![Procure a mensagem](media/connectors-create-api-mq/Browse_message.png)

3. Se não existir uma ligação de MQ existente, em seguida, crie a ligação:  

    1. Selecione **ligar através do gateway de dados no local**e introduza as propriedades do seu servidor MQ.  
    Para **servidor**, pode introduzir o nome do servidor MQ, ou introduza o endereço IP seguido por dois-pontos e o número de porta.
    2. O **gateway** lista pendente apresenta uma lista de quaisquer ligações de gateway existentes que tenham sido configuradas. Selecione o seu gateway.
    3. Quando terminar, selecione **Criar**. A ligação será semelhante ao seguinte:  
    ![Propriedades de ligação](media/connectors-create-api-mq/Connection_Properties.png)

4. As propriedades da ação, pode:  

    * Utilize o **fila** propriedade para aceder a um nome de fila diferente do que o que é definido na ligação
    * Utilize o **MessageId**, **CorrelationId**, **GroupId**e outras propriedades para navegar até uma mensagem com base nas propriedades de mensagem diferentes do MQ
    * Definir **IncludeInfo** ao **verdadeiro** para incluir informações de mensagens adicionais no resultado. Ou, defina-o como **False** para não incluir informações de mensagens adicionais no resultado.
    * Introduza um **tempo limite** valor para determinar o tempo aguardar que uma mensagem chegue numa fila vazia. Se nada for inserido, a primeira mensagem da fila é recuperada e não há tempo gasto a aguardar que uma mensagem seja exibida.  
    ![Procurar propriedades da mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Guarde** as suas alterações e, em seguida **executar** a aplicação lógica:  
![Guardar e executar](media/connectors-create-api-mq/Save_Run.png)

6. Após alguns segundos, são apresentados os passos da execução e pode observar os resultados. Selecione a marca de verificação verde para ver os detalhes de cada passo. Selecione **ver saídas em bruto** para ver detalhes adicionais sobre os dados de saída.  
![Procurar a saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

    Saída brutos:  
    ![Procurar saída brutos de mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Quando o **IncludeInfo** opção estiver definida como VERDADEIRO, é apresentado o resultado seguinte:  
![Mensagem de procurar incluem informações](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens
O **procurar mensagens** ação inclui um **BatchSize** opção para indicar o número de mensagens devem ser devolvidas da fila.  Se **BatchSize** não tem entradas, todas as mensagens são devolvidas. O resultado retornado é uma matriz de mensagens.

1. Ao adicionar o **procurar mensagens** ação, a primeira ligação que está configurada está selecionada por predefinição. Selecione **Alterar ligação** para criar uma ligação nova ou selecione uma ligação diferente.

2. O resultado da procura mensagens mostra:  
![Procurar a saída de mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Receber uma única mensagem
O **mensagem de recebimento** ação tem as mesmas entradas e saídas como o **mensagem procurar** ação. Ao usar **mensagem de recebimento**, a mensagem é eliminada da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens
O **receber mensagens** ação tem as mesmas entradas e saídas como o **procurar mensagens** ação. Ao usar **receber mensagens**, as mensagens são eliminadas da fila.

Se não existirem mensagens na fila ao efetuar uma procura ou receber, o passo falhar com a seguinte saída:  
![Erro de mensagens não MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Enviar uma mensagem
1. Ao adicionar o **mensagem de envio** ação, a primeira ligação que está configurada está selecionada por predefinição. Selecione **Alterar ligação** para criar uma ligação nova ou selecione uma ligação diferente. O válido **tipos de mensagem** são **datagrama**, **responder**, ou **pedir**.  
![Enviar mensagem de propriedades](media/connectors-create-api-mq/Send_Msg_Props.png)

2. A saída da mensagem de envio é semelhante ao seguinte:  
![Enviar a mensagem de saída](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver os acionadores e as ações definidas no swagger e também ver quaisquer limites na [detalhes do conector](/connectors/mq/).

## <a name="next-steps"></a>Passos Seguintes
[Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore os outros conectores disponíveis no Logic Apps em nosso [lista APIs](apis-list.md).
