---
title: Como cumprir comandos de um cliente com o SDK de Fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como lidar com atividades de Comandos Personalizados num cliente com o Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367754"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Cumprir comandos de um cliente com o SDK de Fala (Pré-visualização)

Para completar as tarefas utilizando uma aplicação de Comandos Personalizados, pode enviar cargas personalizadas para um dispositivo cliente conectado.

Neste artigo, vai:

- Defina e envie uma carga útil personalizada da JSON a partir da sua aplicação De comandos personalizados
- Receba e visualize os conteúdos de carga útil personalizados da JSON a partir de uma aplicação de cliente C# UWP Speech SDK

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Uma chave de subscrição Azure para o serviço de Fala
  - [Obtenha um de graça](get-started.md) ou crie-o no [portal Azure](https://portal.azure.com)
- Uma aplicação de Comandos Personalizados previamente criada
  - [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)
- Uma aplicação de clientes ativada por SDK de Fala
  - [Quickstart: Ligue-se a uma aplicação de comando personalizado com o SDK de Fala (Pré-visualização)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcional: Começar rápido

Este artigo descreve, passo a passo, como fazer uma aplicação de cliente para falar com a sua aplicação De Comandos Personalizados. Se preferir mergulhar, o código fonte completo e pronto a compilar utilizado neste artigo está disponível nas [Amostras SDK](https://aka.ms/csspeech/samples)do Discurso .

## <a name="fulfill-with-json-payload"></a>Cumprir com a carga útil da JSON

1. Abra a sua aplicação de Comandos Personalizados previamente criada do Estúdio de [Discurso](https://speech.microsoft.com/)
1. Consulte a secção **Regras de Conclusão** para se certificar de que tem a regra previamente criada que responde ao utilizador
1. Para enviar uma carga diretamente ao cliente, crie uma nova regra com uma ação de Atividade de Envio

   > [!div class="mx-imgBorder"]
   > ![Enviar regra de conclusão da atividade](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome da Regra | UpdateDeviceState | Um nome que descreve o propósito da regra |
   | Condições | Parâmetro necessário - `OnOff` e`SubjectDevice` | Condições que determinam quando a regra pode ser executada |
   | Ações | `SendActivity`(ver abaixo) | A ação a tomar quando a condição da regra é verdadeira |

   > [!div class="mx-imgBorder"]
   > ![Enviar carga útil da Atividade](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Criar visuais para dispositivo dentro ou fora do estado

Em [Quickstart: Ligue-se a uma aplicação de Comando Personalizado com o SDK de Fala (Pré-visualização)](./quickstart-custom-speech-commands-speech-sdk.md) criou uma aplicação de cliente SDK de Discurso que lidou com comandos como `turn on the tv`, `turn off the fan`. Adicione alguns visuais para que possa ver o resultado desses comandos.

Adicione caixas etiquetadas com texto indicando **On** ou **Off** utilizando o seguinte XML adicionado a`MainPage.xaml.cs`

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

Agora que criou uma carga útil JSON, pode adicionar uma referência à biblioteca [JSON.NET](https://www.newtonsoft.com/json) para lidar com a desserialização.

> [!div class="mx-imgBorder"]
> ![Enviar carga útil da Atividade](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Adicione `InitializeDialogServiceConnector` o seguinte `ActivityReceived` ao seu manipulador de eventos. O código adicional extrairá a carga útil da atividade e alterará o estado visual da televisão ou do ventilador em conformidade.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar validações aos parâmetros de Comando Personalizado (pré-visualização)](./how-to-custom-speech-commands-validations.md)
