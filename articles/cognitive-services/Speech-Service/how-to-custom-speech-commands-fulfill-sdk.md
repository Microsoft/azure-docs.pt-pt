---
title: Como atender a comandos personalizados no cliente com o SDK de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como lidar com atividades de comandos personalizados no cliente com o SDK de fala.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: b55bb1c8379cf0a80a95aa0ba1a29297154d5831
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156510"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Como: preencher comandos no cliente com o SDK de fala (versão prévia)

Para concluir tarefas usando um aplicativo de comandos personalizados, você pode enviar cargas personalizadas para um dispositivo de cliente conectado.

Neste artigo, você vai:

- Definir e enviar uma carga JSON personalizada de seu aplicativo de comandos personalizados
- Receber e visualizar o conteúdo da carga JSON personalizada de C# um aplicativo cliente do SDK de fala do UWP

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Uma chave de assinatura do Azure para o serviço de fala
  - [Obtenha um gratuitamente](get-started.md) ou crie-o no [portal do Azure](https://portal.azure.com)
- Um aplicativo de comandos personalizados criado anteriormente
  - [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
- Um aplicativo cliente habilitado para SDK de fala
  - [Início rápido: conectar a um aplicativo de comando personalizado com o SDK de fala (versão prévia)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcional: introdução rápida

Este artigo descreve, passo a passo, como fazer um aplicativo cliente se comunicar com seu aplicativo de comandos personalizados. Se você preferir se aprofundar diretamente no, o código-fonte completo e pronto para compilar usado neste artigo está disponível nos exemplos do [SDK de fala](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Atender à carga JSON

1. Abra o aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Marque a seção **regras de conclusão** para verificar se você tem a regra criada anteriormente que responde de volta ao usuário
1. Para enviar uma carga diretamente para o cliente, crie uma nova regra com uma ação enviar atividade

   > [!div class="mx-imgBorder"]
   > ![regra de conclusão de atividade de envio](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome da Regra | UpdateDeviceState | Um nome que descreve a finalidade da regra |
   | Condições | Parâmetro necessário-`OnOff` e `SubjectDevice` | Condições que determinam quando a regra pode ser executada |
   | Ações | `SendActivity` (veja abaixo) | A ação a ser tomada quando a condição da regra for verdadeira |

   > [!div class="mx-imgBorder"]
   > ](media/custom-speech-commands/fulfill-sdk-send-activity-action.png) ![carga da atividade de envio

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Criar visuais para o estado ligado ou desligado do dispositivo

No [início rápido: conectar a um aplicativo de comando personalizado com o SDK de fala (versão prévia)](./quickstart-custom-speech-commands-speech-sdk.md) você criou um aplicativo cliente de SDK de fala que tratou comandos como `turn on the tv`, `turn off the fan`. Agora, adicione alguns visuais para que você possa ver o resultado desses comandos.

Adicione caixas rotuladas com texto indicando **Ativar** ou **desativar** usando o seguinte XML adicionado ao `MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Tratar conteúdo personalizável

Agora que você criou uma carga JSON, é possível adicionar uma referência à biblioteca [JSON.net](https://www.newtonsoft.com/json) para lidar com a desserialização.

> [!div class="mx-imgBorder"]
> ](media/custom-speech-commands/fulfill-sdk-json-nuget.png) ![carga da atividade de envio

Em `InitializeDialogServiceConnector` adicione o seguinte ao manipulador de eventos de `ActivityReceived`. O código adicional extrairá a carga da atividade e alterará o estado visual da TV ou do ventilador de acordo.

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

## <a name="try-it-out"></a>Experimentar

1. Iniciar a aplicação
1. Selecione habilitar microfone
1. Selecione o botão falar
1. Digamos `turn on the tv`
1. O estado visual da TV deve mudar para "on"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar validações a parâmetros de comando personalizados (visualização)](./how-to-custom-speech-commands-validations.md)
