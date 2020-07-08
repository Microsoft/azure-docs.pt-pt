---
title: Ligue-se ao servidor MQ IBM
description: Enviar e recuperar mensagens com um servidor MQ Azure ou no local e aplicações lógicas Azure
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/14/2020
tags: connectors
ms.openlocfilehash: e9e554fdc092e49f5a87049de0e3dc3163105f58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609508"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ligar a um servidor IBM MQ a partir do Azure Logic Apps

O conector IBM MQ envia e recupera mensagens armazenadas num servidor IBM MQ nas instalações ou no Azure. Este conector inclui um cliente Microsoft MQ que comunica com um servidor MQ IBM remoto através de uma rede TCP/IP. Este artigo fornece um guia de arranque para utilizar o conector MQ. Pode começar por navegar uma única mensagem numa fila e depois experimentar outras ações.

O conector IBM MQ inclui estas ações, mas não fornece gatilhos:

- Navegue numa única mensagem sem apagar a mensagem do servidor IBM MQ.
- Navegue num lote de mensagens sem apagar as mensagens do servidor IBM MQ.
- Receba uma única mensagem e elimine a mensagem do servidor IBM MQ.
- Receba um lote de mensagens e elimine as mensagens do servidor IBM MQ.
- Envie uma única mensagem para o servidor IBM MQ.

Aqui estão as versões MQ da IBM websphere oficialmente suportadas:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a utilizar um servidor MQ no local, [instale o portal de dados no local](../logic-apps/logic-apps-gateway-install.md) num servidor dentro da sua rede. O servidor onde está instalado o gateway de dados no local também deve ter .NET Framework 4.6 instalado para o conector MQ funcionar.

  Depois de terminar a instalação do gateway, também deve criar um recurso em Azure para o portal de dados no local. Para obter mais informações, consulte [Configurar a ligação de porta de entrada de dados](../logic-apps/logic-apps-gateway-connection.md).

  Se o seu servidor MQ estiver disponível publicamente ou disponível dentro do Azure, não terá de utilizar o portal de dados.

