---
title: Como cumprir comandos de um cliente com o SDK de Fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como lidar com atividades de Comandos Personalizados num cliente com o Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871795"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Cumprir comandos de um cliente com o SDK de Fala (Pré-visualização)

Para completar as tarefas utilizando uma aplicação de Comandos Personalizados, pode enviar cargas personalizadas para um dispositivo cliente conectado.

Neste artigo, vai:

- Defina e envie uma carga útil personalizada da JSON a partir da sua aplicação De comandos personalizados
- Receba e visualize os conteúdos de carga útil personalizados da JSON a partir de uma aplicação de cliente C# UWP Speech SDK

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Chave de subscrição Azure para o serviço de Fala: [Obtenha uma gratuitamente](get-started.md) ou crie-a no [portal Azure](https://portal.azure.com)
> * Uma aplicação de comandos personalizados previamente criada: [Quickstart: Criar um Comando Personalizado com Parâmetros (Pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)
> * Aplicação ativada por SDK de Discurso: [Quickstart: Ligue-se a uma aplicação de comando personalizado com o SDK de Fala (Pré-visualização)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcional: Começar rápido

Este artigo descreve, passo a passo, como fazer uma aplicação de cliente para falar com a sua aplicação De Comandos Personalizados. Se preferir mergulhar, o código fonte completo e pronto a compilar utilizado neste artigo está disponível nas [Amostras SDK](https://aka.ms/csspeech/samples)do Discurso .

## <a name="fulfill-with-json-payload"></a>Cumprir com a carga útil da JSON

1. Abra a aplicação Comandos Personalizados que criou anteriormente a partir de [Quickstarts: Criar um comando personalizado com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)
1. Consulte a secção **Regras de Conclusão** para se certificar de que tem a regra previamente criada que responde ao utilizador
1. Para enviar uma carga diretamente ao cliente, crie uma nova regra com uma ação de Atividade de Envio

   > [!div class="mx-imgBorder"]
   > ![Enviar regra de conclusão da atividade](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome da Regra | UpdateDeviceState | Um nome que descreve o propósito da regra |
   | Condições | Parâmetro necessário - `OnOff` e`SubjectDevice` | Condições que determinam quando a regra pode ser executada |
   | Actions (Ações) | `SendActivity`(ver abaixo) | A ação a tomar quando a condição da regra é verdadeira |

1. Copiar o JSON abaixo para **o conteúdo de atividade**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Enviar carga útil da Atividade](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Criar visuais para dispositivo dentro ou fora do estado

Em [Quickstart: Connect to a Custom Command application with the Speech SDK,](./quickstart-custom-speech-commands-speech-sdk.md)you created `turn on the tv`a `turn off the fan`Speech SDK client application that handled commands tais como , . Com alguns visuais adicionados, pode ver o resultado desses comandos.

Adicione caixas etiquetadas com texto indicando **On** ou **Off** utilizando o seguinte XML adicionado a`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Manusear carga útil personalizável
### <a name="add-reference-libraries"></a>Adicionar bibliotecas de referência

Uma vez que criou uma carga útil JSON, precisa adicionar uma referência à biblioteca [JSON.NET](https://www.newtonsoft.com/json) para lidar com a desserialização.
- Cliente direito a sua solução.
- Escolha **gerir pacotes NuGet para solução,** selecione **instalar** 
- Procure **newtonsoft.json** na lista de atualizações, Update **Microsoft.NETCore.UniversalWindowsPlatform** para a versão mais recente

> [!div class="mx-imgBorder"]
> ![Enviar carga útil da Atividade](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Em 'MainPage.xaml.cs', adicione
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Cabo recebido carga

Em `InitializeDialogServiceConnector`, `ActivityReceived` substitua o manipulador de eventos por um código seguinte. O manipulador `ActivityReceived` de eventos modificado sairá da carga útil da atividade e alterará o estado visual da televisão ou ventilador em conformidade.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

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
1. Selecione ativar o microfone
1. Selecione o botão Falar
1. Dizer`turn on the tv`
1. O estado visual da televisão deve mudar para "On"
   > [!div class="mx-imgBorder"]
   > ![Enviar carga útil da Atividade](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar validações aos parâmetros de Comando Personalizado (pré-visualização)](./how-to-custom-speech-commands-validations.md)
