---
title: Enviar atividade de Comandos Personalizados para a aplicação do cliente
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende a enviar atividade a partir de uma aplicação de Comandos Personalizados para uma aplicação de cliente que executa o SDK de discurso.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566100"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Enviar atividade de Comandos Personalizados para a aplicação do cliente

Neste artigo, aprende a enviar atividade a partir de uma aplicação de Comandos Personalizados para uma aplicação de cliente que executa o SDK de discurso.

Completa as seguintes tarefas:

- Defina e envie uma carga personalizada JSON a partir da sua aplicação de Comandos Personalizados
- Receber e visualizar os conteúdos de carga útil personalizados da JSON a partir de uma aplicação de cliente SDK de discurso UWP UWP

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ou superior. Este guia utiliza o Visual Studio 2019
> * Uma chave de subscrição do Azure para o serviço de Voz: [Obtenha uma gratuita](overview.md#try-the-speech-service-for-free) ou crie-a no [portal do Azure](https://portal.azure.com)
> * Uma [aplicação de Comandos Personalizados criada](quickstart-custom-commands-application.md) anteriormente
> * Uma aplicação de cliente ativada pela SDK: [Como fazê-lo: Integre-se com uma aplicação de cliente usando a Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Configuração Enviar atividade ao cliente 
1. Abra a aplicação Comandos Personalizados que criou anteriormente
1. Selecione o comando **TurnOnOff,** **selecione ConfirmationResponse** sob a regra de conclusão e, em seguida, selecione **Adicionar uma ação**
1. Sob **Novo Tipo de Ação,** selecione Enviar atividade ao **cliente**
1. Copie o JSON abaixo para **o conteúdo da Atividade**
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. Clique em **Guardar** para criar uma nova regra com uma ação de Atividade de Envio, **Treinar** e **Publicar** a alteração

   > [!div class="mx-imgBorder"]
   > ![Enviar regra de conclusão da atividade](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrar com a aplicação cliente

Em [Como-a: Configurar a aplicação do cliente com a Speech SDK (Preview)](./how-to-custom-commands-setup-speech-sdk.md)criou uma aplicação de cliente UWP com a Speech SDK que manuseava comandos `turn on the tv` como, `turn off the fan` . . Com alguns visuais adicionados, pode ver o resultado desses comandos.

Para adicionar caixas etiquetadas com texto indicando **dentro** ou **fora**, adicione o seguinte bloco XML do StackPanel a `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Adicionar bibliotecas de referência

Uma vez que criou uma carga JSON, precisa adicionar uma referência à biblioteca [JSON.NET](https://www.newtonsoft.com/json) para lidar com a deserialização.

1. Cliente certo a sua solução.
1. Escolha **Gerir pacotes nuget para solução,** selecione **Navegar** 
1. Se já instalou **Newtonsoft.js,** certifique-se de que a sua versão é de pelo menos 12.0.3. Caso contrário, vá a **Gerir Pacotes NuGet para solução - Atualizações,** procure **Newtonsoft.js** para atualizá-lo. Este guia está a utilizar a versão 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Enviar carga útil da atividade](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Além disso, certifique-se de que o pacote NuGet **Microsoft.NETCore.UniversalWindowsPlatform** é de pelo menos 6.2.10. Este guia está a utilizar a versão 6.2.10.

Em 'MainPage.xaml.cs', adicione

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Manuseie a carga útil recebida

Em `InitializeDialogServiceConnector` , substitua o `ActivityReceived` manipulador de eventos pelo seguinte código. O manipulador de eventos modificado `ActivityReceived` extrairá a carga útil da atividade e alterará o estado visual do televisor ou do ventilador, respectivamente.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Experimente

1. Iniciar a aplicação
1. Selecione Ativar microfone
1. Selecione o botão Falar
1. Diga `turn on the tv`
1. O estado visual da televisão deve mudar para "on"
   > [!div class="mx-imgBorder"]
   > ![Screenshot que mostra que o estado visual do T V está agora ligado.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: configurar pontos finais web (Pré-visualização)](./how-to-custom-commands-setup-web-endpoints.md)
