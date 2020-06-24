---
title: Configurar pontos finais web (Pré-visualização)
titleSuffix: Azure Cognitive Services
description: configurar pontos finais web para comandos personalizados
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5bdb77d27b01f576ca06aa5b6d3df0572b3b1ea6
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307745"
---
# <a name="set-up-web-endpoints"></a>Configurar pontos finais web

Neste artigo, aprende a configurar pontos finais web numa aplicação de Comandos Personalizados que lhe permitem fazer pedidos HTTP a partir de uma aplicação do cliente. Completa as seguintes tarefas:

- Configurar pontos finais web na aplicação Comandos Personalizados
- Ligue para pontos finais da web na aplicação de Comandos Personalizados
- Receba a resposta dos pontos finais da web 
- Integre a resposta dos pontos finais da web numa carga útil personalizada do JSON, envie-a e visualize-a a partir de uma aplicação de cliente SDK de discurso UWP UWP

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Uma chave de subscrição Azure para o serviço [Desematado: Obtenha uma gratuitamente](get-started.md) ou crie-a no [portal Azure](https://portal.azure.com)
> * Uma aplicação de [Comandos Personalizados](quickstart-custom-commands-application.md) previamente criada
> * Uma aplicação de cliente ativada pela SDK: [Como: fim da atividade à aplicação do cliente](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Configurar pontos finais web

1. Abra a aplicação Comandos Personalizados que criou anteriormente. 
1. Vá a "Web endpoints", clique em "Novo ponto final web".

   > [!div class="mx-imgBorder"]
   > ![Novo ponto final da web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Name | AtualizaçãoDeviceState | Nome para o ponto final da web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | O URL do ponto final com o que deseja que a sua aplicação de comando personalizada fale. |
   | Método | POST | As interações permitidas (como GET, POST) com o seu ponto final.|
   | Cabeçalhos | Chave: app, Valor: um nome único para a sua aplicação | Os parâmetros do cabeçalho para incluir no cabeçalho do pedido.|

    > [!NOTE]
    > - O ponto final da web exemplo criado usando [a Azure Function,](https://docs.microsoft.com/azure/azure-functions/)que se liga à base de dados que salva o estado do dispositivo da televisão e do ventilador
    > - O cabeçalho sugerido só é necessário para o ponto final exemplo
    > - No mundo real, o ponto final da web pode ser o ponto final para o [hub IOT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) que gere os seus dispositivos

1. Clique em **Guardar**.

## <a name="call-web-endpoints"></a>Ligue para os pontos finais da web

1. Vá ao comando **TurnOnOff,** selecione **ConfirmationResponse** sob a regra de conclusão e, em seguida, selecione **Adicionar uma ação**.
1. Sob **novo tipo de ação,** selecione Call web **endpoint**
1. In **Edit Action - Endpoints**, selecione **UpdateDeviceState**, que é o ponto final web que criamos.  
1. Na **Configuração**, coloque os seguintes valores: 
   > [!div class="mx-imgBorder"]
   > ![Ligue para os parâmetros de ação dos pontos finais da web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Pontos Finais | AtualizaçãoDeviceState | O ponto final da web que deseja chamar nesta ação. |
   | Parâmetros de consulta | item={SubjectDevice}&&valor={OnOff} | Os parâmetros de consulta para anexar ao URL do ponto final da web.  |
   | Conteúdo corporal | N/D | O conteúdo do corpo do pedido. |

    > [!NOTE]
    > - Os parâmetros de consulta sugeridos são apenas necessários para o ponto final exemplo

1. In **On Success - Ação a executar**, selecione Enviar resposta de **fala**.
   
   > [!div class="mx-imgBorder"]
   > ![Ligue para a ação de pontos finais da web sobre o sucesso](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a executar | Enviar resposta de discurso | Ação a executar se o pedido para o ponto final web for bem sucedido |
   
   > [!NOTE]
   > - Também pode aceder diretamente aos campos na resposta http utilizando `{YourWebEndpointName.FieldName}` . Por exemplo: `{UpdateDeviceState.TV}`

1. In **On Failure - Ação a executar,** selecione Enviar resposta de **fala**
   > [!div class="mx-imgBorder"]
   > ![Ligue para a ação de pontos finais da web em falha](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Definição | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a executar | Enviar resposta de discurso | Ação a executar se o pedido para o ponto final web falhar |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` é opcional. Pode removê-la se não quiser expor nenhuma mensagem de erro.
   > - Dentro do nosso ponto final de exemplo, enviamos de volta a resposta http com mensagens de erro detalhadas para erros comuns, tais como parâmetros de cabeçalho em falta. 

### <a name="try-it-out-in-test-portal"></a>Experimente no portal de testes
- Sobre a resposta ao sucesso\
Salvar, treinar e testar
   > [!div class="mx-imgBorder"]
   > ![Ligue para a ação de pontos finais da web sobre o sucesso](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Na resposta de falha\
Remova um dos parâmetros de consulta, guarde, retreine e teste
   > [!div class="mx-imgBorder"]
   > ![Ligue para a ação de pontos finais da web sobre o sucesso](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrar-se com a aplicação do cliente

Em [Como-fazer: Enviar atividade para a aplicação do cliente (Preview)](./how-to-custom-commands-send-activity-to-client.md), adicionou uma **atividade enviar à ação do cliente.** A atividade é enviada para a aplicação do cliente se a ação **de endpoint web de chamada** é ou não bem sucedida.
No entanto, na maioria dos casos, só pretende enviar atividade para a aplicação do cliente quando a chamada para o ponto final da web for bem sucedida. Neste exemplo, este é o momento em que o estado do dispositivo é atualizado com sucesso.

1. Elimine a **atividade Enviar para a ação** do cliente que anteriormente acrescentou.
1. Editar o ponto final da web de chamada: 
    1. Na **Configuração**, **certifique-se de que os parâmetros de consulta** são`item={SubjectDevice}&&value={OnOff}`
    1. In **On Success**, alterar **ação para executar** para Enviar atividade ao **cliente**
    1. Copie o JSON abaixo para o **Conteúdo da Atividade**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Enviar atividade sobre o sucesso](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Agora só envia atividade ao cliente quando o pedido para o web endpoint for bem sucedido.

### <a name="create-visuals-for-syncing-device-state"></a>Criar visuais para sincronizar o estado do dispositivo
Adicione o seguinte XML a `MainPage.xaml` cima do `"EnableMicrophoneButton"` bloco.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Estado do dispositivo de sincronização 

In `MainPage.xaml.cs` , adicione a referência `using Windows.Web.Http;` . Adicione o seguinte código à `MainPage` classe. Este método enviará um pedido GET para o ponto final do exemplo e extrairá o estado atual do dispositivo para a sua aplicação. Certifique-se de alterar `<your_app_name>` o que usou no **cabeçalho** no ponto final do Comando Personalizado Web

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
1. Clique em Sync Device State.\
Se testar a aplicação `turn on tv` na secção anterior, veria os programas de TV como "ligados".
    > [!div class="mx-imgBorder"]
    > ![Estado do dispositivo de sincronização](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecione Ative o microfone
1. Selecione o botão Falar
1. Dizer`turn on the fan`
1. O estado visual do ventilador deve mudar para "on"
    > [!div class="mx-imgBorder"]
    > ![Ligue o ventilador](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ativar um processo CI/CD para a sua aplicação de Comandos Personalizados](./how-to-custom-commands-deploy-cicd.md)