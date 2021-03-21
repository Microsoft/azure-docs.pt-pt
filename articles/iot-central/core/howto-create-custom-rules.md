---
title: Estender a Azure IoT Central com regras e notificações personalizadas | Microsoft Docs
description: Como desenvolvedor de soluções, configurar uma aplicação IoT Central para enviar notificações de e-mail quando um dispositivo deixa de enviar telemetria. Esta solução utiliza Azure Stream Analytics, Azure Functions e SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 824308b66803d2dfa05383ff06ce97c48626619d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179345"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Expandir o Azure IoT Central com regras personalizadas através do Stream Analytics, das Funções do Azure do SendGrid

Este guia de como fazer mostra-lhe, como desenvolvedor de soluções, como estender a sua aplicação IoT Central com regras e notificações personalizadas. O exemplo mostra o envio de uma notificação a um operador quando um dispositivo deixa de enviar telemetria. A solução utiliza uma consulta [Azure Stream Analytics](../../stream-analytics/index.yml) para detetar quando um dispositivo deixou de enviar telemetria. O trabalho stream Analytics utiliza [funções Azure](../../azure-functions/index.yml) para enviar e-mails de notificação usando [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Este guia de como fazer mostra-lhe como estender a IoT Central para além do que já pode fazer com as regras e ações incorporadas.

Neste guia de como guiar, aprende-se a:

* Telemetria de fluxo a partir de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie uma consulta stream Analytics que detete quando um dispositivo deixou de enviar dados.
* Envie uma notificação por e-mail utilizando os serviços Azure Functions e SendGrid.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicação IoT Central

Crie uma aplicação IoT Central no site do [gestor de aplicações Azure IoT Central](https://aka.ms/iotcentral) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicação | Análise na loja – monitorização de condições |
| Nome da aplicação | Aceite o padrão ou escolha o seu próprio nome |
| URL | Aceite o prefixo padrão ou escolha o seu próprio prefixo URL único |
| Diretório | Seu inquilino do Diretório Ativo Azure |
| Subscrição do Azure | A sua subscrição do Azure |
| Region | A sua região mais próxima |

Os exemplos e imagens deste artigo utilizam a região **dos Estados Unidos.** Escolha um local próximo de si e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicação inclui dois dispositivos de termóstato simulado que enviam telemetria.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que cria. Crie os seus recursos Azure no mesmo local que a sua aplicação IoT Central.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal Azure para criar um espaço de nomes de Centros de Eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o seu nome de espaço de nome |
| Escalão de preço | Básico |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| Localização | E.U.A Leste |
| Unidades de Débito | 1 |

### <a name="stream-analytics-job"></a>Trabalho de Stream Analytics

Utilize o [portal Azure para criar um trabalho stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o nome do seu trabalho |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| Localização | E.U.A Leste |
| Ambiente de alojamento | Cloud |
| Unidades de transmissão em fluxo | 3 |

### <a name="function-app"></a>Aplicação de funções

Utilize o [portal Azure para criar uma aplicação de função](https://portal.azure.com/#create/Microsoft.FunctionApp) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome da aplicação    | Escolha o nome da aplicação da sua função |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| SO | Windows |
| Plano de Alojamento | Plano de Consumo |
| Localização | E.U.A Leste |
| Pilha de Tempo de Execução | .NET |
| Armazenamento | Criar novo |

### <a name="sendgrid-account-and-api-keys"></a>Conta SendGrid e Chaves API

Se não tiver uma conta Sendgrid, crie uma [conta gratuita](https://app.sendgrid.com/) antes de começar.

1. A partir das Definições do painel de instrumentos de envio no menu esquerdo, selecione **Teclas API**.
1. Clique **em Criar Tecla API.**
1. Nomeie a nova chave **API AzureFunctionAccess.**
1. Clique **em Criar & Ver .**

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Screenshot da chave Create SendGrid API.":::

Depois, será-lhe dada uma chave API. Guarde esta cadeia para utilização posterior.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Você pode configurar uma aplicação IoT Central para exportar continuamente telemetria para um centro de eventos. Nesta secção, você cria um centro de eventos para receber telemetria a partir da sua aplicação IoT Central. O centro de eventos entrega a telemetria ao seu trabalho stream Analytics para processamento.

1. No portal Azure, navegue para o seu espaço de nomes De Event Hubs e selecione **+ Centro de Eventos.**
1. Nomeie o seu centro **de eventos centralexport,** e selecione **Criar**.

O seu espaço de nomes do Event Hubs parece a seguinte imagem: 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
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

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de registo de funções aparecem no painel **'Registos':**

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Adicionar consulta stream analytics

Esta solução utiliza uma consulta stream Analytics para detetar quando um dispositivo deixa de enviar telemetria por mais de 120 segundos. A consulta utiliza a telemetria do centro do evento como entrada. O trabalho envia os resultados da consulta para a aplicação de função. Nesta secção, configura o trabalho stream Analytics:

1. No portal Azure, navegue para o seu trabalho stream Analytics, em **Jobs topology** select **Inputs**, escolha **+ Adicionar entrada de fluxo**, e, em seguida, escolha Event **Hub**.
1. Utilize as informações na tabela seguinte para configurar a entrada utilizando o hub de eventos que criou anteriormente e, em seguida, escolha **Guardar**:

    | Definição | Valor |
    | ------- | ----- |
    | Alias de entrada | centraltelemetria |
    | Subscrição | A sua subscrição |
    | Espaço de nomes do hub de eventos | O seu espaço de nomes do Centro de Eventos |
    | Nome do Hub de Eventos | Utilizar o ex-exposição existente - **centralexport** |

1. Em **Topologia de Jobs,** selecione **Outputs,** escolha **+ Adicionar** e, em seguida, escolha **Azure Function**.
1. Utilize as informações na tabela a seguir para configurar a saída e, em seguida, escolha **Guardar:**

    | Definição | Valor |
    | ------- | ----- |
    | Alias de saída | e-mailnotificação |
    | Subscrição | A sua subscrição |
    | Aplicação de funções | O seu aplicativo de função |
    | Função  | HttpTrigger1 |

1. Em **Topologia de Jobs,** **selecione Consulta** e substitua a consulta existente pelo seguinte SQL:

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
1. Para iniciar o trabalho stream Analytics, escolha **o Overview**, em seguida, **Iniciar**, em seguida, **Agora**, e, em seguida, **começar**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Screenshot de Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central 

No site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) navegue para a aplicação IoT Central que criou.

Nesta secção, configura a aplicação para transmitir a telemetria dos seus dispositivos simulados para o seu centro de eventos. Para configurar a exportação:

1. Navegue para a página **data export,** selecione **+ Novos**, e depois **Azure Event Hubs**.
1. Utilize as seguintes definições para configurar a exportação e, em seguida, **selecione Guardar**: 

    | Definição | Valor |
    | ------- | ----- |
    | Nome a Apresentar | Exportação para Centros de Eventos |
    | Ativado | Ativado |
    | Tipo de dados para exportar | Telemetria |
    | Enriquecimentos | Introduza a chave desejada / Valor de como pretende que os dados exportados sejam organizados | 
    | Destino | Criar Novas informações para onde os dados serão exportados |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Screenshot da Exportação de Dados.":::

Aguarde até que o estado de exportação esteja **a funcionar** antes de continuar.

## <a name="test"></a>Teste

Para testar a solução, pode desativar a exportação contínua de dados da IoT Central para dispositivos de paragem simulados:

1. Na sua aplicação IoT Central, navegue na página **Data Export** e selecione a configuração **de exportação de Centros de Exportação para Eventos.**
1. Definir **Ativado** para **desligar** e escolher **Guardar**.
1. Após pelo menos dois minutos, o endereço de e-mail **Para** enviar um ou mais e-mails que se parecem com o seguinte conteúdo de exemplo:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Arrumar tudo

Para arrumar depois deste como fazer e evitar custos desnecessários, elimine o grupo de recursos **DetectStoppedDevices** no portal Azure.

Pode eliminar a aplicação IoT Central da página **Gestão** dentro da aplicação.

## <a name="next-steps"></a>Passos seguintes

Neste guia de como guiar, aprendeu a:

* Telemetria de fluxo a partir de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie uma consulta stream Analytics que detete quando um dispositivo deixou de enviar dados.
* Envie uma notificação por e-mail utilizando os serviços Azure Functions e SendGrid.

Agora que sabe como criar regras e notificações personalizadas, o próximo passo sugerido é aprender a [estender o Azure IoT Central com análises personalizadas.](howto-create-custom-analytics.md)