* A aplicação lógica onde pretende adicionar a ação MQ. Esta aplicação lógica deve utilizar a mesma localização que a sua ligação de gateway de dados no local e já deve ter um gatilho que inicie o seu fluxo de trabalho.

  O conector MQ não tem gatilhos, por isso deve adicionar primeiro um gatilho à sua aplicação lógica. Por exemplo, pode utilizar o gatilho de Recorrência. Se é novo em aplicações lógicas, experimente este [quickstart para criar a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Criar conexão MQ

Se ainda não tiver uma ligação MQ quando adicionar uma ação MQ, é solicitado que crie a ligação, por exemplo:

![Fornecer informações de conexão](media/connectors-create-api-mq/connection-properties.png)

1. Se estiver a ligar-se a um servidor MQ no local, selecione **Connect via gateway de dados no local.**

1. Forneça as informações de ligação para o seu servidor MQ.

   * Para **o Servidor,** pode introduzir o nome do servidor MQ ou introduzir o endereço IP seguido de um cólon e o número de porta.

   * Para utilizar a camada de tomadas seguras (SSL), selecione **Ativar SSL?**

     O conector MQ suporta atualmente apenas a autenticação do servidor, e não a autenticação do cliente. Para mais informações, consulte [problemas de Ligação e autenticação.](#connection-problems)

1. Na secção **gateway,** siga estes passos:

   1. Na lista **de Subscrição,** selecione a subscrição Azure associada ao seu recurso de gateway Azure.

   1. Na lista **'Connection Gateway',** selecione o recurso gateway Azure que pretende utilizar.

1. Quando concluir, selecione **Criar**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemas de ligação e autenticação

Quando a sua aplicação lógica tentar ligar-se ao servidor MQ no local, poderá obter este erro:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se estiver a utilizar o conector MQ diretamente no Azure, o servidor MQ precisa de utilizar um certificado emitido por uma autoridade de [certificados](https://www.ssl.com/faqs/what-is-a-certificate-authority/)de confiança.

* Se estiver a usar o portal de dados no local, tente utilizar um certificado emitido por uma autoridade de [certificados](https://www.ssl.com/faqs/what-is-a-certificate-authority/) de confiança, sempre que possível. No entanto, se esta opção não for possível, poderá utilizar um certificado auto-assinado, que não é emitido por uma autoridade de [certificado](https://www.ssl.com/faqs/what-is-a-certificate-authority/) de confiança e é considerado menos seguro.

  Para instalar o certificado auto-assinado do servidor, pode utilizar a ferramenta **Gestor de Certificação** do Windows (certmgr.msc). Para este cenário, no seu computador local, onde o serviço de gateway de dados no local está em funcionamento, é necessário instalar o certificado na loja de certificados **de Computador Local** ao nível das **Autoridades de Certificação de Raiz Fidedignas.**

  1. No computador onde o serviço de gateway de dados no local está em funcionamento, abra o menu inicial, encontre e selecione **Executar certificados de utilizador**.

  1. Depois de a ferramenta Do Gestor de Certificação do Windows abrir, aceda aos **Certificados - Autoridades locais**  >   **de certificação de raiz fidedignas** por computador e instale o certificado.

     > [!IMPORTANT]
     > Certifique-se de que instala **certificados - Autoridades locais**  >  **de certificação de raiz fidedignas.**

* O servidor MQ requer que defina a especificação de cifra que pretende utilizar para ligações TLS/SSL. No entanto, o SslStream em .NET não permite especificar a encomenda para especificações de cifra. Para contornar esta limitação, pode alterar a configuração do servidor MQ para corresponder à primeira especificação de cifra na suite que o conector envia na negociação TLS/SSL.

  Quando experimenta a ligação, o servidor MQ regista uma mensagem de evento que indica que a ligação falhou porque a outra extremidade usou a especificação de cifra incorreta. A mensagem do evento contém a especificação de cifra que aparece em primeiro lugar na lista. Atualize a especificação de cifra na configuração do canal para corresponder à especificação de cifra na mensagem do evento.

## <a name="browse-single-message"></a>Navegue numa única mensagem

1. Na sua aplicação lógica, sob o gatilho ou outra ação, selecione **Novo passo**.

1. Na caixa de pesquisa, insira `mq` e selecione a ação **da mensagem Browse.**

   ![Selecione ação "Procurar mensagem"](media/connectors-create-api-mq/browse-message.png)

1. Se ainda não criou uma ligação MQ, é-lhe solicitado que [crie essa ligação.](#create-connection)

1. Depois de criar a ligação, configurar as propriedades da ação da **mensagem Browse:**

   | Propriedade | Descrição |
   |----------|-------------|
   | **Filas** | Se diferente da fila especificada na ligação, especifique essa fila. |
   | **MessageId**, **CorrelationId,** **GroupId,** e outras propriedades | Navegue por uma mensagem baseada nas diferentes propriedades de mensagens MQ |
   | **IncluirInfo** | Para incluir informações adicionais de mensagens na saída, selecione **true**. Para omitir informações adicionais de mensagens na saída, selecione **falso**. |
   | **Tempo Limite** | Insira um valor para determinar quanto tempo esperar que uma mensagem chegue numa fila vazia. Se nada for introduzido, a primeira mensagem na fila é recuperada, e não há tempo gasto à espera que uma mensagem apareça. |
   |||

   Por exemplo:

   ![Propriedades para ação "Procurar mensagem"](media/connectors-create-api-mq/browse-message-properties.png)

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**. Para testar a sua aplicação, selecione **Executar**.

   Após o final da execução, o designer mostra os passos de fluxo de trabalho e o seu estado para que possa rever a saída.

1. Para ver os detalhes sobre cada passo, clique na barra de título do degrau. Para rever mais informações sobre a saída de um passo, **selecione Mostrar saídas cruas**.

   ![Procurar saída de mensagem](media/connectors-create-api-mq/browse-message-output.png)

   Aqui está uma amostra de produção bruta:

   ![Navegue na saída crua da mensagem](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se definir **o IncludeInfo** para **verdadeiro,** é mostrada uma saída adicional:

   ![Procurar mensagem inclua informações](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Navegue por várias mensagens

A ação **de mensagens Browse** inclui uma opção **BatchSize** para indicar quantas mensagens devem regressar da fila. Se **o BatchSize** não tiver valor, todas as mensagens são devolvidas. A saída devolvida é uma série de mensagens.

1. Siga os passos anteriores, mas adicione a ação das **mensagens Browse.**

1. Se ainda não criou uma ligação MQ, é-lhe solicitado que [crie essa ligação.](#create-connection) Caso contrário, por padrão, utiliza-se a primeira ligação previamente configurada. Para criar uma nova ligação, selecione **Alterar a ligação**. Ou selecione uma ligação diferente.

1. Forneça a informação para a ação.

1. Guarde e execute a aplicação lógica.

   Depois de a aplicação lógica terminar de funcionar, aqui está alguma saída de amostra da ação de **mensagens Browse:**

   ![Amostra de saída de "procurar mensagens"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Receber uma única mensagem

A ação **da mensagem Receber** tem as mesmas entradas e saídas que a ação **da mensagem Browse.** Quando utilizar **a mensagem Receber,** a mensagem é apagada da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **'Receber mensagens'** tem as mesmas entradas e saídas que a ação das **mensagens Browse.** Quando utilizar **Mensagens Receber,** as mensagens são apagadas da fila.

> [!NOTE]
> Ao executar uma navegação ou uma ação de receção numa fila que não tenha mensagens, a ação falha com esta saída:
>
> ![Erro de MQ "sem mensagem"](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Enviar mensagem

1. Siga os passos anteriores, mas adicione a ação **de mensagem Enviar.**

1. Se ainda não criou uma ligação MQ, é-lhe solicitado que [crie essa ligação.](#create-connection) Caso contrário, por padrão, utiliza-se a primeira ligação previamente configurada. Para criar uma nova ligação, selecione **Alterar a ligação**. Ou selecione uma ligação diferente.

1. Forneça a informação para a ação. Para **o MessageType**, selecione um tipo de mensagem válido: **Datagram,** **Answer**, or **Request**

   ![Propriedades para "Enviar ação de mensagem"](media/connectors-create-api-mq/send-message-properties.png)

1. Guarde e execute a aplicação lógica.

   Depois de a aplicação lógica terminar de funcionar, eis algumas saídas de amostra da ação de **mensagem Enviar:**

   ![Saída de amostra "Enviar mensagem"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre ações e limites, descritos pela descrição do Conector Swagger, consulte a página de [referência](/connectors/mq/)do conector .

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
