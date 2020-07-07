---
title: Estender a Azure IoT Central com regras e notificações personalizadas Microsoft Docs
description: Como desenvolvedor de soluções, configurar uma aplicação IoT Central para enviar notificações de e-mail quando um dispositivo deixa de enviar telemetria. Esta solução utiliza Azure Stream Analytics, Azure Functions e SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80158151"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Estender a Azure IoT Central com regras personalizadas usando Stream Analytics, Funções Azure e SendGrid



Este guia de como fazer mostra-lhe, como desenvolvedor de soluções, como estender a sua aplicação IoT Central com regras e notificações personalizadas. O exemplo mostra o envio de uma notificação a um operador quando um dispositivo deixa de enviar telemetria. A solução utiliza uma consulta [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) para detetar quando um dispositivo deixou de enviar telemetria. O trabalho stream Analytics utiliza [funções Azure](https://docs.microsoft.com/azure/azure-functions/) para enviar e-mails de notificação usando [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

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
| Região | A sua região mais próxima |

Os exemplos e imagens deste artigo utilizam a região **dos Estados Unidos.** Escolha um local próximo de si e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicação inclui dois dispositivos de termóstato simulado que enviam telemetria.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que cria. Crie os seus recursos Azure no mesmo local que a sua aplicação IoT Central.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal Azure para criar um espaço de nomes de Centros de Eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o seu nome de espaço de nome |
| Escalão de preço | Básica |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| Localização | E.U.A. Leste |
| Unidades de Débito | 1 |

### <a name="stream-analytics-job"></a>Trabalho de Stream Analytics

Utilize o [portal Azure para criar um trabalho stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o nome do seu trabalho |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| Localização | E.U.A. Leste |
| Ambiente de alojamento | Cloud |
| Unidades de transmissão em fluxo | 3 |

### <a name="function-app"></a>Function app

Utilize o [portal Azure para criar uma aplicação de função](https://portal.azure.com/#create/Microsoft.FunctionApp) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome da aplicação    | Escolha o nome da aplicação da sua função |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| SO | Windows |
| Plano de Alojamento | Plano de Consumo |
| Localização | E.U.A. Leste |
| Pilha de Tempo de Execução | .NET |
| Armazenamento | Criar novo |

### <a name="sendgrid-account"></a>Conta SendGrid

Utilize o [portal Azure para criar uma conta SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o nome da sua conta SendGrid |
| Palavra-passe | Criar uma senha |
| Subscrição | A sua subscrição |
| Grupo de recursos | Deteta desacossados |
| Escalão de preço | F1 Gratuito |
| Informações de contacto | Preencha as informações necessárias |

Quando cria todos os recursos necessários, o seu grupo de recursos **DetectStoppedDevices** parece a seguinte imagem:

![Detetar dispositivos parados grupo de recursos](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Você pode configurar uma aplicação IoT Central para exportar continuamente telemetria para um centro de eventos. Nesta secção, você cria um centro de eventos para receber telemetria a partir da sua aplicação IoT Central. O centro de eventos entrega a telemetria ao seu trabalho stream Analytics para processamento.

1. No portal Azure, navegue para o seu espaço de nomes De Event Hubs e selecione **+ Centro de Eventos.**
1. Nomeie o seu centro **de eventos centralexport,** e selecione **Criar**.

O seu espaço de nomes do Event Hubs parece a seguinte imagem:

![Espaço de nomes dos Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Obtenha a chave API SendGrid

A sua aplicação de função necessita de uma chave API SendGrid para enviar mensagens de correio e-mail. Para criar uma chave SendGrid API:

1. No portal Azure, navegue para a sua conta SendGrid. Em **seguida,** escolha Gerir para aceder à sua conta SendGrid.
1. Na sua conta SendGrid, escolha **Definições**e, em seguida, **Teclas API**. Escolha **criar a chave API:**

    ![Criar a chave API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na página **'Criar Chave API',** crie uma chave chamada **AzureFunctionAccesss** com permissões **de Acesso Completo.**
1. Tome nota da Chave API, precisa quando configurar a sua aplicação de função.

## <a name="define-the-function"></a>Definir a função

Esta solução utiliza uma aplicação Azure Functions para enviar uma notificação por e-mail quando o trabalho stream Analytics deteta um dispositivo parado. Para criar a sua aplicação de função:

1. No portal Azure, navegue para a instância **do Serviço de Aplicações** no grupo de recursos **DetectStoppedDevices.**
1. Selecione **+** para criar uma nova função.
1. Na página **ESCOLHA AMBIENTE,** escolha **In-portal** e, em seguida, selecione **Continue**.
1. Na página **CREATE A FUNCTION,** escolha **Webhook + API** e, em seguida, selecione **Criar**.

O portal cria uma função predefinida chamada **HttpTrigger1**:

![Função de gatilho HTTP padrão](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Vinculação de funções de configuração

Para enviar e-mails com SendGrid, tem de configurar as encadernações para a sua função da seguinte forma:

1. **Selecione Integrar,** escolha a saída **HTTP ($return)** e, em seguida, selecione **eliminar**.
1. Escolha **+ Saída Nova,** em seguida, escolha **SendGrid**e, em seguida, escolha **Selecione**. Escolha **instalar** para instalar a extensão SendGrid.
1. Quando a instalação estiver concluída, selecione **Use function return value**. Adicionar um endereço válido **Para receber** notificações por e-mail.  Adicionar um **válido Do endereço** para usar como remetente de e-mail.
1. Selecione **novo** ao lado da Definição de **Aplicação da Chave API da SendGrid**. **Insira o SendGridAPIKey** como a chave e a chave API SendGrid que notou anteriormente como o valor. Em seguida, selecione **Criar**.
1. Escolha **Guardar** para guardar as ligações SendGrid para a sua função.

As definições de integração parecem a seguinte imagem:

![Integrações de aplicativos de função](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Adicione o código de função

Para implementar a sua função, adicione o código C# para analisar o pedido HTTP de entrada e enviar os e-mails da seguinte forma:

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

1. **Selecione Guardar** para guardar a função.

### <a name="test-the-function-works"></a>Testar os trabalhos de função

Para testar a função no portal, escolha primeiro **Logs** na parte inferior do editor de código. Em seguida, escolha **teste** à direita do editor de código. Utilize o seguinte JSON como **organismo de pedido:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de registo de funções aparecem no painel **'Registos':**

![Saída de registo de funções](media/howto-create-custom-rules/function-app-logs.png)

Após alguns minutos, o endereço de e-mail **Para** enviar um e-mail com o seguinte conteúdo:

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
    | O nome do hub de eventos | Utilizar o ex-exposição existente - **centralexport** |

1. Em **Topologia de Jobs,** selecione **Outputs,** escolha **+ Adicionar**e, em seguida, escolha a **função Azure**.
1. Utilize as informações na tabela a seguir para configurar a saída e, em seguida, escolha **Guardar:**

    | Definição | Valor |
    | ------- | ----- |
    | Alias de saída | e-mailnotificação |
    | Subscrição | A sua subscrição |
    | Function app | O seu aplicativo de função |
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

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central

No site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) navegue para a aplicação IoT Central que criou a partir do modelo Contoso. Nesta secção, configura a aplicação para transmitir a telemetria dos seus dispositivos simulados para o seu centro de eventos. Para configurar a exportação:

1. Navegue para a página **data export,** selecione **+ Novos**, e depois **Azure Event Hubs**.
1. Utilize as seguintes definições para configurar a exportação e, em seguida, **selecione Guardar**:

    | Definição | Valor |
    | ------- | ----- |
    | Nome a Apresentar | Exportação para Centros de Eventos |
    | Ativado | Ativado |
    | Espaço de nomes dos Event Hubs | O nome do seu espaço de nome do Event Hubs |
    | Hub de eventos | centralexport |
    | Medições | Ativado |
    | Dispositivos | Desativado |
    | Modelos de dispositivo | Desativado |

![Configuração contínua da exportação de dados](media/howto-create-custom-rules/cde-configuration.png)

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
