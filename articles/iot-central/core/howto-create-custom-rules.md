---
title: Alargar a Azure IoT Central com regras e notificações personalizadas [ Microsoft Docs
description: Como um desenvolvedor de soluções, configure uma aplicação IoT Central para enviar notificações de e-mail quando um dispositivo parar de enviar telemetria. Esta solução utiliza o Azure Stream Analytics, as Funções Azure e a SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158151"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Alargar a Central Azure IoT com regras personalizadas utilizando stream analytics, funções azure e SendGrid



Este guia mostra-lhe, como desenvolvedor de soluções, como alargar a sua aplicação IoT Central com regras e notificações personalizadas. O exemplo mostra o envio de uma notificação a um operador quando um dispositivo deixa de enviar telemetria. A solução utiliza uma consulta [azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) para detetar quando um dispositivo deixou de enviar telemetria. O trabalho stream analytics utiliza [funções Azure](https://docs.microsoft.com/azure/azure-functions/) para enviar e-mails de notificação usando [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Este guia de como orientar mostra como estender a IoT Central para além do que já pode fazer com as regras e ações incorporadas.

Neste guia de como orientar, aprende-se a:

* Telemetria de fluxo de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie uma consulta de Stream Analytics que detete quando um dispositivo deixou de enviar dados.
* Envie uma notificação por e-mail utilizando as Funções Azure e os serviços SendGrid.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como guiar, precisa de uma subscrição azure ativa.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicação IoT Central

Criar uma aplicação IoT Central no site do gestor de [aplicações Azure IoT Central](https://aka.ms/iotcentral) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicação | Análise na loja – monitorização de condições |
| Nome da aplicação | Aceite o padrão ou escolha o seu próprio nome |
| do IdP | Aceite o padrão ou escolha o seu próprio prefixo URL único |
| Diretório | O seu inquilino de Diretório Ativo Azure |
| Subscrição do Azure | A sua subscrição do Azure |
| Região | A sua região mais próxima |

Os exemplos e imagens deste artigo utilizam a região **dos Estados Unidos.** Escolha um local próximo de si e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicação inclui dois dispositivos termóstatos simulados que enviam telemetria.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que cria. Crie os seus recursos Azure no mesmo local que a sua aplicação IoT Central.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal Azure para criar um espaço](https://portal.azure.com/#create/Microsoft.EventHub) de nome dos Hubs de Eventos com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o seu nome de espaço de nome |
| Escalão de preço | Básico |
| Subscrição | A sua subscrição |
| Grupo de recursos | Detetou Dispositivos Interrompidos |
| Localização | E.U.A. Leste |
| Unidades de Débito | 1 |

### <a name="stream-analytics-job"></a>Trabalho de Análise de Fluxo

Utilize o [portal Azure para criar um trabalho de Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o seu nome de trabalho |
| Subscrição | A sua subscrição |
| Grupo de recursos | Detetou Dispositivos Interrompidos |
| Localização | E.U.A. Leste |
| Ambiente de alojamento | Nuvem |
| Unidades de transmissão em fluxo | 3 |

### <a name="function-app"></a>Function app

Utilize o [portal Azure para criar uma aplicação](https://portal.azure.com/#create/Microsoft.FunctionApp) de função com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome da aplicação    | Escolha o nome da sua app de função |
| Subscrição | A sua subscrição |
| Grupo de recursos | Detetou Dispositivos Interrompidos |
| SO | Windows |
| Plano de Alojamento | Plano de Consumo |
| Localização | E.U.A. Leste |
| Pilha de Tempo de Execução | .NET |
| Storage | Criar novo |

### <a name="sendgrid-account"></a>Conta SendGrid

Utilize o [portal Azure para criar uma conta SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o nome da sua conta SendGrid |
| Palavra-passe | Criar uma senha |
| Subscrição | A sua subscrição |
| Grupo de recursos | Detetou Dispositivos Interrompidos |
| Escalão de preço | F1 Gratuito |
| Informações de contacto | Preencha as informações necessárias |

Quando criou todos os recursos necessários, o grupo de recursos **DetectStoppedDevices** parece ser a seguinte imagem:

![Detetar grupo de recursos de dispositivos parados](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode configurar uma aplicação IoT Central para exportar continuamente telemetria para um centro de eventos. Nesta secção, você cria um hub de eventos para receber telemetria da sua aplicação IoT Central. O centro de eventos entrega a telemetria ao seu trabalho de Stream Analytics para processamento.

1. No portal Azure, navegue para o espaço de nome do Seu Event Hubs e selecione **+ Event Hub**.
1. Nomeie o seu centro de eventos **centralexport,** e selecione **Create**.

O espaço de nome do Seu Event Hubs parece a seguinte imagem:

![Espaço de nomes dos Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Obter chave API SendGrid

A sua aplicação de funções precisa de uma chave SendGrid API para enviar mensagens de correio eletrónico. Para criar uma chave API SendGrid:

1. No portal Azure, navegue para a sua conta SendGrid. Em seguida, escolha **Gerir** o acesso à sua conta SendGrid.
1. Na sua conta SendGrid, escolha **Definições**, em **seguida, Teclas API**. Escolha **criar a chave API:**

    ![Criar a chave API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na página **Chave Create API,** crie uma chave chamada **AzureFunctionAccess** com permissões **de acesso completo.**
1. Tome nota da Chave API, precisa dela quando configurar a sua aplicação de funções.

## <a name="define-the-function"></a>Definir a função

Esta solução utiliza uma aplicação Azure Functions para enviar uma notificação por e-mail quando o trabalho do Stream Analytics detetar um dispositivo parado. Para criar a sua aplicação de funções:

1. No portal Azure, navegue para a instância do Serviço de **Aplicações** no grupo de recursos **DetectStoppedDevices.**
1. Selecione **+** para criar uma nova função.
1. Na página **CHOOSE A DEVELOPMENT ENVIRONMENT,** escolha **In-portal** e, em seguida, selecione **Continuar**.
1. Na página **CREATE A FUNCTION,** escolha **Webhook + API** e, em seguida, selecione **Criar**.

O portal cria uma função predefinida chamada **HttpTrigger1:**

![Função de gatilho PADRÃO HTTP](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurar encadernações de função

Para enviar e-mails com sendGrid, precisa configurar as ligações para a sua função da seguinte forma:

1. Selecione **Integrar,** escolha a saída **HTTP ($return)** e, em seguida, selecione **eliminar**.
1. Escolha **+ Saída Nova,** escolha **SendGrid,** e depois escolha **Select**. Escolha **instalar** para instalar a extensão SendGrid.
1. Quando a instalação estiver concluída, selecione **Use o valor de devolução**da função . Adicione um endereço válido **para** receber notificações por e-mail.  Adicione um **endereço** válido para usar como remetente de e-mail.
1. Selecione **novas próximas** definições de aplicação de **chave API SendGrid**. Introduza o **SendGridAPIKey** como chave e a chave SendGrid API que observou anteriormente como o valor. Em seguida, selecione **Criar**.
1. Escolha **guardar** para guardar as encadernações SendGrid para a sua função.

As definições de integração parecem a seguinte imagem:

![Integrações de aplicativos de função](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Adicione o código de função

Para implementar a sua função, adicione o código C# para analisar o pedido http e envie os e-mails da seguinte forma:

1. Escolha a função **HttpTrigger1** na sua aplicação de função e substitua o código C# pelo seguinte código:

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

    Pode ver uma mensagem de erro até guardar o novo código.

1. Selecione **Guardar** para salvar a função.

### <a name="test-the-function-works"></a>Testar os trabalhos de função

Para testar a função no portal, escolha primeiro **Registos** na parte inferior do editor de código. Em seguida, escolha **o Teste** à direita do editor de código. Utilize o seguinte JSON como **organismo de pedido:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de registo de funções aparecem no painel **Registos:**

![Saída de registo de funções](media/howto-create-custom-rules/function-app-logs.png)

Após alguns minutos, o endereço de e-mail **Para** receber um e-mail com o seguinte conteúdo:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Adicionar consulta de Análise de Fluxo

Esta solução utiliza uma consulta de Stream Analytics para detetar quando um dispositivo para de enviar telemetria por mais de 120 segundos. A consulta usa a telemetria do centro de eventos como sua entrada. O trabalho envia os resultados da consulta para a aplicação de função. Nesta secção, configura o trabalho de Stream Analytics:

1. No portal Azure, navegue para o seu trabalho de Stream Analytics, sob a **topologia Jobs** selecione **Inputs,** escolha + Adicione a entrada de **fluxo,** e depois escolha **o Event Hub**.
1. Utilize as informações na tabela seguinte para configurar a entrada utilizando o centro de eventos que criou anteriormente e, em seguida, escolha **Guardar:**

    | Definição | Valor |
    | ------- | ----- |
    | Alias de entrada | centraltelemetria |
    | Subscrição | A sua subscrição |
    | Espaço de nomes do hub de eventos | O seu espaço de nome do Hub de Eventos |
    | O nome do hub de eventos | Utilização existente - **centralexport** |

1. Em **Jobs topologia,** selecione **Outputs,** escolha **+ Adicionar,** e, em seguida, escolha a **função Azure**.
1. Utilize as informações no quadro seguinte para configurar a saída e, em seguida, escolha **Guardar:**

    | Definição | Valor |
    | ------- | ----- |
    | Alias de saída | notificação por e-mail |
    | Subscrição | A sua subscrição |
    | Function app | A sua aplicação de funções |
    | Função  | HttpTrigger1 |

1. Em **Topologia Jobs,** selecione **Consulta** e substitua a consulta existente pelo seguinte SQL:

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
1. Para iniciar o trabalho de Stream Analytics, escolha **visão geral,** em **seguida, Iniciar,** em **seguida, agora**, e, em seguida, **iniciar:**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central

No site do gestor de [aplicações Azure IoT Central,](https://aka.ms/iotcentral) navegue para a aplicação IoT Central que criou a partir do modelo Contoso. Nesta secção, configura a aplicação para transmitir a telemetria dos seus dispositivos simulados para o seu centro de eventos. Para configurar a exportação:

1. Navegue na página **de Exportação** de Dados, selecione **+ Novo,** e depois **Azure Event Hubs**.
1. Utilize as seguintes definições para configurar a exportação e, em seguida, **selecione Guardar:**

    | Definição | Valor |
    | ------- | ----- |
    | Nome a Apresentar | Exportação para Centros de Eventos |
    | Ativado | Ativado |
    | Espaço de nomes dos Event Hubs | Nome do nome do nome do seu evento Hubs |
    | Hub de eventos | centralexporte |
    | Medições | Ativado |
    | Dispositivos | Desativado |
    | Modelos de dispositivo | Desativado |

![Configuração contínua da exportação de dados](media/howto-create-custom-rules/cde-configuration.png)

Aguarde até que o estado de exportação **esteja em funcionamento** antes de continuar.

## <a name="test"></a>Test

Para testar a solução, pode desativar a exportação contínua de dados da IoT Central para dispositivos parados simulados:

1. Na sua aplicação IoT Central, navegue para a página **de Exportação** de Dados e selecione a configuração de **exportação para Hubs de Eventos.**
1. Definir **Ativado** para **desligar** e escolher **Guardar**.
1. Após pelo menos dois minutos, o endereço de e-mail **Para** recebe um ou mais e-mails que se parecem com o seguinte conteúdo exemplo:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Arrumação

Para arrumar depois deste como e evitar custos desnecessários, elimine o grupo de recursos **DetectStoppedDevices** no portal Azure.

Pode eliminar a aplicação IoT Central da página **De Gestão** dentro da aplicação.

## <a name="next-steps"></a>Passos seguintes

Neste guia de como orientar, aprendeu a:

* Telemetria de fluxo de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie uma consulta de Stream Analytics que detete quando um dispositivo deixou de enviar dados.
* Envie uma notificação por e-mail utilizando as Funções Azure e os serviços SendGrid.

Agora que sabe como criar regras e notificações personalizadas, o próximo passo sugerido é aprender a estender o [Azure IoT Central com análises personalizadas.](howto-create-custom-analytics.md)
