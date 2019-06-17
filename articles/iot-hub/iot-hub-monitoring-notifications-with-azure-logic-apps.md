---
title: Monitorização remota do IoT e notificações com o Azure Logic Apps | Documentos da Microsoft
description: Utilize o Azure Logic Apps para monitorização de temperatura de IoT no seu hub IoT e enviar automaticamente notificações por e-mail para sua caixa de correio para quaisquer anomalias detetadas.
author: robinsh
keywords: IOT de monitoramento, notificações de iot, monitorização de temperatura de iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719328"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>A monitorização remota do IoT e notificações com o Azure Logic Apps, ligar o seu IoT hub e caixa de correio

![Diagrama de ponto a ponto](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[O Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) pode ajudar a organizar os fluxos de trabalho no local e serviços em nuvem, um ou mais das empresas e entre vários protocolos. Uma aplicação lógica começa com um acionador, que é seguido por uma ou mais ações que podem ser seqüenciadas usando controles internos, como condições e iteradores. Esta flexibilidade torna Logic Apps, uma solução de IoT ideal para cenários de monitorização de IoT. Por exemplo, a chegada de dados de telemetria a partir de um dispositivo num ponto de extremidade do IoT Hub pode iniciar os fluxos de trabalho de aplicação de lógica do armazém de dados num blob de armazenamento do Azure, enviar alertas por e-mail para o avisar de anomalias de dados, agendar a visita de um técnico, se um dispositivo comunicar uma falha , e assim por diante.

## <a name="what-you-learn"></a>O que irá aprender

Saiba como criar uma aplicação lógica que liga o seu IoT hub e caixa de correio para o monitoramento de temperatura e notificações.

O código de cliente em execução no seu dispositivo define uma propriedade de aplicativo, `temperatureAlert`, na mensagem de telemetria de cada, envia ao seu hub IoT. Quando o código de cliente Deteta uma temperatura acima 30 C, define esta propriedade para `true`; caso contrário, ele define a propriedade como `false`.

Neste tópico, iremos configurar encaminhamento no IoT hub para enviar mensagens no qual `temperatureAlert = true` para um barramento de serviço de ponto final e configurar uma aplicação lógica que aciona as mensagens que chegam ao ponto final do Service Bus e envia-lhe uma notificação por e-mail.

## <a name="what-you-do"></a>O que fazer

* Criar um espaço de nomes do Service Bus e adicionar-lhe uma fila do Service Bus.
* Adicione um ponto final personalizado e uma regra de roteamento ao seu hub IoT para rotear mensagens que contêm um alerta de temperatura à fila do Service Bus.
* Criar, configurar e testar uma aplicação lógica para consumir mensagens da sua fila do Service Bus e enviar e-mails de notificação para um destinatário pretendido.

## <a name="what-you-need"></a>Do que precisa

* Concluir o [simulador online de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes incluem os seguintes requisitos:

  * Uma subscrição ativa do Azure.
  * Um hub IoT do Azure com a sua subscrição.
  * Uma aplicação de cliente em execução no seu dispositivo que envia mensagens de telemetria ao seu hub IoT do Azure.

## <a name="create-service-bus-namespace-and-queue"></a>Criar o espaço de nomes do Service Bus e fila

Crie um espaço de nomes e uma fila do Service Bus. Mais tarde neste tópico, vai criar uma regra de encaminhamento no hub IoT para mensagens diretas que contêm um alerta de temperatura à fila do Service Bus, onde serão detetados por uma aplicação lógica e dispará-lo para enviar um e-mail de notificação.

### <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus

1. Sobre o [portal do Azure](https://portal.azure.com/), selecione **+ criar um recurso** > **integração** > **Service Bus**.

1. Sobre o **criar espaço de nomes** painel, forneça as seguintes informações:

   **Nome**: O nome do espaço de nomes do service bus. O espaço de nomes tem de ser exclusivo em todo o Azure.

   **Escalão de preço**: Selecione **básica** na lista pendente. O escalão básico é suficiente para este tutorial.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que utiliza o seu hub IoT.

   **Localização**: Utilize a mesma localização que utiliza o seu hub IoT.

   ![Criar um espaço de nomes do service bus no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecione **Criar**. Aguarde pela conclusão antes de passar para a próxima etapa da implementação.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Adicionar uma fila do Service Bus para o espaço de nomes

1. Abra o espaço de nomes do Service Bus. A maneira mais fácil de obter ao espaço de nomes do Service Bus é selecionar **grupos de recursos** partir do painel de recursos, selecione o grupo de recursos, em seguida, selecione o espaço de nomes do Service Bus na lista de recursos.

1. Sobre o **espaço de nomes do Service Bus** painel, selecione **+ fila**.

1. Introduza um nome para a fila e, em seguida, selecione **criar**. Quando a fila foi criada com êxito, o **criar fila** fecha o painel.

   ![Adicionar uma fila do service bus no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Volta a **espaço de nomes do Service Bus** painel, em **entidades**, selecione **filas**. Abrir a fila do Service Bus na lista e, em seguida, selecione **políticas de acesso partilhado** >  **+ adicionar**.

1. Introduza um nome para a política, verificação **Manage**e, em seguida, selecione **criar**.

   ![Adicionar uma política de fila do service bus no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Adicionar um ponto final personalizado e uma regra de encaminhamento ao seu hub IoT

Adicione um ponto final personalizado para a fila do Service Bus ao seu hub IoT e crie uma regra de roteamento de mensagem para direcionar as mensagens que contêm um alerta de temperatura para esse ponto final, de onde eles serão detetados pela sua aplicação lógica. A regra de encaminhamento utiliza uma consulta de encaminhamento, `temperatureAlert = "true"`, para encaminhar mensagens com base no valor da `temperatureAlert` propriedade de aplicativo definida pelo código do cliente em execução no dispositivo. Para obter mais informações, consulte [mensagem de consulta de encaminhamento com base nas propriedades da mensagem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Adicionar um ponto final personalizado

1. Abra o seu hub IoT. A maneira mais fácil de obter ao IoT hub é selecionar **grupos de recursos** partir do painel de recursos, selecione o grupo de recursos, em seguida, selecione o seu hub IoT na lista de recursos.

1. Sob **Messaging**, selecione **roteamento de mensagens**. Sobre o **roteamento de mensagens** painel, selecione a **pontos finais personalizados** separador e, em seguida, selecione **+ adicionar**. Na lista pendente, selecione **fila do Service bus**.

   ![Adicionar um ponto final ao seu hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Sobre o **adicionar um ponto de final do service bus** painel, introduza as seguintes informações:

   **Nome do ponto final**: O nome do ponto de extremidade.

   **O espaço de nomes do Service bus**: Selecione o espaço de nomes que criou.

   **Fila do Service bus**: Selecione a fila que criou.

   ![Adicionar um ponto final ao seu hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecione **Criar**. Quando o ponto final é criado com êxito, avance para o passo seguinte.

### <a name="add-a-routing-rule"></a>Adicionar uma regra de encaminhamento

1. Volta a **roteamento de mensagens** painel, selecione a **rotas** separador e, em seguida, selecione **+ adicionar**.

1. Sobre o **adicionar uma rota** painel, introduza as seguintes informações:

   **Nome**: O nome da regra de encaminhamento.

   **Ponto final**: Selecione o ponto final que criou.

   **Origem de dados**: Selecione **mensagens de telemetria do dispositivo**.

   **Consulta de encaminhamento**: Introduza `temperatureAlert = "true"`.

   ![Adicionar uma regra de roteamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selecione **Guardar**. Pode fechar o **roteamento de mensagens** painel.

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar uma aplicação lógica

Na secção anterior, configure seu hub IoT para mensagens de rota que contém um alerta de temperatura para a fila do Service Bus. Agora, vai configurar uma aplicação lógica para monitorizar a fila do Service Bus e enviar uma notificação por e-mail sempre que uma mensagem é adicionada à fila.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. Selecione **criar um recurso** > **integração** > **aplicação lógica**.

1. Introduza as seguintes informações:

   **Nome**: O nome da aplicação lógica.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que utiliza o seu hub IoT.

   **Localização**: Utilize a mesma localização que utiliza o seu hub IoT.

   ![Criar uma aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecione **Criar**.

### <a name="configure-the-logic-app-trigger"></a>Configurar o acionador da aplicação lógica

1. Abra a aplicação lógica. A maneira mais fácil para ir para a aplicação lógica consiste em selecionar **grupos de recursos** partir do painel de recursos, selecione o grupo de recursos, em seguida, selecione a sua aplicação lógica na lista de recursos. Quando seleciona a aplicação lógica, abre o estruturador de aplicações lógicas.

1. No estruturador de aplicações lógicas, desloque para baixo até **modelos** e selecione **aplicação lógica em branco**.

   ![Começar com uma aplicação lógica em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecione o **todos os** separador e, em seguida, selecione **do Service Bus**.

   ![Selecionar barramento de serviço para começar a criar a sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Sob **Acionadores**, selecione **quando uma ou mais mensagens chegam a uma fila (concluir automaticamente)** .

   ![Selecione o acionador da aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Crie uma ligação de barramento de serviço.
   1. Introduza um nome de ligação e selecione o seu espaço de nomes do Service Bus na lista. É aberto o ecrã seguinte.

      ![Criar uma ligação de barramento de serviço para a aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecione a política de barramento de serviço (RootManageSharedAccessKey). Em seguida, selecione **criar**.

      ![Criar uma ligação de barramento de serviço para a aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. No ecrã final, para **nome da fila**, selecione a fila que criou na lista pendente. Introduza `175` para **número máximo de mensagem**.

      ![Especifique o número máximo de mensagens para a ligação de barramento de serviço na sua aplicação lógica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecione **guardar** no menu na parte superior do Designer de aplicações lógicas para guardar as alterações.

### <a name="configure-the-logic-app-action"></a>Configure a ação de aplicação lógica

1. Crie uma ligação de serviço de SMTP.

   1. Selecione **Novo passo**. Na **escolher uma ação**, selecione a **todos os** separador.

   1. Tipo `smtp` na caixa de pesquisa, selecione o **SMTP** serviço no resultado da pesquisa e, em seguida, selecione **enviar correio eletrónico**.

      ![Criar uma ligação de SMTP na sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Introduza as informações de SMTP para sua caixa de correio e, em seguida, selecione **criar**.

      ![Introduza as informações de ligação de SMTP na sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), e [Yahoo correio](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Terá de desativar o SSL para estabelecer a ligação. Se for este o caso e pretender voltar a ativar SSL, após o estabelecimento de ligação, veja o passo opcional no final desta secção.

   1. Do **adicionar novo parâmetro** menu pendente na **enviar correio eletrónico** passo, selecione **de**, **para**, **assunto**e **corpo**. Clique ou toque em qualquer lugar no ecrã para fechar a caixa de seleção.

      ![Escolha os campos de e-mail de ligação SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Introduza o seu endereço de e-mail para **partir** e **para**, e `High temperature detected` para **assunto** e **corpo**. Se o **adicionar conteúdo dinâmico a partir das aplicações e conectores utilizados neste fluxo** é aberta a caixa de diálogo, selecione **ocultar** para fechá-lo. Não utilizar conteúdo dinâmico neste tutorial.

      ![Campos de e-mail de ligação do SMTP Fill-in](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecione **guardar** para guardar a ligação de SMTP.

1. (Opcional) Se tiver de desativar o SSL para estabelecer uma ligação com o seu fornecedor de e-mail e pretende voltar a ativá-la, siga estes passos:

   1. Sobre o **aplicação lógica** painel, em **ferramentas de desenvolvimento**, selecione **ligações de API**.

   1. Na lista de ligações de API, selecione a ligação de SMTP.

   1. Sobre o **smtp ligação API** painel, em **gerais**, selecione **ligação editar API**.

   1. Sobre o **editar ligação de API** painel, selecione **ativar SSL?** , volte a introduzir a palavra-passe para a sua conta de e-mail e selecione **guardar**.

      ![Editar ligação de API de SMTP na sua aplicação lógica no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A aplicação lógica está agora pronta para processar os alertas de temperatura da fila do Service Bus e enviar notificações para a sua conta de e-mail.

## <a name="test-the-logic-app"></a>Testar a aplicação lógica

1. Inicie a aplicação de cliente no seu dispositivo.

1. Se estiver a utilizar um dispositivo físico, traga cuidadosamente uma fonte de calor perto o sensor de calor até que a temperatura for superior a 30 de graus de C. Se estiver a utilizar o simulador online, o código de cliente aleatoriamente transmitirá as mensagens de telemetria que excedem o c de 30.

1. Deve começar a receber notificações por e-mail enviadas pela aplicação lógica.

   > [!NOTE]
   > O fornecedor de serviços de e-mail poderá ter de verificar a identidade do remetente certificar-se de que é o utilizador que envia o e-mail.

## <a name="next-steps"></a>Passos Seguintes

Criou uma aplicação lógica que liga o seu IoT hub e caixa de correio para o monitoramento de temperatura e notificações com êxito.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
