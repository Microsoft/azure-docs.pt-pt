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
ms.openlocfilehash: 95f27827950c5ed38caa1f83ede266afb57a1697
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515639"
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

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Implementar um ponto final externo da web usando a App de Função Azure

* Para o bem deste tutorial, precisa de um ponto final HTTP que mantenha os estados para todos os dispositivos que configura no comando **TurnOnOff** da sua aplicação de comandos personalizados.

* Se já tiver um ponto final web que deseja ligar, salte para a [secção seguinte](#setup-web-endpoints-in-custom-commands). Em alternativa, na secção seguinte, fornecemos-lhe um ponto final web hospedado predefinido que pode utilizar se quiser saltar esta secção.

### <a name="input-format-of-azure-function"></a>Formato de entrada da Função Azure
* Em seguida, irá implantar um ponto final utilizando [funções Azure](../../azure-functions/index.yml).
Segue-se o formato geral de um evento de Comandos Personalizados que é passado para a sua função Azure. Utilize esta informação quando estiver a escrever a sua aplicação de função.

    ```json
    {
      "conversationId": "string",
      "currentCommand": {
        "name": "string",
        "parameters": {
          "SomeParameterName": "string",
          "SomeOtherParameterName": "string"
        }
      },
      "currentGlobalParameters": {
          "SomeGlobalParameterName": "string",
          "SomeOtherGlobalParameterName": "string"
      }
    }
    ```

    
* Vamos rever os principais atributos desta entrada:
        
    | Atributo | Explicação |
    | ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
    | **conversationId** | O identificador único da conversa. Note que este ID pode ser gerado a partir da aplicação do cliente. |
    | **atualCommand** | O comando que está ativo na conversa. |
    | **nome** | O nome do comando. O `parameters` atributo é um mapa com os valores atuais dos parâmetros. |
    | **actuaisParametrosGlobal** | Um mapa `parameters` como, mas usado para parâmetros globais. |


* Para a **Função Azure DeviceState,** um evento de comandos personalizados de exemplo será o seguinte. Isto funcionará como uma **entrada** para a aplicação de função.
    
    ```json
    {
      "conversationId": "someConversationId",
      "currentCommand": {
        "name": "TurnOnOff",
        "parameters": {
          "item": "tv",
          "value": "on"
        }
      }
    }
    ```

### <a name="output-format-of-azure-function"></a>Formato de saída da Função Azure

#### <a name="output-consumed-by-a-custom-commands--application"></a>Saída consumida por uma aplicação de Comandos Personalizados
Neste caso, pode definir o formato de saída deve aderir ao seguinte formato. Siga [a Atualização de um comando a partir de um ponto final da web](./how-to-custom-commands-update-command-from-web-endpoint.md) para mais detalhes.

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

#### <a name="output-consumed-by-a-client-application"></a>Produção consumida por uma aplicação do cliente
Neste caso, pode definir o formato de saída de acordo com as necessidades do seu cliente.
* Para o nosso ponto final **deviceState,** a saída da função Azure é consumida por uma aplicação do cliente em vez da aplicação Comandos Personalizados. Exemplo de **saída** da função Azure deve gostar de seguir:
    
    ```json
    {
      "TV": "on",
      "Fan": "off"
    }
    ``` 

*  Além disso, esta saída deve ser escrita para um armazenamento externo, para que possa manter o estado dos dispositivos. O estado de armazenamento externo será utilizado na [secção Integração com a secção de aplicação do cliente.](#integrate-with-client-application)


### <a name="host-azure-function"></a>Função Azure anfitrião

1. Crie uma conta de armazenamento de mesa para guardar o estado do dispositivo.
    1. Vá ao portal Azure e crie um novo recurso de conta de **armazenamento** tipo por **nome devicestate**.
        1. Copie o valor da **cadeia de ligação** a partir das **teclas de acesso do estado do dispositivo ->**.
        1. Terá de adicionar este string ao código de aplicação de função de amostra descarregada.
    1. Descarregue o [código de aplicação de função da amostra](https://aka.ms/speech/cc-function-app-sample).
    1. Abra a solução descarregada em VS 2019. EmConnections.js **de** ficheiros, substitua **o valor STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** para o segredo copiado do passo *a*.
1.  Descarregue o código **DeviceStateAzureFunction.**
1. [Implementar](../../azure-functions/index.yml) a App Funções para Azure.
    
    1.  Aguarde que a implementação tenha sucesso e vá buscar o recurso implantado no portal Azure. 
    1. Selecione **Funções** no painel esquerdo e, em seguida, selecione **DeviceState**.
    1.  Na nova janela, selecione **Código + Teste** e, em seguida, selecione Obter URL de **função**.
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Configurar pontos finais web em comandos personalizados
Vamos ligar a função Azure com a aplicação de Comandos Personalizados existente.
Nesta secção, utilizará um ponto final **do DeviceState** existente. Se criou o seu próprio ponto web utilizando a Função Azure ou não, utilize-a em vez do padrão https://webendpointexample.azurewebsites.net/api/DeviceState .

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
    > - O ponto final da web exemplo criado usando [a função Azure,](../../azure-functions/index.yml)que se liga à base de dados que salva o estado do dispositivo da televisão e do ventilador
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

Em [Como-a: Enviar atividade para a aplicação do cliente,](./how-to-custom-commands-send-activity-to-client.md)adicionou uma **atividade enviar à ação do cliente.** A atividade é enviada para a aplicação cliente, independentemente de a ação **Chamar ponto final Web** ser realizada com êxito ou não.
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

