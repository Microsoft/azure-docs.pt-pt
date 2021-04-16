---
title: Monitorização remota IoT e notificações com aplicações Azure Logic | Microsoft Docs
description: Utilize aplicativos Azure Logic para monitorização da temperatura IoT no seu hub IoT e envie automaticamente notificações por e-mail para a sua caixa de correio para quaisquer anomalias detetadas.
author: robinsh
keywords: iot monitorização, iot notificações, iot monitorização da temperatura
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564955"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitorização remota IoT e notificações com Azure Logic Apps que ligam o seu hub IoT e caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[As Azure Logic Apps](../logic-apps/index.yml) podem ajudá-lo a orquestrar fluxos de trabalho em todos os locais e serviços na nuvem, uma ou mais empresas, e através de vários protocolos. Uma aplicação lógica começa com um gatilho, que é seguido por uma ou mais ações que podem ser sequenciadas usando controlos incorporados, tais como condições e iteradores. Esta flexibilidade faz das Aplicações Lógicas uma solução IoT ideal para cenários de monitorização de IoT. Por exemplo, a chegada de dados de telemetria de um dispositivo num ponto final do IoT Hub pode iniciar fluxos de trabalho de aplicações lógicas para armazenar os dados numa bolha de armazenamento Azure, enviar alertas de e-mail para alertar para anomalias de dados, agendar uma visita de um técnico se um dispositivo reportar uma falha, e assim por diante.

Neste artigo, aprende a criar uma aplicação lógica que liga o seu hub IoT e a sua caixa de correio para monitorização de temperatura e notificações. O código de cliente em execução no seu dispositivo define uma propriedade de aplicação, `temperatureAlert` em cada mensagem de telemetria que envia para o seu hub IoT. Quando o código cliente deteta uma temperatura superior a 30 C, define esta propriedade `true` para; caso contrário, define a propriedade para `false` .

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

Neste tópico, você configura o encaminhamento no seu hub IoT para enviar mensagens em que a `temperatureAlert` propriedade é para um ponto final de Bus `true` service. Em seguida, configura uma aplicação lógica que desencadeia as mensagens que chegam ao ponto final do Service Bus e envia-lhe uma notificação por e-mail.

## <a name="prerequisites"></a>Pré-requisitos

