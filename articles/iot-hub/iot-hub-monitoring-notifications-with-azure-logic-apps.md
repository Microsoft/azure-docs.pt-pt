---
title: Monitorização remota ioT e notificações com aplicações da Lógica Azure [ Microsoft Docs
description: Utilize aplicativos azure logic para monitorização da temperatura IoT no seu hub IoT e envie automaticamente notificações de e-mail para a sua caixa de correio para quaisquer anomalias detetadas.
author: robinsh
keywords: monitorização de iot, notificações oot, monitorização da temperatura iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: ad1fcb67704e79f5aef62a59604e47f477804405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68385725"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitorização e notificações remotas IoT com Aplicações Lógicas Azure que ligam o seu hub IoT e caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[As Aplicações Lógicas Azure](https://docs.microsoft.com/azure/logic-apps/) podem ajudá-lo a orquestrar fluxos de trabalho através de serviços no local e na nuvem, uma ou mais empresas, e através de vários protocolos. Uma aplicação lógica começa com um gatilho, que é seguido por uma ou mais ações que podem ser sequenciadas usando controlos incorporados, tais como condições e iterres. Esta flexibilidade faz das Aplicações Lógicas uma solução IoT ideal para cenários de monitorização de IoT. Por exemplo, a chegada de dados de telemetria de um dispositivo num ponto final do IoT Hub pode iniciar fluxos de trabalho de aplicações lógicas para armazenar os dados numa bolha de Armazenamento Azure, enviar alertas de e-mail para alertar para anomalias de dados, agendar uma visita técnica se um dispositivo reportar uma falha E assim por diante.

## <a name="what-you-learn"></a>O que irá aprender

Aprende a criar uma aplicação lógica que ligue o seu hub IoT e a sua caixa de correio para monitorização de temperatura e notificações.

O código do cliente em execução `temperatureAlert`no seu dispositivo define uma propriedade de aplicação, em cada mensagem de telemetria que envia para o seu hub IoT. Quando o código do cliente deteta uma temperatura superior `true`a 30 C, define esta propriedade para; caso contrário, define a `false`propriedade para .

As mensagens que chegam ao seu hub IoT são semelhantes às seguintes, com os dados de telemetria contidos no corpo e a `temperatureAlert` propriedade contida nas propriedades da aplicação (as propriedades do sistema não são mostradas):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Para saber mais sobre o formato de mensagem IoT Hub, consulte [Criar e ler mensagens IoT Hub](iot-hub-devguide-messages-construct.md).

Neste tópico, você configura o encaminhamento no seu hub `temperatureAlert` IoT para enviar mensagens em que a propriedade é `true` para um ponto final de ônibus de serviço. Em seguida, configura uma aplicação lógica que dispara nas mensagens que chegam ao ponto final do Bus de Serviço e envia-lhe uma notificação por e-mail.

## <a name="what-you-do"></a>O que faz

* Crie um espaço de nome de ônibus de serviço e adicione-lhe uma fila de ônibus de serviço.
* Adicione um ponto final personalizado e uma regra de encaminhamento ao seu hub IoT para direcionar mensagens que contenham um alerta de temperatura para a fila do Ônibus de serviço.
* Crie, configure e teste uma aplicação lógica para consumir mensagens da sua fila de ônibus de serviço e enviar e-mails de notificação para um destinatário pretendido.

## <a name="what-you-need"></a>Do que precisa

* Complete o tutorial de [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes cobrem os seguintes requisitos:

  * Uma subscrição ativa do Azure.
  * Um hub Azure IoT sob a sua assinatura.
  * Uma aplicação de cliente em execução no seu dispositivo que envia mensagens de telemetria para o seu hub Azure IoT.

## <a name="create-service-bus-namespace-and-queue"></a>Criar espaço de nome de ônibus de serviço e fila

Crie um espaço de nomes e uma fila do Service Bus. Mais tarde neste tópico, cria-se uma regra de encaminhamento no seu hub IoT para direcionar mensagens que contenham um alerta de temperatura para a fila do Service Bus, onde serão captadas por uma aplicação lógica e desencadeá-la para enviar um e-mail de notificação.

### <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus

1. No [portal Azure, selecione](https://portal.azure.com/) **+ Crie um** > **ônibus de serviço**de**integração** > de recursos.

1. No painel Criar espaço de **nome,** forneça as seguintes informações:

   **Nome**: O nome do espaço de nome do autocarro de serviço. O espaço de nome deve ser único em Azure.

   **Nível de preços**: Selecione **Basic** da lista de descidas. O nível básico é suficiente para este tutorial.

   **Grupo de recursos:** Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize o mesmo local que o seu hub IoT.

   ![Crie um espaço de nome de ônibus de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecione **Criar**. Aguarde que a colocação esteja concluída antes de passar para o próximo passo.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Adicione uma fila de ônibus de serviço ao espaço de nome

1. Abra o espaço de nome do Ônibus de serviço. A maneira mais fácil de chegar ao espaço de nome do Ônibus de serviço é selecionar **grupos de recursos** a partir do painel de recursos, selecionar o seu grupo de recursos e, em seguida, selecionar o espaço de nome do Ônibus de serviço da lista de recursos.

1. No painel **Service Bus Namespace,** selecione **+ Fila**.

1. Introduza um nome para a fila e, em seguida, selecione **Criar**. Quando a fila foi criada com sucesso, o painel de **fila Criar** fecha- se.

   ![Adicione uma fila de ônibus de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. De volta ao painel **Service Bus Namespace,** em **Entidades,** selecione **Filas**. Abra a fila do Ônibus de serviço da lista e, em seguida, selecione **políticas** > de acesso partilhado **+ Adicionar**.

1. Introduza um nome para a apólice, verifique **Gerir,** e depois **selecione Criar**.

   ![Adicione uma política de fila de ônibus de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Adicione uma regra de endpoint personalizado e encaminhamento ao seu hub IoT

Adicione um ponto final personalizado para a fila do Ônibus de serviço ao seu hub IoT e crie uma regra de encaminhamento de mensagens para direcionar mensagens que contenham um alerta de temperatura para esse ponto final, onde serão captadas pela sua aplicação lógica. A regra de encaminhamento utiliza `temperatureAlert = "true"`uma consulta de encaminhamento, para encaminhar mensagens com base no valor da propriedade da `temperatureAlert` aplicação definida pelo código cliente em execução no dispositivo. Para saber mais, consulte a consulta de [encaminhamento de mensagens com base nas propriedades da mensagem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Adicione um ponto final personalizado

1. Abra o seu centro de iôts. A maneira mais fácil de chegar ao hub IoT é selecionar **grupos de Recursos** a partir do painel de recursos, selecionar o seu grupo de recursos e, em seguida, selecionar o seu hub IoT da lista de recursos.

1. Em **Mensagens,** selecione **o encaminhamento de mensagens**. No painel de **encaminhamento de mensagens,** selecione o separador de **pontos finais Personalizado** e, em seguida, selecione + **Adicionar**. A partir da lista de lançamentos, selecione fila de **ônibus de serviço**.

   ![Adicione um ponto final ao seu hub IoT no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. No painel de fim do **autocarro de serviço,** introduza as seguintes informações:

   **Nome final**: O nome do ponto final.

   **Espaço de nome de autocarro de serviço**: Selecione o espaço de nome que criou.

   **Fila de ônibus de serviço**: Selecione a fila que criou.

   ![Adicione um ponto final ao seu hub IoT no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecione **Criar**. Depois de o ponto final ser criado com sucesso, proceda ao próximo passo.

### <a name="add-a-routing-rule"></a>Adicionar uma regra de encaminhamento

1. Volte ao painel de **encaminhamento** da mensagem, selecione o separador **Rotas** e, em seguida, selecione **+ Adicionar**.

1. No painel adicionar um painel de **rota,** introduza as seguintes informações:

   **Nome**: O nome da regra de encaminhamento.

   **Ponto final**: Selecione o ponto final que criou.

   **Fonte de dados**: Selecione Mensagens de **Telemetria do Dispositivo**.

   **Consulta de encaminhamento**: Enter `temperatureAlert = "true"`.

   ![Adicione uma regra de encaminhamento no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selecione **Guardar**. Pode fechar o painel de **encaminhamento de mensagens.**

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar uma Aplicação Lógica

Na secção anterior, configura o seu hub IoT para encaminhar mensagens contendo um alerta de temperatura para a sua fila de ônibus de serviço. Agora, configura uma aplicação lógica para monitorizar a fila do Service Bus e enviar uma notificação de e-mail sempre que uma mensagem é adicionada à fila.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. Selecione Criar uma**aplicação lógica**de**integração** >  **de recursos.** > 

1. Introduza as seguintes informações:

   **Nome**: O nome da aplicação lógica.

   **Grupo de recursos:** Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize o mesmo local que o seu hub IoT.

   ![Criar uma aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecione **Criar**.

### <a name="configure-the-logic-app-trigger"></a>Configure o gatilho da aplicação lógica

1. Abra a aplicação lógica. A maneira mais fácil de chegar à aplicação lógica é selecionar **grupos de Recursos** a partir do painel de recursos, selecionar o seu grupo de recursos e, em seguida, selecionar a sua aplicação lógica a partir da lista de recursos. Ao selecionar a aplicação lógica, o Logic Apps Designer abre.

1. No Designer de Aplicações Lógicas, desloque-se para os **Modelos** e selecione **Blank Logic App**.

   ![Comece com uma aplicação lógica em branco no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecione o separador **All** e, em seguida, selecione **Service Bus**.

   ![Selecione Service Bus para começar a criar a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Em **'Gatilhos',** selecione **Quando uma ou mais mensagens chegarem numa fila (completa automática)**.

   ![Selecione o gatilho para a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Crie uma ligação de ônibus de serviço.
   1. Introduza um nome de ligação e selecione o seu espaço de nome do Ônibus de serviço na lista. O próximo ecrã abre.

      ![Crie uma ligação de ônibus de serviço para a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecione a política de ônibus de serviço (RootManageSharedAccessKey). Em seguida, selecione **Criar**.

      ![Crie uma ligação de ônibus de serviço para a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. No ecrã final, para **o nome da fila,** selecione a fila que criou a partir da queda. Introduza `175` a **contagem máxima de mensagens**.

      ![Especifique a contagem máxima de mensagens para a ligação de ônibus de serviço na sua aplicação lógica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecione **Guardar** no menu no topo do Logic Apps Designer para guardar as suas alterações.

### <a name="configure-the-logic-app-action"></a>Configure a ação lógica da aplicação

1. Crie uma ligação de serviço SMTP.

   1. Selecione **Novo passo**. Em **Escolha uma ação**, selecione o separador **'Todos'.**

   1. Digite `smtp` na caixa de pesquisa, selecione o serviço **SMTP** no resultado da pesquisa e, em seguida, selecione **Enviar e-mail**.

      ![Crie uma ligação SMTP na sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Introduza as informações SMTP para a sua caixa de correio e, em seguida, selecione **Criar**.

      ![Insira informações de conexão SMTP na sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações sMTP para [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Pode ser necessário desativar o SSL para estabelecer a ligação. Se for esse o caso e pretender reativar o SSL depois de a ligação ter sido estabelecida, consulte o passo opcional no final desta secção.

   1. A partir do **novo parâmetro de** queda na etapa enviar **e-mail,** selecione **From,** **To**, **Subject** and **Body**. Clique ou toque em qualquer lugar do ecrã para fechar a caixa de seleção.

      ![Escolha campos de e-mail de ligação SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Insira o seu endereço `High temperature detected` de e-mail para **From** and **To,** e para **Assunto** e **Corpo**. Se o **Adicionar conteúdo dinâmico a partir das aplicações e conectores utilizados neste** diálogo de fluxo se abrir, selecione **'Ocultar'** para o fechar. Não utiliza conteúdo dinâmico neste tutorial.

      ![Preencher campos de e-mail de ligação SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecione **Guardar** para salvar a ligação SMTP.

1. (Opcional) Se teve de desativar o SSL para estabelecer uma ligação com o seu fornecedor de e-mail e pretender reativa-lo, siga estes passos:

   1. No painel de **aplicações Logic,** em Ferramentas de **Desenvolvimento,** selecione **ligações API**.

   1. A partir da lista de ligações API, selecione a ligação SMTP.

   1. No painel **de ligação SMTP API,** em **geral,** selecione **ligação API de edição**.

   1. No painel de **ligação Editar API,** selecione **Enable SSL?** **Save**

      ![Editar conexão SMTP API na sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A sua aplicação lógica está agora pronta para processar alertas de temperatura da fila do Autocarro de Serviço e enviar notificações para a sua conta de e-mail.

## <a name="test-the-logic-app"></a>Testar a aplicação lógica

1. Inicie a aplicação do cliente no seu dispositivo.

1. Se estiver a utilizar um dispositivo físico, traga cuidadosamente uma fonte de calor perto do sensor de calor até que a temperatura exceda os 30 graus C. Se estiver a utilizar o simulador online, o código do cliente irá transmitir aleatoriamente mensagens de telemetria que excedam 30 C.

1. Deve começar a receber notificações de e-mail enviadas pela aplicação lógica.

   > [!NOTE]
   > O seu fornecedor de serviços de e-mail poderá ter de verificar a identidade do remetente para se certificar de que é você que envia o e-mail.

## <a name="next-steps"></a>Passos seguintes

Criou com sucesso uma aplicação lógica que liga o seu hub IoT e a sua caixa de correio para monitorização de temperatura e notificações.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
