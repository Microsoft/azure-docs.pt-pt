---
title: Ligar ao servidor IBM MQ
description: Envie e recupere mensagens com um servidor IBM MQ Azure ou no local e aplicações lógicas Azure
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410275"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Ligue-se a um servidor IBM MQ a partir de Aplicações Lógicas Azure

O conector IBM MQ envia e recupera mensagens armazenadas num servidor IBM MQ nas instalações ou no Azure. Este conector inclui um cliente Microsoft MQ que comunica com um servidor IBM MQ remoto através de uma rede TCP/IP. Este artigo fornece um guia de arranque para utilizar o conector MQ. Pode começar por navegar numa única mensagem numa fila e depois experimentar outras ações.

O conector IBM MQ inclui estas ações, mas não fornece gatilhos:

- Navegue numa única mensagem sem apagar a mensagem do servidor IBM MQ.
- Navegue num lote de mensagens sem apagar as mensagens do servidor IBM MQ.
- Receba uma única mensagem e elimine a mensagem do servidor IBM MQ.
- Receba um lote de mensagens e elimine as mensagens do servidor IBM MQ.
- Envie uma única mensagem para o servidor IBM MQ.

Aqui estão as versões MQ ibm WebSphere mQ oficialmente suportadas:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a utilizar um servidor MQ no local, [instale a porta de dados no local](../logic-apps/logic-apps-gateway-install.md) num servidor dentro da sua rede. O servidor onde o portal de dados no local está instalado também deve ter .NET Framework 4.6 instalado para o conector MQ funcionar.

  Depois de terminar a instalação do portal, também deve criar um recurso em Azure para o portal de dados no local. Para mais informações, consulte [Configurar a ligação de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  Se o seu servidor MQ estiver disponível publicamente ou disponível dentro do Azure, não tem de utilizar o portal de dados.

* A aplicação lógica onde pretende adicionar a ação MQ. Esta aplicação lógica deve utilizar a mesma localização que a sua ligação de gateway de dados no local e já deve ter um gatilho que inicie o seu fluxo de trabalho.

  O conector MQ não tem gatilhos, por isso deve adicionar primeiro um gatilho à sua aplicação lógica. Por exemplo, pode utilizar o gatilho de recorrência. Se você é novo em aplicativos lógicos, experimente este [quickstart para criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Criar ligação MQ

Se ainda não tiver uma ligação MQ quando adicionar uma ação MQ, é-lhe pedido que crie a ligação, por exemplo:

![Fornecer informações de ligação](media/connectors-create-api-mq/connection-properties.png)

1. Se estiver a ligar-se a um servidor MQ no local, selecione **Connect através da gateway de dados no local**.

1. Forneça as informações de ligação para o seu servidor MQ.

   * Para **o Servidor,** pode introduzir o nome do servidor MQ ou introduzir o endereço IP seguido de um cólon e o número de porta.

   * Para utilizar a camada de tomadas seguras (SSL), **selecione Ativar SSL?**

     O conector MQ suporta atualmente apenas a autenticação do servidor, não a autenticação do cliente. Para mais informações, consulte problemas de [ligação e autenticação.](#connection-problems)

1. Na secção **gateway,** siga estes passos:

   1. Na lista **de Subscrição,** selecione a subscrição Azure associada ao seu recurso de gateway Azure.

   1. Na lista de Gateway de **Ligação,** selecione o recurso de gateway Azure que pretende utilizar.

1. Quando terminar, selecione **Criar**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemas de ligação e autenticação

Quando a sua aplicação lógica tenta ligar-se ao seu servidor MQ no local, pode obter este erro:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se estiver a utilizar o conector MQ diretamente no Azure, o servidor MQ precisa de utilizar um certificado emitido por uma autoridade de [certificado](https://www.ssl.com/faqs/what-is-a-certificate-authority/)de confiança .

* Se estiver a usar o portal de dados no local, tente usar um certificado emitido por uma autoridade de [certificados](https://www.ssl.com/faqs/what-is-a-certificate-authority/) de confiança quando possível. No entanto, se esta opção não for possível, poderá utilizar um certificado auto-assinado, que não é emitido por uma autoridade de [certificados](https://www.ssl.com/faqs/what-is-a-certificate-authority/) de confiança e é considerado menos seguro.

  Para instalar o certificado auto-assinado do servidor, pode utilizar a ferramenta Do Gestor de **Certificação do Windows** (certmgr.msc). Para este cenário, no seu computador local onde está a funcionar o serviço de gateway de dados no local, é necessário instalar o certificado na sua loja de certificados **de computador local** ao nível das Autoridades de **Certificação de Raiz fidedigna.**

  1. No computador onde está a funcionar o serviço de gateway de dados no local, abra o menu inicial, encontre e selecione **Gerir os certificados de utilizador**.

  1. Depois da ferramenta do Gestor de Certificação do Windows abrir, vá à pasta **Certificates - Local Computer** >  **Trusted Root Certification Authorities** e instale o certificado.

     > [!IMPORTANT]
     > Certifique-se de que instala certificado nos **Certificados - Loja** > das Autoridades de Certificação de**Raiz fidedignas** de computador local.

* O servidor MQ requer que defina a especificação de cifra que pretende utilizar para ligações SSL. No entanto, o SsLStream em .NET não permite especificar a encomenda das especificações de cifra. Para contornar esta limitação, pode alterar a configuração do servidor MQ para corresponder à primeira especificação de cifra na suite que o conector envia na negociação ssl.

  Quando experimenta a ligação, o servidor MQ regista uma mensagem de evento que indica que a ligação falhou porque a outra extremidade usou a especificação de cifra incorreta. A mensagem do evento contém a especificação de cifra que aparece primeiro na lista. Atualize a especificação de cifra na configuração do canal para corresponder à especificação de cifra na mensagem do evento.

## <a name="browse-single-message"></a>Navegue uma única mensagem

1. Na sua aplicação lógica, sob o gatilho ou outra ação, selecione **Novo passo**.

1. Na caixa de `mq`pesquisa, introduza , e selecione a ação de **mensagem Browse.**

   ![Selecione ação "Navegar na mensagem"](media/connectors-create-api-mq/browse-message.png)

1. Se ainda não criou uma ligação MQ, é-lhe pedido que [crie essa ligação.](#create-connection)

1. Depois de criar a ligação, configurar as propriedades da ação da **mensagem Browse:**

   | Propriedade | Descrição |
   |----------|-------------|
   | **Filas** | Se diferente da fila especificada na ligação, especifique a fila. |
   | **MessageId,** **CorrelationId,** **GroupId,** e outras propriedades | Procure uma mensagem baseada nas diferentes propriedades da mensagem MQ |
   | **IncluirInfo** | Para incluir informações adicionais de mensagem na saída, selecione **true**. Para omitir informações adicionais de mensagem na saída, selecione **falso**. |
   | **Intervalo** | Insira um valor para determinar quanto tempo esperar que uma mensagem chegue numa fila vazia. Se nada for introduzido, a primeira mensagem na fila é recuperada, e não há tempo gasto à espera que apareça uma mensagem. |
   |||

   Por exemplo:

   ![Propriedades para ação "Navegar mensagem"](media/connectors-create-api-mq/browse-message-properties.png)

1. Quando terminar, na barra de ferramentas de design, selecione **Save**. Para testar a sua aplicação, selecione **Executar**.

   Após o acabamento da corrida, o designer mostra os passos de fluxo de trabalho e o seu estado para que possa rever a saída.

1. Para ver os detalhes sobre cada passo, clique na barra de título do passo. Para rever mais informações sobre a saída de um passo, selecione **Mostrar saídas cruas**.

   ![Ver saída de mensagem](media/connectors-create-api-mq/browse-message-output.png)

   Aqui está uma amostra de saída crua:

   ![Navegue na saída bruta da mensagem](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se definir **o IncludeInfo** **como verdadeiro,** é mostrada uma saída adicional:

   ![A mensagem de navegação inclua informações](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Navegue em várias mensagens

A ação de **mensagens Browse** inclui uma opção **BatchSize** para indicar quantas mensagens devem voltar da fila. Se **o BatchSize** não tiver valor, todas as mensagens são devolvidas. A saída devolvida é uma série de mensagens.

1. Siga os passos anteriores, mas adicione a ação de **mensagens Browse.**

1. Se ainda não criou uma ligação MQ, é-lhe pedido que [crie essa ligação.](#create-connection) Caso contrário, por padrão, a primeira ligação previamente configurada é utilizada. Para criar uma nova ligação, selecione **ligação Change**. Ou, selecione uma ligação diferente.

1. Forneça as informações para a ação.

1. Guarde e execute a aplicação lógica.

   Depois que a aplicação lógica termina a execução, aqui está alguma saída de amostra da ação de **mensagens Browse:**

   ![Prove a saída "Navegar em mensagens"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Receber uma única mensagem

A ação **de mensagem Receber** tem as mesmas inputs e saídas que a ação da mensagem **Browse.** Quando utilizar a **mensagem Receber,** a mensagem é apagada da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **de mensagens Receber** tem as mesmas inputs e saídas que a ação de **mensagens Browse.** Quando utilizar **Mensagens de Receção,** as mensagens são apagadas da fila.

> [!NOTE]
> Ao executar uma navegação ou uma ação de receção numa fila que não tem nenhuma mensagem, a ação falha com esta saída:
>
> ![Erro "sem mensagem" mQ](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Enviar mensagem

1. Siga os passos anteriores, mas adicione a ação enviar a **mensagem.**

1. Se ainda não criou uma ligação MQ, é-lhe pedido que [crie essa ligação.](#create-connection) Caso contrário, por padrão, a primeira ligação previamente configurada é utilizada. Para criar uma nova ligação, selecione **ligação Change**. Ou, selecione uma ligação diferente.

1. Forneça as informações para a ação. Para **o MessageType,** selecione um tipo de mensagem válido: **Datagram,** **Resposta**ou **Pedido**

   ![Propriedades para "Enviar ação de mensagem"](media/connectors-create-api-mq/send-message-properties.png)

1. Guarde e execute a aplicação lógica.

   Depois que a aplicação lógica termina em execução, aqui está alguma saída de amostra da ação **enviar mensagem:**

   ![Saída de amostra "Enviar mensagem"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre ações e limites, descritos pela descrição swagger do conector, reveja a página de [referência](/connectors/mq/)do conector .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
