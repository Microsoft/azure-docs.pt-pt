---
title: Estender os IoT Central do Azure com regras e notificações personalizadas | Microsoft Docs
description: Como desenvolvedor de soluções, configure um aplicativo IoT Central para enviar notificações por email quando um dispositivo parar de enviar telemetria. Essa solução usa Azure Stream Analytics e Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c31fa96457a3945c39fcc34770cb6783af3b81e8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049113"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Estender IoT Central do Azure com regras personalizadas que enviam notificações

Este guia de instruções mostra, como um desenvolvedor de soluções, como estender seu aplicativo IoT Central com regras e notificações personalizadas. O exemplo mostra o envio de uma notificação para um operador quando um dispositivo para de enviar telemetria. A solução usa uma consulta [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) para detectar quando um dispositivo parou de enviar telemetria. O trabalho de Stream Analytics usa [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) para enviar emails de notificação usando o [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Este guia de instruções mostra como estender IoT Central além do que ele já pode fazer com as regras e ações internas.

Neste guia de instruções, você aprende a:

* Transmita a telemetria de um aplicativo IoT Central usando a *exportação de dados contínuas*.
* Crie uma consulta Stream Analytics que detecta quando um dispositivo parou de enviar dados.
* Envie uma notificação por email usando os serviços Azure Functions e SendGrid.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisa de uma assinatura ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>IoT Central aplicativo

Crie um aplicativo IoT Central na página [IOT central do Azure – meus aplicativos](https://aka.ms/iotcentral) com as seguintes configurações:

| Definição | Value |
| ------- | ----- |
| Plano de pagamento | "Pay As You Go" |
| Modelo de aplicação | Amostra Contoso |
| Nome da aplicação | Aceite o padrão ou escolha seu próprio nome |
| URL | Aceite o padrão ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Seu locatário Azure Active Directory |
| Subscrição do Azure | A sua subscrição do Azure |
| Região | East US |

Os exemplos e capturas de tela neste artigo usam a região **leste dos EUA** . Escolha um local perto de você e certifique-se de criar todos os seus recursos na mesma região.

### <a name="resource-group"></a>Resource group

Use o [portal do Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que você criar. Crie seus recursos do Azure no mesmo local que o aplicativo IoT Central.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Use o [portal do Azure para criar um namespace de hubs de eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes configurações:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o nome do namespace |
| Escalão de preço | Básica |
| Subscription | A sua subscrição |
| Resource group | DetectStoppedDevices |
| Location | East US |
| Unidades de Débito | 1 |

### <a name="stream-analytics-job"></a>Tarefa do Stream Analytics

Use o [portal do Azure para criar um trabalho de Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) com as seguintes configurações:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o nome do seu trabalho |
| Subscription | A sua subscrição |
| Resource group | DetectStoppedDevices |
| Location | East US |
| Ambiente de alojamento | Nuvem |
| Unidades de transmissão em fluxo | 3 |

### <a name="function-app"></a>Function app

Use o [portal do Azure para criar um aplicativo de funções](https://portal.azure.com/#create/Microsoft.FunctionApp) com as seguintes configurações:

| Definição | Value |
| ------- | ----- |
| Nome da aplicação    | Escolha o nome do aplicativo de funções |
| Subscription | A sua subscrição |
| Resource group | DetectStoppedDevices |
| OS | Windows |
| Plano de Alojamento | Plano de Consumo |
| Location | East US |
| Pilha de Tempo de Execução | .NET |
| Armazenamento | Criar novo |

### <a name="sendgrid-account"></a>Conta do SendGrid

Use o [portal do Azure para criar uma conta do SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) com as seguintes configurações:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o nome da conta do SendGrid |
| Palavra-passe | Criar uma palavra-passe |
| Subscription | A sua subscrição |
| Resource group | DetectStoppedDevices |
| Escalão de preço | F1 Gratuito |
| Informação de contacto | Preencher as informações necessárias |

Quando você tiver criado todos os recursos necessários, seu grupo de recursos **DetectStoppedDevices** será semelhante à captura de tela a seguir:

![Detectar o grupo de recursos de dispositivos interrompidos](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Você pode configurar um aplicativo de IoT Central para exportar continuamente a telemetria para um hub de eventos. Nesta seção, você cria um hub de eventos para receber telemetria do seu aplicativo IoT Central. O Hub de eventos fornece a telemetria para seu trabalho de Stream Analytics para processamento.

1. Na portal do Azure, navegue até o namespace de seus hubs de eventos e selecione **+ Hub de eventos**.
1. Nomeie o **centralexport**do hub de eventos e selecione **criar**.

O namespace dos hubs de eventos é semelhante à captura de tela a seguir:

![Espaço de nomes dos Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Obter chave de API do SendGrid

Seu aplicativo de funções precisa de uma chave de API SendGrid para enviar mensagens de email. Para criar uma chave de API do SendGrid:

1. Na portal do Azure, navegue até sua conta do SendGrid. Em seguida, escolha **gerenciar** para acessar sua conta do SendGrid.
1. Em sua conta do SendGrid, escolha **configurações**e, em seguida, **chaves de API**. Escolha **criar chave de API**:

    ![Criar chave de API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na página **criar chave de API** , crie uma chave chamada **AzureFunctionAccess** com permissões de **acesso completo** .
1. Anote a chave de API, você precisará dela quando configurar seu aplicativo de funções.

## <a name="define-the-function"></a>Definir a função

Essa solução usa um aplicativo Azure Functions para enviar uma notificação por email quando o trabalho de Stream Analytics detecta um dispositivo interrompido. Para criar seu aplicativo de funções:

1. Na portal do Azure, navegue até a instância do **serviço de aplicativo** no grupo de recursos **DetectStoppedDevices** .
1. Selecione **+** para criar uma nova função.
1. Na página **escolher um ambiente de desenvolvimento** , escolha **no portal** e, em seguida, selecione **continuar**.
1. Na página **criar uma função** , escolha **webhook + API** e, em seguida, selecione **criar**.

O portal cria uma função padrão chamada **HttpTrigger1**:

![Função de gatilho HTTP padrão](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurar associações de função

Para enviar emails com o SendGrid, você precisa configurar as associações para a função da seguinte maneira:

1. Selecione **integrar**, escolha o http de saída **($Return)** e, em seguida, selecione **excluir**.
1. Escolha **+ nova saída**, escolha **SendGrid**e escolha **selecionar**. Escolha **instalar** para instalar a extensão SendGrid.
1. Quando a instalação for concluída, selecione **usar valor de retorno da função**. Adicione um **endereço de endereçamento** válido para receber notificações por email.  Adicione um **endereço de** remetente válido para usar como o emissor do email.
1. Selecione **novo** ao lado de **configuração do aplicativo de chave de API do SendGrid**. Insira **SendGridAPIKey** como a chave e a chave de API SendGrid que você anotou anteriormente como o valor. Em seguida, selecione **Criar**.
1. Escolha **salvar** para salvar as associações SendGrid para sua função.

As configurações de integração são parecidas com a seguinte captura de tela:

![Integrações do aplicativo de funções](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Adicionar o código de função

Para implementar sua função, adicione o C# código para analisar a solicitação HTTP de entrada e enviar os emails da seguinte maneira:

1. Escolha a função **HttpTrigger1** em seu aplicativo de funções e substitua C# o código pelo código a seguir:

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

    Você poderá ver uma mensagem de erro até salvar o novo código.

1. Selecione **salvar** para salvar a função.

### <a name="test-the-function-works"></a>Testar a função Works

Para testar a função no portal, primeiro escolha **logs** na parte inferior do editor de código. Em seguida, escolha **teste** à direita do editor de códigos. Use o JSON a seguir como o **corpo da solicitação**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de log de função aparecem no painel de **logs** :

![Saída de log de função](media/howto-create-custom-rules/function-app-logs.png)

Após alguns minutos, o endereço de email **para** recebe um email com o seguinte conteúdo:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Adicionar consulta de Stream Analytics

Essa solução usa uma consulta Stream Analytics para detectar quando um dispositivo para de enviar telemetria por mais de 120 segundos. A consulta usa a telemetria do hub de eventos como entrada. O trabalho envia os resultados da consulta para o aplicativo de funções. Nesta seção, você configura o trabalho de Stream Analytics:

1. No portal do Azure, navegue até o trabalho do Stream Analytics, em **topologia de trabalhos** selecione **entradas**, escolha **+ Adicionar entrada de fluxo**e, em seguida, selecione Hub de **eventos**.
1. Use as informações na tabela a seguir para configurar a entrada usando o Hub de eventos que você criou anteriormente e, em seguida, escolha **salvar**:

    | Definição | Value |
    | ------- | ----- |
    | Alias de entrada | centraltelemetry |
    | Subscription | A sua subscrição |
    | Espaço de nomes do hub de eventos | Seu namespace do hub de eventos |
    | O nome do hub de eventos | Usar- **centralexport** existente |

1. Em **topologia de trabalhos**, **selecione saídas**, escolha **+ Adicionar**e, em seguida, escolha **Azure function**.
1. Use as informações na tabela a seguir para configurar a saída e, em seguida, escolha **salvar**:

    | Definição | Value |
    | ------- | ----- |
    | Alias de saída | emailnotification |
    | Subscription | A sua subscrição |
    | Function app | Seu aplicativo de funções |
    | Função  | HttpTrigger1 |

1. Em **topologia de trabalhos**, selecione **consulta** e substitua a consulta existente pelo seguinte SQL:

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
1. Para iniciar o trabalho de Stream Analytics, escolha **visão geral**, **Iniciar**, **agora**e **Iniciar**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configurar a exportação no IoT Central

Navegue até o [aplicativo IOT central](https://aka.ms/iotcentral) que você criou no modelo da contoso. Nesta seção, você configura o aplicativo para transmitir a telemetria de seus dispositivos simulados para o Hub de eventos. Para configurar a exportação:

1. Navegue até a página **exportação de dados contínuas** , selecione **+ novo**e os **hubs de eventos do Azure**.
1. Use as configurações a seguir para configurar a exportação e, em seguida, selecione **salvar**:

    | Definição | Value |
    | ------- | ----- |
    | Nome a Apresentar | Exportar para os Hubs de Eventos |
    | Enabled | Ativado |
    | Espaço de nomes dos Event Hubs | Nome do namespace de seus hubs de eventos |
    | Hub de eventos | centralexport |
    | Medições | Ativado |
    | Dispositivos | Desativado |
    | Modelos de Dispositivos | Desativado |

![Configuração de exportação de dados contínuas](media/howto-create-custom-rules/cde-configuration.png)

Aguarde até que o status de exportação seja **executado** antes de continuar.

## <a name="test"></a>Teste

Para testar a solução, você pode desabilitar a exportação de dados contínuas de IoT Central para dispositivos interrompidos simulados:

1. No aplicativo IoT Central, navegue até a página **exportação de dados contínua** e selecione **exportar para a configuração de exportação dos hubs de eventos** .
1. Defina **habilitado** como **desativado** e escolha **salvar**.
1. Após pelo menos dois minutos, o endereço de email **to** receberá um ou mais emails parecidos com o seguinte conteúdo de exemplo:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Organizar

Para fazer a limpeza após este procedimento e evitar custos desnecessários, exclua o grupo de recursos **DetectStoppedDevices** no portal do Azure.

Você pode excluir o aplicativo IoT Central da página de **Gerenciamento** dentro do aplicativo.

## <a name="next-steps"></a>Passos seguintes

Neste guia de instruções, você aprendeu a:

* Transmita a telemetria de um aplicativo IoT Central usando a *exportação de dados contínuas*.
* Crie uma consulta Stream Analytics que detecta quando um dispositivo parou de enviar dados.
* Envie uma notificação por email usando os serviços Azure Functions e SendGrid.

Agora que você sabe como criar regras e notificações personalizadas, a próxima etapa sugerida é aprender a [estender IOT central do Azure com análises personalizadas](howto-create-custom-analytics.md).
