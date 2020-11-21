---
title: Configurar pontos finais Web (Pré-visualização)
titleSuffix: Azure Cognitive Services
description: configurar pontos finais Web para comandos personalizados
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: e50d7aba5cc5b3d5d620d844cc9ad169ad8b3bf6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025896"
---
# <a name="set-up-web-endpoints"></a>Configurar pontos finais Web

Neste artigo, vai aprender a configurar pontos finais Web numa aplicação de Comandos Personalizados que lhe permitem realizar pedidos HTTP a partir de uma aplicação cliente. Irá concluir as seguintes tarefas:

- Configurar os pontos finais Web na aplicação de Comandos Personalizados
- Chamar os pontos finais Web na aplicação de Comandos Personalizados
- Receber a resposta dos pontos finais Web 
- Integrar a resposta dos pontos finais Web num payload JSON personalizado, enviar e visualizá-la a partir de uma aplicação cliente com o SDK de Voz UWP C#

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Uma chave de subscrição do Azure para o serviço de Voz: [Obtenha uma gratuita](overview.md#try-the-speech-service-for-free) ou crie-a no [portal do Azure](https://portal.azure.com)
> * Uma [aplicação de Comandos Personalizados criada](quickstart-custom-commands-application.md) anteriormente
> * Uma aplicação cliente com o SDK de Voz ativado: [Procedimento: terminar a atividade para a aplicação cliente](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Configurar pontos finais Web

1. Abra a aplicação de Comandos Personalizados que criou anteriormente. 
1. Aceda a "Pontos finais Web" e clique em "Novo ponto final Web".

   > [!div class="mx-imgBorder"]
   > ![Novo ponto final Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome | UpdateDeviceState | Nome do ponto final Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | O URL do ponto final com o qual pretende que a sua aplicação de comando personalizado comunique. |
   | Método | POST | As interações permitidas (como GET, POST) com o seu ponto final.|
   | Cabeçalhos | Chave: aplicação, Valor: escolha os primeiros 8 dígitos do seu applicationId | Os parâmetros do cabeçalho a serem incluídos no cabeçalho do pedido.|

    > [!NOTE]
    > - O ponto final Web de exemplo criado com a [Função do Azure](../../azure-functions/index.yml), que se liga à base de dados que guarda o estado do dispositivo da TV e da ventoinha
    > - O cabeçalho sugerido apenas é necessário para o ponto final de exemplo
    > - Para garantir que o valor do cabeçalho é exclusivo no nosso ponto final de exemplo, utilize os 8 primeiros dígitos do seu applicationId
    > - No mundo real, o ponto final Web pode ser o ponto final para o [Hub IOT](../../iot-hub/about-iot-hub.md) que gere os seus dispositivos

1. Clique em **Guardar**.

## <a name="call-web-endpoints"></a>Chamar pontos finais Web

1. Aceda ao comando **TurnOnOff**, selecione **ConfirmationResponse** na regra de conclusão e, em seguida, selecione **Adicionar uma ação**.
1. Em **Nova Ação-Tipo**, selecione **Chamar ponto final Web**
1. Em **Editar Ação – Pontos finais**, selecione **UpdateDeviceState**, que é o ponto final Web que criámos.  
1. Em **Configuração**, coloque os seguintes valores: 
   > [!div class="mx-imgBorder"]
   > ![Parâmetros de ação para chamar pontos finais Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Pontos Finais | UpdateDeviceState | O ponto final Web que pretende chamar nesta ação. |
   | Parâmetros de consulta | item={SubjectDevice}&&value={OnOff} | Os parâmetros da consulta a serem acrescentados ao URL do ponto final Web.  |
   | Conteúdo do corpo | N/D | O conteúdo do corpo do pedido. |

    > [!NOTE]
    > - Os parâmetros da consulta sugerida apenas são necessários para o ponto final de exemplo

1. Em **Em Caso de Êxito – Ação a executar**, selecione **Enviar resposta de voz**.
    
    No **Editor simples**, introduza `{SubjectDevice} is {OnOff}`.
   
   > [!div class="mx-imgBorder"]
   > ![Screenshot que mostra o On Success - Ação para executar o ecrã.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a executar | Enviar resposta de voz | Ação a executar se o pedido ao ponto final Web for realizado com êxito |
   
   > [!NOTE]
   > - Também pode aceder diretamente aos campos na resposta http com `{YourWebEndpointName.FieldName}`. Por exemplo: `{UpdateDeviceState.TV}`

1. Em **Em Caso de Falha – Ação a executar**, selecione **Enviar resposta de voz**

    No **Editor simples**, introduza `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Ação Chamar pontos finais Web Em Caso de Falha](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a executar | Enviar resposta de voz | Ação a executar se o pedido ao ponto final Web falhar |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` é opcional. Pode removê-lo se não quiser expor nenhuma mensagem de erro.
   > - No nosso ponto final de exemplo, enviámos a resposta http com mensagens de erro detalhadas para erros comuns, como parâmetros de cabeçalho em falta. 

### <a name="try-it-out-in-test-portal"></a>Experimente no portal de teste
- Resposta Em Caso de Êxito\
Guardar, preparar e testar
   > [!div class="mx-imgBorder"]
   > ![Screenshot que mostra a resposta On Success.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Resposta Em Caso de Falha\
Remover um dos parâmetros da consulta, guardar, treinar novamente e testar
   > [!div class="mx-imgBorder"]
   > ![Ação Chamar pontos finais Web Em Caso de Êxito](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrar com a aplicação cliente

Em [Procedimentos: Enviar a atividade para a aplicação cliente (Pré-visualização)](./how-to-custom-commands-send-activity-to-client.md), adicionou uma ação **Enviar atividade para o cliente**. A atividade é enviada para a aplicação cliente, independentemente de a ação **Chamar ponto final Web** ser realizada com êxito ou não.
No entanto, na maioria dos casos, vai querer apenas enviar atividade para a aplicação cliente quando a chamada para o ponto final Web é realizada com êxito. Neste exemplo, isto ocorre quando o estado do dispositivo é atualizado com êxito.

1. Elimine a ação **Enviar atividade para o cliente** que adicionou anteriormente.
1. Edite o ponto final Web da chamada: 
    1. Em **Configuração**, confirme que os **Parâmetros de Consulta** são `item={SubjectDevice}&&value={OnOff}`
    1. Em **Em Caso de Êxito**, altere **Ação a executar** para **Enviar atividade para o cliente**
    1. Copie o JSON abaixo para o **Conteúdo da Atividade**
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
   
Agora envia apenas atividade para o cliente quando o pedido ao ponto final Web é realizado com êxito.

### <a name="create-visuals-for-syncing-device-state"></a>Criar elementos visuais para sincronizar o estado do dispositivo
Adicione o seguinte XML a `MainPage.xaml` acima do bloco `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Sincronizar o estado do dispositivo 

Em `MainPage.xaml.cs`, adicione a referência `using Windows.Web.Http;`. Adicione o seguinte código à classe `MainPage`. Este método vai enviar um pedido GET para o ponto final de exemplo e extrair o estado atual do dispositivo para a sua aplicação. Confirme que altera `<your_app_name>` para o que utilizou no **cabeçalho** no ponto final Web de Comando Personalizado

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Experimente

1. Iniciar a aplicação
1. Clique em Sincronizar o Estado do Dispositivo.\
Se testou a aplicação com `turn on tv` na secção anterior, vai ver os programas da TV como "ativados".
    > [!div class="mx-imgBorder"]
    > ![Sincronizar o estado do dispositivo](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecione Ativar microfone
1. Selecione o botão Falar
1. Diga `turn on the fan`
1. O estado do elemento visual da ventoinha deve mudar para "ativado"
    > [!div class="mx-imgBorder"]
    > ![Ligar a ventoinha](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicação de Comandos Personalizados de Exportação como uma habilidade remota](./how-to-custom-commands-integrate-remote-skills.md)

