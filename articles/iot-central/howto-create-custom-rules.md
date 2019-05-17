---
title: Expandir o Azure IoT Central com notificações e de regras personalizadas | Documentos da Microsoft
description: Como um desenvolvedor de soluções, configure uma aplicação de centro de IoT para enviar notificações por e-mail quando um dispositivo para enviar telemetria. Esta solução utiliza o Azure Stream Analytics e funções do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6140a8aea3fe0fe0a8f1c01cd1c97404c41f7a69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805985"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Expandir o Azure IoT Central com regras personalizadas que enviam notificações

Este guia de procedimentos mostra-lhe, como um desenvolvedor de soluções, como expandir a sua aplicação do Centro de IoT com notificações e de regras personalizadas. O exemplo mostra a enviar uma notificação para um operador quando um dispositivo para enviar telemetria. A solução utiliza uma [do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) consulta para detetar quando um dispositivo tiver parado a enviar telemetria. Utilizações da tarefa do Stream Analytics [as funções do Azure](https://docs.microsoft.com/azure/azure-functions/) enviar a notificação envia um e-mail a utilizar [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Este guia de procedimentos mostra como estender o Centro de IoT para além de que ele pode já fazer com as regras internas e ações.

Este guia de procedimentos, ficará a saber como:

* Stream telemetria a partir de uma aplicação do Centro de IoT com *exportação de dados contínua*.
* Crie uma consulta do Stream Analytics que Deteta quando um dispositivo parou de envio de dados.
* Envie uma notificação por e-mail com as funções do Azure e os serviços do SendGrid.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicação IoT Central

Criar uma aplicação do Centro de IoT desde o [do Azure IoT Central - meus aplicativos](https://aka.ms/iotcentral) página com as seguintes definições:

| Definição | Value |
| ------- | ----- |
| Plano de pagamento | Pay As You Go |
| Modelo de aplicação | Amostra Contoso |
| Nome da aplicação | Aceite a predefinição ou escolha seu próprio nome de |
| URL | Aceite a predefinição ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Inquilino do Azure Active Directory |
| Subscrição do Azure | A sua subscrição do Azure |
| Região | EUA Leste |

Os exemplos e capturas de ecrã neste artigo utilizam o **E.U.A. Leste** região. Escolha uma localização perto de si e certifique-se de que criar todos os seus recursos na mesma região.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal do Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que criou. Crie os seus recursos do Azure na mesma localização que a aplicação do Centro de IoT.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal do Azure para criar um espaço de nomes de Hubs de eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o seu espaço de nomes |
| Escalão de preço | Básico |
| Subscrição | A sua subscrição |
| Grupo de recursos | DetectStoppedDevices |
| Location | EUA Leste |
| Unidades de Débito | 1 |

### <a name="stream-analytics-job"></a>Tarefa do Stream Analytics

Utilize o [portal do Azure para criar uma tarefa do Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o nome da tarefa |
| Subscrição | A sua subscrição |
| Grupo de recursos | DetectStoppedDevices |
| Location | EUA Leste |
| Ambiente de Alojamento | Cloud |
| Unidades de transmissão em fluxo | 3 |

### <a name="function-app"></a>Function App

Utilize o [portal do Azure para criar uma aplicação de funções](https://portal.azure.com/#create/Microsoft.FunctionApp) com as seguintes definições:

| Definição | Value |
| ------- | ----- |
| Nome da aplicação    | Escolha o nome da sua aplicação de função |
| Subscrição | A sua subscrição |
| Grupo de recursos | DetectStoppedDevices |
| SO | Windows |
| Plano de Alojamento | Plano de Consumo |
| Location | EUA Leste |
| Pilha de Tempo de Execução | .NET |
| Armazenamento | Criar novo |

### <a name="sendgrid-account"></a>Conta de SendGrid

Utilize o [portal do Azure para criar uma conta do SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o nome da sua conta de SendGrid |
| Palavra-passe | Criar uma palavra-passe |
| Subscrição | A sua subscrição |
| Grupo de recursos | DetectStoppedDevices |
| Escalão de preço | F1 Gratuito |
| Informação de contacto | Preencher as informações necessárias |

Depois de criar todos os recursos necessários, sua **DetectStoppedDevices** grupo de recursos é semelhante a captura de ecrã seguinte:

![Detetar grupo de recursos de dispositivos parado](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode configurar uma aplicação IoT Central continuamente exportar telemetria para um hub de eventos. Nesta secção, vai criar um hub de eventos a receber dados telemétricos a partir da sua aplicação IoT Central. O hub de eventos fornece a telemetria para a sua tarefa do Stream Analytics para processamento.

1. No portal do Azure, navegue para o espaço de nomes de Hubs de eventos e selecione **+ Hub de eventos**.
1. Nome do seu hub de eventos **centralexport**e selecione **criar**.

O espaço de nomes de Hubs de eventos é semelhante a captura de ecrã seguinte:

![Espaço de nomes dos Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Obter chave de API de SendGrid

A aplicação de função tem uma chave de API do SendGrid para enviar mensagens de e-mail. Para criar uma chave de API do SendGrid:

1. No portal do Azure, navegue para a sua conta do SendGrid. Em seguida, escolha **gerir** para aceder à sua conta do SendGrid.
1. Na sua conta do SendGrid, escolha **configurações**, em seguida, **chaves de API**. Escolher **criar chave de API**:

    ![Criar chave de API de SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Sobre o **criar chave de API** página, crie uma chave denominada **AzureFunctionAccess** com **acesso total** permissões.
1. Tome nota da chave de API, irá precisar dele quando configurar a sua aplicação de função.

## <a name="define-the-function"></a>Definir a função

Esta solução utiliza uma aplicação de funções do Azure para enviar uma notificação por e-mail quando a tarefa do Stream Analytics Deteta um dispositivo de parado. Para criar a sua aplicação de funções:

1. No portal do Azure, navegue para o **serviço de aplicações** instância no **DetectStoppedDevices** grupo de recursos.
1. Selecione **+** para criar uma nova função.
1. Na **escolher um ambiente de desenvolvimento** página, selecione **-portal** e, em seguida, selecione **continuar**.
1. Sobre o **criar uma função** página, selecione **Webhook + API** e, em seguida, selecione **criar**.

O portal cria uma função predefinida denominada **HttpTrigger1**:

![Função de Acionador HTTP padrão](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurar os enlaces de função

Para enviar e-mails com o SendGrid, tem de configurar as ligações para a função da seguinte forma:

1. Selecione **integrar**, escolha a saída **HTTP ($return)** e, em seguida, selecione **eliminar**.
1. Escolher **+ nova saída**, em seguida, escolha **SendGrid**e, em seguida, escolha **selecionar**. Escolher **instalar** para instalar a extensão do SendGrid.
1. Quando a instalação estiver concluída, selecione **utilizar o valor de retorno da função**. Adicionar um válido **endereço** para receber notificações por e-mail.  Adicionar um válido **de endereço** para utilizar como o remetente de e-mail.
1. Selecione **novos** junto a **definição de aplicação da chave de API de SendGrid**. Introduza **SendGridAPIKey** como a chave e a chave de API do SendGrid que apontou anteriormente como o valor. Em seguida, selecione **Criar**.
1. Escolher **guardar** para guardar os enlaces do SendGrid para sua função.

As definições de integrar parecem com a captura de ecrã seguinte:

![Integrações de aplicação de função](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Adicione o código de função

Para implementar a sua função, adicione o C# código para analisar a solicitação de HTTP de entrada e enviar os e-mails da seguinte forma:

1. Escolha o **HttpTrigger1** funcionar na sua aplicação de função e substitua o C# código com o código a seguir:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Poderá ver uma mensagem de erro até que guarde o novo código.

1. Selecione **guardar** para guardar a função.

### <a name="test-the-function-works"></a>Testar a função funciona

Para testar a função no portal, escolha **registos** na parte inferior do editor de código. Em seguida, escolha **teste** à direita do editor de código. Utilize o seguinte JSON como o **corpo do pedido**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de registo de função são apresentados no **registos** painel:

![Saída de registo de função](media/howto-create-custom-rules/function-app-logs.png)

Após alguns minutos, o **para** endereço de e-mail recebe um e-mail com o seguinte conteúdo:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Adicionar consulta do Stream Analytics

Esta solução utiliza uma consulta do Stream Analytics para detectar quando um dispositivo para enviar telemetria durante mais de 120 segundos. A consulta utiliza a telemetria do hub de eventos como entrada. A tarefa envia os resultados da consulta para a aplicação de funções. Nesta secção, configurar a tarefa do Stream Analytics:

1. No portal do Azure, navegue para a tarefa do Stream analytics, em **topologia de tarefas** selecionar **entradas**, escolha **+ Adicionar entrada de fluxo**e, em seguida, escolha **eventos Hub**.
1. Utilize as informações na tabela seguinte para configurar a entrada com o hub de eventos que criou anteriormente, em seguida, escolha **guardar**:

    | Definição | Value |
    | ------- | ----- |
    | Alias de entrada | centraltelemetry |
    | Subscrição | A sua subscrição |
    | Espaço de nomes do Hub de Eventos | O espaço de nomes do Hub de eventos |
    | O nome do hub de eventos | Utilizar existente - **centralexport** |

1. Sob **topologia de tarefas**, selecione **saídas**, escolha **+ adicionar**e, em seguida, selecione **função do Azure**.
1. Utilize as informações na tabela seguinte para configurar a saída, em seguida, escolha **guardar**:

    | Definição | Value |
    | ------- | ----- |
    | Alias de saída | EmailNotification |
    | Subscrição | A sua subscrição |
    | Function App | A aplicação de funções |
    | Função  | HttpTrigger1 |

1. Sob **topologia de tarefas**, selecione **consulta** e substitua a consulta existente com o SQL seguinte:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Selecione **Guardar**.
1. Para iniciar a tarefa de Stream Analytics, escolha **descrição geral**, em seguida, **iniciar**, em seguida, **agora**e, em seguida **iniciar**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configurar a exportação no Centro de IoT

Navegue para o [aplicação IoT Central](https://aka.ms/iotcentral) criada a partir do modelo de Contoso. Nesta secção, irá configurar a aplicação para transmitir a telemetria dos seus dispositivos simulados para o event hub. Para configurar a exportação:

1. Navegue para o **a exportação contínua de dados** página, selecione **+ novo**e, em seguida **Hubs de eventos**.
1. Utilize as seguintes definições para configurar a exportação, em seguida, selecione **guardar**:

    | Definição | Value |
    | ------- | ----- |
    | Nome a apresentar | Exportar para os Hubs de Eventos |
    | Enabled | Ativa |
    | Espaço de nomes dos Event Hubs | O nome do espaço de nomes de Hubs de eventos |
    | Hub de eventos | centralexport |
    | Medições | Ativa |
    | Dispositivos | Desativada |
    | Modelos de Dispositivos | Desativada |

![Configuração da exportação contínua de dados](media/howto-create-custom-rules/cde-configuration.png)

Aguarde até que seja o estado de exportação **em execução** antes de continuar.

## <a name="test"></a>Teste

Para testar a solução, pode desativar a exportação contínua de dados do Centro de IoT para dispositivos simulados de parada:

1. Na aplicação do Centro de IoT, navegue para o **a exportação contínua de dados** página e selecione o **exportar para os Hubs de eventos** exportar configuração.
1. Definir **Enabled** para **desativar** e escolha **guardar**.
1. Depois de, pelo menos, dois minutos, o **para** endereço de e-mail recebe uma ou mais mensagens de correio eletrónico que ter um aspeto semelhante ao exemplo seguinte conteúdas:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Arrumá

Para organizar após este procedimentos e evitar custos desnecessários, elimine o **DetectStoppedDevices** grupo de recursos no portal do Azure.

Pode eliminar a aplicação do Centro de IoT do **gestão** página na aplicação.

## <a name="next-steps"></a>Passos Seguintes

Este guia de procedimentos, ficou a saber como:

* Stream telemetria a partir de uma aplicação do Centro de IoT com *exportação de dados contínua*.
* Crie uma consulta do Stream Analytics que Deteta quando um dispositivo parou de envio de dados.
* Envie uma notificação por e-mail com as funções do Azure e os serviços do SendGrid.

Agora que sabe como criar regras personalizadas e notificações, o passo seguinte sugerido é saber como [Visualize e analise os dados do Azure IoT Central no dashboard do Power BI](howto-connect-powerbi.md).