* Complete o tutorial [de simulador on-line Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo. Por exemplo, pode ir a [Raspberry Pi com node.js](iot-hub-raspberry-pi-kit-node-get-started.md) ou a um dos quickstarts de [telemetria Enviar.](quickstart-send-telemetry-dotnet.md) Estes artigos abrangem os seguintes requisitos:

  * Uma subscrição ativa do Azure.
  * Um hub Azure IoT sob a sua assinatura.
  * Uma aplicação do cliente em execução no seu dispositivo que envia mensagens de telemetria para o seu hub Azure IoT.

## <a name="create-service-bus-namespace-and-queue"></a>Criar espaço de nome de ônibus de serviço e fila

Crie um espaço de nomes e uma fila do Service Bus. Mais tarde neste tópico, cria uma regra de encaminhamento no seu hub IoT para direcionar mensagens que contenham um alerta de temperatura para a fila do Service Bus, onde serão recolhidas por uma aplicação lógica e desencadeá-la para enviar um e-mail de notificação.

### <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus

1. No [portal Azure](https://portal.azure.com/), selecione **+ Criar um**  >    >  **ônibus de serviço de** integração de recursos.

1. No painel **de espaços de nome Create,** forneça as seguintes informações:

   **Nome**: O nome do espaço de nome do autocarro de serviço. O espaço de nome deve ser único em todo o Azure.

   **Nível de preços**: Selecione **Basic** da lista de drop-down. O nível básico é suficiente para este tutorial.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize o mesmo local que o seu hub IoT utiliza.

   ![Crie um espaço de nome de ônibus de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecione **Criar**. Aguarde que a colocação esteja concluída antes de passar para o passo seguinte.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Adicione uma fila de ônibus de serviço ao espaço de nomes

1. Abra o espaço de nomes do Service Bus. A maneira mais fácil de chegar ao espaço de nomes do Service Bus é selecionar **grupos** de recursos a partir do painel de recursos, selecionar o seu grupo de recursos e, em seguida, selecionar o espaço de nomes do Service Bus na lista de recursos.

1. No **painel service bus namespace,** selecione **+ fila.**

1. Introduza um nome para a fila e, em seguida, **selecione Criar**. Quando a fila tiver sido criada com sucesso, o painel de **fila Create** fecha-se.

   ![Adicione uma fila de ônibus de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. De volta ao painel **de nomes** do autocarro de serviço, em **Entidades,** selecione **Filas**. Abra a fila do Service Bus da lista e, em seguida, **selecione Políticas de acesso partilhado**+  >  **Adicionar**.

1. Introduza um nome para a apólice, verifique **Gerir** e, em seguida, selecione **Criar**.

   ![Adicione uma política de fila de autocarros de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Adicione uma regra de ponto final personalizado e encaminhamento ao seu hub IoT

Adicione um ponto final personalizado para a fila do Service Bus ao seu hub IoT e crie uma regra de encaminhamento de mensagens para mensagens diretas que contenham um alerta de temperatura para esse ponto final, onde serão captadas pela sua aplicação lógica. A regra de encaminhamento utiliza uma consulta de encaminhamento, `temperatureAlert = "true"` para encaminhar mensagens com base no valor da propriedade da `temperatureAlert` aplicação definida pelo código do cliente em execução no dispositivo. Para saber mais, consulte [a consulta de encaminhamento de mensagens com base nas propriedades da mensagem](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Adicione um ponto final personalizado

1. Abra o seu hub IoT. A maneira mais fácil de chegar ao hub IoT é selecionar **grupos** de recursos a partir do painel de recursos, selecionar o seu grupo de recursos e, em seguida, selecionar o seu hub IoT da lista de recursos.

1. Em **Mensagens**, **selecione o encaminhamento de mensagens**. No painel de encaminhamento de **mensagens,** selecione o **separador pontos finais personalizados** e, em seguida, selecione **+ Adicionar**. Da lista de drop-down, selecione **Fila de autocarros de serviço**.

   ![Screenshot que destaca a opção de fila de autocarros Service.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. No painel de ponto final do ponto de saída do **serviço Add a service,** insira as seguintes informações:

   **Nome do ponto final**: O nome do ponto final.

   **Espaço de nomes de ônibus de serviço**: Selecione o espaço de nome que criou.

   **Fila de autocarros de serviço**: Selecione a fila que criou.

   ![Adicione um ponto final ao seu hub IoT no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecione **Criar**. Depois de o ponto final ser criado com sucesso, proceda ao passo seguinte.

### <a name="add-a-routing-rule"></a>Adicionar uma regra de encaminhamento

1. Volte ao painel **de encaminhamento de mensagens,** selecione o **separador Rotas** e, em seguida, selecione **+ Adicionar**.

1. No painel **de rotas Adicionar,** insira as seguintes informações:

   **Nome**: O nome da regra de encaminhamento.

   **Ponto final**: Selecione o ponto final que criou.

   **Fonte de dados**: Selecione **Mensagens de Telemetria do Dispositivo**.

   **Consulta de encaminhamento**: Insira `temperatureAlert = "true"` .

   ![Adicione uma regra de encaminhamento no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selecione **Guardar**. Pode fechar o painel **de encaminhamento de mensagens.**

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar uma App Lógica

Na secção anterior, configura o seu hub IoT para encaminhar mensagens que contenham um alerta de temperatura para a sua fila de ônibus de serviço. Agora, criou uma aplicação lógica para monitorizar a fila do Service Bus e enviar uma notificação por e-mail sempre que uma mensagem é adicionada à fila.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. Selecione **Criar uma** App lógica de  >  **integração**  >  **de** recursos.

1. Introduza as seguintes informações:

   **Nome**: O nome da aplicação lógica.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize o mesmo local que o seu hub IoT utiliza.

   ![Criar uma aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecione **Criar**.

### <a name="configure-the-logic-app-trigger"></a>Configure o gatilho da aplicação lógica

1. Abra a aplicação lógica. A maneira mais fácil de chegar à aplicação lógica é selecionar **grupos** de Recursos a partir do painel de recursos, selecionar o seu grupo de recursos e, em seguida, selecionar a sua aplicação lógica a partir da lista de recursos. Quando seleciona a aplicação lógica, abre o Logic Apps Designer.

1. No Design de Aplicações Lógicas, desloque-se até aos **modelos** e selecione **a App lógica em branco.**

   ![Comece com uma aplicação lógica em branco no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecione o separador **All** e, em seguida, selecione **Service Bus**.

   ![Selecione Service Bus para começar a criar a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Em **Triggers**, selecione **Quando uma ou mais mensagens chegarem numa fila (completada automaticamente)**.

   ![Selecione o gatilho para a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Crie uma ligação de autocarro de serviço.
   1. Introduza um nome de ligação e selecione o seu espaço de nome de Service Bus na lista. O próximo ecrã abre.

      ![Screenshot que realça o Quando uma ou mais mensagens chegam numa opção de fila (completa automaticamente).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecione a política do autocarro de serviço (RootManageSharedAccessKey). Em seguida,  **selecione Criar**.

      ![Crie uma ligação de ônibus de serviço para a sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. No ecrã final, para o **nome da fila,** selecione a fila que criou a partir do drop-down. Introduza `175` para a contagem máxima de **mensagem**.

      ![Especifique a contagem máxima de mensagem para a ligação de autocarro de serviço na sua aplicação lógica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. **Selecione Guardar** no menu no topo do Logic Apps Designer para guardar as suas alterações.

### <a name="configure-the-logic-app-action"></a>Configure a ação lógica da app

1. Criar uma ligação de serviço SMTP.

   1. Selecione **Novo passo**. Em **Escolha uma ação**, selecione o separador **All.**

   1. Digite `smtp` a caixa de pesquisa, selecione o serviço **SMTP** no resultado da pesquisa e, em seguida, selecione **Enviar e-mail**.

      ![Crie uma ligação SMTP na sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Introduza as informações SMTP para a sua caixa de correio e, em seguida, selecione **Criar**.

      ![Introduza informações de conexão SMTP na sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações do SMTP para [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Pode ser necessário desativar o TLS/SSL para estabelecer a ligação. Se for esse o caso e pretender voltar a ativar o TLS após a ligação ter sido estabelecida, consulte o passo opcional no final desta secção.

   1. A partir do **novo parâmetro** drop-down no passo enviar **por e-mail,** selecione **De**, **Para**, **Assunto** e **Corpo**. Clique ou toque em qualquer lugar do ecrã para fechar a caixa de seleção.

      ![Escolha campos de e-mail de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Insira o seu endereço de e-mail para **De** e **Para**, e `High temperature detected` para **Sujeito** e **Corpo**. Se o **conteúdo dinâmico do Add das aplicações e conectores utilizados neste** diálogo de fluxo abrir, selecione **Hide** para fechá-lo. Não utiliza conteúdo dinâmico neste tutorial.

      ![Preenchimento de campos de e-mail de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. **Selecione Guardar** para guardar a ligação SMTP.

1. (Opcional) Se tiver de desativar o TLS para estabelecer uma ligação com o seu fornecedor de e-mail e pretender reativá-lo, siga estes passos:

   1. No painel **de aplicações Logic,** em **Ferramentas de Desenvolvimento,** selecione **ligações API**.

   1. A partir da lista de ligações API, selecione a ligação SMTP.

   1. No painel **de ligação da API SMTP,** em **Geral,** selecione **a ligação API de Edição**.

   1. No painel de ligação da **API de edição,** selecione **Enable SSL?** 

      ![Editar a ligação API SMTP na sua aplicação lógica no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A sua aplicação lógica está agora pronta para processar alertas de temperatura da fila do Service Bus e enviar notificações para a sua conta de e-mail.

## <a name="test-the-logic-app"></a>Testar a aplicação lógica

1. Inicie a aplicação do cliente no seu dispositivo.

1. Se estiver a utilizar um dispositivo físico, leve cuidadosamente uma fonte de calor para perto do sensor de calor até que a temperatura exceda os 30 graus C. Se estiver a utilizar o simulador online, o código do cliente irá descodificá-lo aleatoriamente com mensagens de telemetria que excedam 30 C.

1. Deverá começar a receber notificações por e-mail enviadas pela aplicação lógica.

   > [!NOTE]
   > O seu fornecedor de serviços de e-mail poderá ter de verificar a identidade do remetente para se certificar de que é você quem envia o e-mail.

## <a name="next-steps"></a>Passos seguintes

Criou com sucesso uma aplicação lógica que liga o seu hub IoT e a sua caixa de correio para monitorização de temperatura e notificações.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]