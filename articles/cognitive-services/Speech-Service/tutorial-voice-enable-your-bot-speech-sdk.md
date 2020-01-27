---
title: 'Tutorial: vozes habilite o bot usando o SDK de fala-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um bot de eco usando o Microsoft bot-Framework, o implantará no Azure e o registrará com o canal de fala de linha direta do bot-Framework. Em seguida, você configurará um aplicativo cliente de exemplo para o Windows que lhe permite falar com o bot e ouvi-lo responderá a você.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: dcohen
ms.openlocfilehash: 0c26f94d0a51b7912d3f964e3cc96ec392fec69b
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495175"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: habilitar o bot para voz usando o SDK de fala

Agora você pode usar o poder do serviço de fala para habilitar uma voz com facilidade para um bot de chat.

Neste tutorial, você criará um bot de eco usando o Microsoft bot-Framework, o implantará no Azure e o registrará com o canal de fala de linha direta do bot-Framework. Em seguida, você configurará um aplicativo cliente de exemplo para o Windows que lhe permite falar com o bot e ouvi-lo responderá a você.

Este tutorial destina-se a desenvolvedores que estão apenas começando sua jornada com o Azure, bots-estrutura de bot, fala de linha direta ou o SDK de fala, e querem criar rapidamente um sistema funcional com codificação limitada. Nenhuma experiência ou familiaridade com esses serviços é necessária.

No final deste exercício, você terá configurado um sistema que funcionará da seguinte maneira:

1. O aplicativo cliente de exemplo está configurado para se conectar ao canal de fala de linha direta e ao bot de eco
1. O áudio é registrado no microfone padrão no botão de pressionamento (ou registrado continuamente se a palavra-chave personalizada for ativada)
1. Opcionalmente, a detecção de palavra-chave personalizada acontece, a retenção de streaming de áudio para a nuvem
1. Usando o SDK de fala, o aplicativo se conecta ao canal de fala de linha direta e transmite áudio
1. Opcionalmente, a verificação de palavra-chave de precisão mais alta ocorre no serviço
1. O áudio é passado para o serviço de reconhecimento de fala e transcrita para texto
1. O texto reconhecido é passado para o Echo-bot como uma atividade de estrutura de bot 
1. O texto de resposta é transformado em áudio pelo serviço de conversão de texto em fala (TTS) e transmitido de volta para o aplicativo cliente para reprodução

![diagrama-marca](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "O fluxo do canal de fala")

> [!NOTE]
> As etapas neste tutorial não exigem um serviço pago. Como um novo usuário do Azure, você poderá usar créditos de sua assinatura de avaliação gratuita do Azure e a camada gratuita do serviço de fala para concluir este tutorial.

Eis o que este tutorial aborda:
> [!div class="checklist"]
> * Criar novos recursos do Azure
> * Criar, testar e implantar o exemplo de bot de eco em um serviço de Azure App
> * Registrar seu bot com canal de fala de linha direta
> * Compilar e executar o cliente de fala de linha direta para interagir com o bot de eco
> * Adicionar ativação de palavra-chave personalizada
> * Saiba como alterar o idioma da fala reconhecida e falada

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você precisará para concluir este tutorial:

- Um PC com Windows 10 com um microfone de trabalho e alto-falantes (ou fones de ouvido)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou superior
- [SDK do .NET Core](https://dotnet.microsoft.com/download) versão 2,1 ou posterior
- Uma conta do Azure. [Inscreva-se gratuitamente](https://azure.microsoft.com/free/ai/).
- Uma conta do [GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O aplicativo cliente que você criará neste tutorial usa alguns serviços do Azure. Para reduzir o tempo de ida e volta das respostas do bot, você desejará certificar-se de que esses serviços estejam localizados na mesma região do Azure. Nesta seção, você criará um grupo de recursos na região **oeste dos EUA** . Esse grupo de recursos será usado ao criar recursos individuais para o bot-Framework, o canal de fala de linha direta e o serviço de fala.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No painel de navegação esquerdo, selecione **grupos de recursos**. Em seguida, clique em **Adicionar** para adicionar um novo grupo de recursos.
1. Você será solicitado a fornecer algumas informações:
   * Defina a **assinatura** para **avaliação gratuita** (você também pode usar uma assinatura existente).
   * Insira um nome para seu **grupo de recursos**. É recomendável **SpeechEchoBotTutorial-resourcegroup**.
   * Na lista suspensa **região** , selecione **oeste dos EUA**.
1. Clique em **revisar e criar**. Você deve ver uma faixa que a **validação**de leitura passou.
1. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.
1. Assim como nos recursos que você criará posteriormente neste tutorial, é uma boa ideia fixar esse grupo de recursos em seu painel para facilitar o acesso. Se você quiser fixar esse grupo de recursos, clique no ícone de pino no canto superior direito do painel.

### <a name="choosing-an-azure-region"></a>Escolhendo uma região do Azure

Se você quiser usar uma região diferente para este tutorial, esses fatores podem limitar suas opções:

* Certifique-se de usar uma [região do Azure com suporte](regions.md#voice-assistants).
* O canal de fala de linha direta usa o serviço de conversão de texto em fala, que tem vozes padrão e neural. As vozes neurais são [limitadas a regiões específicas do Azure](regions.md#standard-and-neural-voices).
* As chaves de avaliação gratuitas podem ser restritas a uma região específica.

Para obter mais informações sobre regiões, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Criar recursos

Agora que você tem um grupo de recursos na região **oeste dos EUA** , a próxima etapa é criar recursos individuais para cada serviço que você usará neste tutorial.

### <a name="create-a-speech-service-resource"></a>Criar um recurso de serviço de fala

Siga estas instruções para criar um recurso de fala:

1. Vá para a [portal do Azure](https://portal.azure.com) e selecione **criar um recurso** no painel de navegação esquerdo.
2. Na barra de pesquisa, digite **fala**.
3. Selecione **fala**e clique em **criar**.
4. Você será solicitado a fornecer algumas informações:
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-Speech**
   * Para **assinatura**, verifique se a **avaliação gratuita** está selecionada.
   * Para **local**, selecione **oeste dos EUA**.
   * Para **tipo de preço**, selecione **F0**. Essa é a camada gratuita.
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**.
5. Depois de inserir todas as informações necessárias, clique em **criar**. Pode levar alguns minutos para criar seu recurso.
6. Posteriormente neste tutorial, você precisará de chaves de assinatura para esse serviço. Você pode acessar essas chaves a qualquer momento da **visão geral** do recurso (gerenciar chaves) ou **chaves**.

Neste ponto, verifique se seu grupo de recursos (**SpeechEchoBotTutorial-resourcegroup**) tem um recurso de fala:

| NAME | TIPO  | LOCALIZAÇÃO |
|------|-------|----------|
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | E.U.A. Oeste |

### <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

A próxima etapa é criar um plano do serviço de aplicativo. Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web.

1. Vá para a [portal do Azure](https://portal.azure.com) e selecione **criar um recurso** no painel de navegação esquerdo.
2. Na barra de pesquisa, digite **plano do serviço de aplicativo**. Em seguida, localize e selecione o cartão do **plano do serviço de aplicativo** nos resultados da pesquisa.
3. Clique em **Criar**.
4. Você será solicitado a fornecer algumas informações:
   * Defina a **assinatura** para **avaliação gratuita** (você também pode usar uma assinatura existente).
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**.
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-AppServicePlan**
   * Para **sistema operacional**, selecione **Windows**.
   * Para **região**, selecione **oeste dos EUA**.
   * Para **tipo de preço**, verifique se **padrão S1** está selecionado. Esse deve ser o valor padrão. Se não estiver, certifique-se de definir o **sistema operacional** para o **Windows** , conforme descrito acima.
5. Clique em **revisar e criar**. Você deve ver uma faixa que a **validação**de leitura passou.
6. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.

Neste ponto, verifique se seu grupo de recursos (**SpeechEchoBotTutorial-resourcegroup**) tem dois recursos:

| NAME | TIPO  | LOCALIZAÇÃO |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | E.U.A. Oeste |

## <a name="build-an-echo-bot"></a>Criar um bot de eco

Agora que você criou alguns recursos, vamos criar um bot. Vamos começar com o exemplo de bot de eco, que como o nome indica, ecoa o texto que você inseriu como resposta. Não se preocupe, o código de exemplo está pronto para uso sem nenhuma alteração. Ele está configurado para trabalhar com o canal de fala de linha direta, que vamos conectar depois de implantarmos o bot no Azure.

> [!NOTE]
> As instruções a seguir, bem como informações adicionais sobre o bot de eco, estão disponíveis no [Leiame do exemplo no GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Executar o exemplo de bot em seu computador

1. Clone o repositório de exemplos:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Inicie o Visual Studio.
3. Na barra de ferramentas, selecione **arquivo** > **abrir** > **projeto/solução**e abra a solução de projeto de bot de eco:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Depois que o projeto for carregado, pressione `F5` para compilar e executar o projeto.

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testar o exemplo de bot com o emulador do bot Framework

O [emulador do bot Framework](https://github.com/microsoft/botframework-emulator) é um aplicativo de desktop que permite que os desenvolvedores de bot testem e depurem seus bots localmente ou remotamente por meio de um túnel. O emulador dá suporte ao texto digitado como entrada (não de voz). O bot responderá com o texto. Siga estas etapas para usar o emulador do bot Framework para testar seu bot de eco em execução localmente, com entrada de texto e saída de texto. Depois de implantarmos o bot Azure, vamos testá-lo com entrada de voz e saída de voz.

1. Instalar o [emulador do bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versão 4.3.0 ou superior
2. Inicie o emulador do bot Framework e abra o bot:
   * **Arquivo** -> o **bot aberto**.
3. Insira a URL para o bot. Por exemplo:

   ```
   http://localhost:3978/api/messages
   ```
   e pressione "conectar".
4. O bot deve lhe saudar imediatamente com "Olá e bem-vindo!" . Digite qualquer mensagem de texto e confirme que você obtém uma resposta do bot.

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Implantar o bot em um serviço de Azure App

A próxima etapa é implantar o Echo bot no Azure. Há algumas maneiras de implantar um bot, mas neste tutorial vamos nos concentrar na publicação diretamente do Visual Studio.

> [!NOTE]
> Como alternativa, você pode implantar um bot usando os modelos de [CLI do Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) e de [implantação](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. No Visual Studio, abra o bot de eco que foi configurado para uso com canal de fala de linha direta:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **EchoBot** e selecione **publicar...**
1. Uma nova janela intitulada **escolher um destino de publicação** será aberta.
1. Selecione **serviço de aplicativo** no painel de navegação esquerdo, selecione **criar novo**e clique em **publicar**.
1. Quando a janela **Criar serviço de aplicativo** for exibida:
   * Clique em **Adicionar uma conta**e entre com suas credenciais de conta do Azure. Se você já tiver entrado, selecione a conta desejada na lista suspensa.
   * Para o **nome do aplicativo**, você precisará inserir um nome globalmente exclusivo para o bot. Esse nome é usado para criar uma URL de bot exclusiva. Um valor padrão será preenchido, incluindo a data e a hora (por exemplo: "EchoBot20190805125647"). Você pode usar o nome padrão para este tutorial.
   * Para **assinatura**, defina-a como **avaliação gratuita**
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**
   * Para o **plano de hospedagem**, selecione **SpeechEchoBotTutorial-AppServicePlan**
1. Clique em **Criar**.
1. Você deverá ver uma mensagem de êxito no Visual Studio parecida com esta:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. O navegador padrão deve abrir e exibir uma página que leia: "o bot está pronto!".
1. Neste ponto, verifique o grupo de recursos **SpeechEchoBotTutorial-resourcegroup** no portal do Azure e confirme se há três recursos:

| NAME | TIPO  | LOCALIZAÇÃO |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-AppServicePlan | Plano do App Service | E.U.A. Oeste |
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | E.U.A. Oeste |

## <a name="enable-web-sockets"></a>Habilitar soquetes Web

Você precisará fazer uma pequena alteração de configuração para que o bot possa se comunicar com o canal de fala de linha direta usando os soquetes da Web. Siga estas etapas para habilitar o Web Sockets:

1. Navegue até a [portal do Azure](https://portal.azure.com)e localize seu serviço de aplicativo. O recurso deve ser nomeado semelhante a **EchoBot20190805125647** (seu nome de aplicativo exclusivo).
2. No painel de navegação esquerdo, em **configurações**, clique em **configuração**.
3. Selecione a guia **configurações gerais** .
4. Localize a alternância para **Web Sockets** e defina-a como **ativado**.
5. Clique em **Guardar**.

> [!TIP]
> Você pode usar os controles na parte superior da página Azure App serviço para parar ou reiniciar o serviço. Isso pode ser útil ao solucionar problemas.

## <a name="create-a-channel-registration"></a>Criar um registro de canal

Agora que você criou um serviço de Azure App para hospedar o bot, a próxima etapa é criar um **registro de canais de bot**. A criação de um registro de canal é um pré-requisito para registrar seu bot com canais de bot-Framework, incluindo canal de fala de linha direta.

> [!NOTE]
> Se você quiser saber mais sobre como os bots utilizam canais, consulte [conectar um bot a canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. A primeira etapa é criar um novo recurso para o registro. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. No **bot**do tipo de barra de pesquisa, depois que os resultados forem exibidos, selecione o **registro de canais de bot**.
3. Clique em **Criar**.
4. Você será solicitado a fornecer algumas informações:
   * Para o **identificador de bot**, digite **SpeechEchoBotTutorial-BotRegistration**.
   * Para **assinatura**, selecione **avaliação gratuita**.
   * Para **grupo de recursos**, selecione **SpeechEchoBotTutorial-resourcegroup**.
   * Para **local**, selecione **oeste dos EUA**.
     * Para **tipo de preço**, selecione **F0**.
     * Para **ponto de extremidade do sistema de mensagens**, insira a URL para seu aplicativo Web com o caminho `/api/messages` acrescentado no final. Por exemplo: se o nome do aplicativo exclusivo globalmente era **EchoBot20190805125647**, o ponto de extremidade do sistema de mensagens seria: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Para o **Application insights**, você pode definir isso como **desativado**. Para obter mais informações, consulte [bot Analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignorar **criação automática de ID do aplicativo e senha**.
5. Na parte inferior da folha de **registro de canais de bot** , clique em **criar**.

Neste ponto, verifique o grupo de recursos **SpeechEchoBotTutorial-resourcegroup** no portal do Azure. Agora, ele deve mostrar quatro recursos:

| NAME | TIPO  | LOCALIZAÇÃO |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-AppServicePlan | Plano do App Service | E.U.A. Oeste |
| SpeechEchoBotTutorial-BotRegistration | Bot Channels Registration | Global |
| SpeechEchoBotTutorial-fala | Serviços Cognitivos | E.U.A. Oeste |

> [!IMPORTANT]
> O recurso de registro de canais de bot mostrará a região global, embora você tenha selecionado oeste dos EUA. Isto era esperado.

## <a name="register-the-direct-line-speech-channel"></a>Registrar o canal de fala de linha direta

Agora é hora de registrar seu bot com o canal de fala de linha direta. Esse canal é o que é usado para criar uma conexão entre o bot de eco e um aplicativo cliente compilado com o SDK de fala.

1. Localize e abra o recurso **SpeechEchoBotTutorial-BotRegistration** no [portal do Azure](https://portal.azure.com).
1. No painel de navegação esquerdo, selecione **canais**.
   * Procure **mais canais**, localize e clique em **Direct line Speech**.
   * Examine o texto na página intitulado configurar a **Direct line Speech**e, em seguida, expanda o menu suspenso chamado "conta de serviço cognitiva".
   * Selecione o recurso de fala que você criou anteriormente (por exemplo, **SpeechEchoBotTutorial-Speech**) no menu para associar o bot à sua chave de assinatura de fala.
   * Clique em **Guardar**.

1. No painel de navegação esquerdo, clique em **configurações**.
   * Marque a caixa rotulada **habilitar ponto de extremidade de streaming**. Isso é necessário para habilitar um protocolo de comunicação criado em soquetes da Web entre o bot e o canal de fala de linha direta.
   * Clique em **Guardar**.

> [!TIP]
> Se você quiser saber mais, veja [conectar um bot à Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Esta página inclui informações adicionais e problemas conhecidos.

## <a name="build-the-direct-line-speech-client"></a>Criar o cliente de fala de linha direta

Nesta etapa, você vai criar o cliente Direct line Speech. O cliente é um aplicativo Windows Presentation Foundation (WPF) no C# que usa o [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) para gerenciar a comunicação com o bot usando o canal de fala de linha direta. Use-o para interagir com e testar o bot antes de gravar um aplicativo cliente personalizado.

O cliente Direct line Speech tem uma interface do usuário simples que permite configurar a conexão com o bot, exibir a conversa de texto, exibir as atividades de bot-Framework no formato JSON e exibir cartões adaptáveis. Ele também dá suporte ao uso de palavras-chave personalizadas. Você usará esse cliente para falar com o bot e receber uma resposta de voz.

Antes de continuarmos, verifique se o microfone e os alto-falantes estão habilitados e funcionando.

1. Navegue até o repositório GitHub para o [cliente Direct line Speech](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Siga as instruções fornecidas para clonar o repositório, compilar o projeto, configurar o cliente e iniciar o cliente.
3. Clique em **reconectar** e verifique se você vê a mensagem **pressione o botão MIC ou digite para começar a conversar com o bot**.
4. Vamos testá-lo. Clique no botão do microfone e fale com algumas palavras em inglês. O texto reconhecido será exibido à medida que você falar. Quando você terminar de falar, o bot responderá em sua própria voz, dizendo "Echo" seguido das palavras reconhecidas.
5. Você também pode usar texto para se comunicar com o bot. Basta digitar o texto na barra inferior. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Solucionando problemas de erros no cliente Direct line Speech

Se você receber uma mensagem de erro na janela do aplicativo principal, use esta tabela para identificar e solucionar o erro:

| Erro | O que deve fazer? |
|-------|----------------------|
|Erro AuthenticationFailure: falha na atualização do WebSocket com um erro de autenticação (401). Verificar a chave de assinatura (ou o token de autorização) correta e o nome da região| Na página Configurações do aplicativo, verifique se você inseriu a chave de assinatura de fala e sua região corretamente.<br>Certifique-se de que sua chave de fala e região de chave foram inseridas corretamente. |
|Erro ConnectionFailure: a conexão foi fechada pelo host remoto. Código de erro: 1011. Detalhes do erro: não foi possível conectar ao bot antes de enviar uma mensagem | Verifique se você [marcou a caixa "Habilitar ponto de extremidade de streaming"](#register-the-direct-line-speech-channel) e/ou os [ **soquetes da Web** alternados](#enable-web-sockets) para ativado.<br>Verifique se o serviço de Azure App está em execução. Se for, tente reiniciar o serviço de aplicativo.|
|Erro ConnectionFailure: a conexão foi fechada pelo host remoto. Código de erro: 1011. Detalhes do erro: o código de status de resposta não indica êxito: 500 (InternalServerError)| O bot especificou uma voz neural em seu campo [ler](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) atividade de saída, mas a região do Azure associada à sua chave de assinatura de fala não oferece suporte a vozes neurais. Consulte [vozes padrão e neural](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Erro ConnectionFailure: a conexão foi fechada pelo host remoto. Código de erro: 1000. Detalhes do erro: tempo ocioso máximo de conexão de soquete da Web excedido (> 300000 MS)| Esse é um erro esperado quando uma conexão com o canal é deixada aberta e inativa por mais de cinco minutos. |

Se o problema não for resolvido na tabela, consulte [assistentes de voz: perguntas](faq-voice-assistants.md)frequentes.

### <a name="view-bot-activities"></a>Exibir atividades de bot

Cada bot envia e recebe mensagens de **atividade** . Na janela **log de atividades** do cliente Direct line Speech, você verá os logs com carimbo de data/hora com cada atividade que o cliente recebeu do bot. Você também pode ver as atividades que o cliente enviou para o bot usando o método [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) . Quando você seleciona um item de log, ele mostrará os detalhes da atividade associada como JSON.

Aqui está um exemplo de JSON de uma atividade recebida pelo cliente:
```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Para saber mais sobre o que é retornado na saída JSON, consulte [campos na atividade](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Para fins deste tutorial, você pode se concentrar nos campos de [texto](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e de [fala](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) .

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Exibir o código-fonte do cliente para chamadas para o SDK de fala

O cliente Direct line Speech usa o pacote NuGet [Microsoft. cognitivaservices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contém o SDK de fala. Um bom lugar para começar a examinar o código de exemplo é o método InitSpeechConnector () no arquivo [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), que cria esses dois objetos SDK de fala:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -para definições de configuração (por exemplo, chave de assinatura de fala, região de chave)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) -gerenciar os eventos de conexão de canal e de assinatura de cliente para tratamento de respostas reconhecidas de fala e bot.

## <a name="add-custom-keyword-activation"></a>Adicionar ativação de palavra-chave personalizada

O SDK de fala dá suporte à ativação de palavra-chave personalizada. Semelhante a "Ei Cortana" para o assistente da Microsoft, você pode escrever um aplicativo que irá escutar continuamente por uma palavra-chave de sua escolha. Tenha em mente que uma palavra-chave pode ser uma palavra única ou uma frase com várias palavras.

> [!NOTE]
> A *palavra-chave* Term muitas vezes é usada de forma intercambiável com o termo *Wake Word*, e você pode ver ambos usados na documentação da Microsoft.

A detecção de palavra-chave é feita no aplicativo cliente. Se estiver usando uma palavra-chave, o áudio será transmitido somente para o canal de fala de linha direta se a palavra-chave for detectada. O canal de fala de linha direta inclui um componente chamado *KWV (verificação de palavra-chave)* , que faz processamento mais complexo na nuvem para verificar se a palavra-chave escolhida está no início do fluxo de áudio. Se a verificação da palavra-chave for realizada com sucesso, o canal se comunicará com o bot.

Siga estas etapas para criar um modelo de palavra-chave, configurar o cliente de fala de linha direta para usar esse modelo e, por fim, testá-lo com o bot.

1. Siga estas instruções para [criar uma palavra-chave personalizada usando o serviço de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Descompacte o arquivo de modelo que você baixou na etapa anterior. Ele deve ser nomeado para sua palavra-chave. Você está procurando um arquivo chamado `kws.table`.
3. No cliente Direct line Speech, localize o menu **configurações** (procure o ícone de engrenagem no canto superior direito). Localize o **caminho do arquivo de modelo** e insira o nome do caminho completo para o arquivo de `kws.table` da etapa 2.
4. Certifique-se de marcar a caixa denominada **habilitado**. Você deverá ver essa mensagem ao lado da caixa de seleção: "escutará a palavra-chave na próxima conexão". Se você tiver fornecido o arquivo errado ou um caminho inválido, verá uma mensagem de erro.
5. Insira sua **chave de assinatura**de fala, região de **chave de assinatura**e clique em **OK** para fechar o menu **configurações** .
6. Clique em **reconectar**. Você deverá ver uma mensagem que lê: "nova conversa iniciada-tipo, pressione o botão do microfone ou diga a palavra-chave". Agora, o aplicativo está ouvindo continuamente.
7. Fale qualquer frase que comece com sua palavra-chave. Por exemplo: " **{Your palavra-chave}** , qual é a hora?". Você não precisa fazer uma pausa após a desabsolutação da palavra-chave. Quando terminar, duas coisas acontecem:
   * Você verá uma transcrição do que falou
   * Logo após, você ouvirá a resposta do bot
8. Continue experimentando os três tipos de entrada aos quais o bot dá suporte:
   * Texto digitado na barra inferior
   * Pressionando o ícone de microfone e falando
   * Dizendo uma frase que começa com sua palavra-chave

### <a name="view-the-source-code-that-enables-keyword"></a>Exibir o código-fonte que habilita a palavra-chave

No código-fonte do cliente Direct line Speech, dê uma olhada nesses arquivos para examinar o código usado para habilitar a detecção de palavras-chave:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) inclui uma chamada para o método SDK de fala [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-), que é usado para instanciar o modelo de um arquivo local no disco.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) inclui uma chamada para o método SDK de fala [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync), que ativa a detecção de palavra-chave contínua.

## <a name="optional-change-the-language-and-bot-voice"></a>Adicional Alterar o idioma e o bot Voice

O bot que você criou ouvirá e responderá em inglês, com uma voz de texto em fala inglês americano padrão. No entanto, você não está limitado a usar o inglês ou uma voz padrão. Nesta seção, você aprenderá a alterar o idioma que o bot escutará e responderá. Você também aprenderá a selecionar uma voz diferente para esse idioma.

### <a name="change-the-language"></a>Alterar o idioma

Você pode escolher entre qualquer um dos idiomas mencionados na tabela de [conversão de fala em texto](language-support.md#speech-to-text) . No exemplo a seguir, iremos alterar o idioma para alemão.

1. Abra o aplicativo cliente Direct line Speech, clique no botão Configurações (ícone de engrenagem superior direito) e insira `de-de` no campo idioma (esse é o valor de localidade mencionado na tabela de [fala para texto](language-support.md#speech-to-text) ). Isso define o idioma falado a ser reconhecido, substituindo o `en-us`padrão. Isso também instrui o canal de fala de linha direta a usar uma voz alemã padrão para a resposta do bot.
2. Feche a página Configurações e clique no botão Reconectar para estabelecer uma nova conexão com o seu bot de eco.
3. Clique no botão do microfone e diga uma frase em alemão. Você verá o texto reconhecido e o bot de eco respondendo com a voz alemã padrão.


### <a name="change-the-default-bot-voice"></a>Alterar a voz do bot padrão

A seleção da voz de conversão de texto em fala e do controle da pronúncia pode ser feita se o bot especificar a resposta na forma de uma [linguagem de marcação de síntese de fala](speech-synthesis-markup.md) (SSML) em vez de texto simples. O bot de eco não usa SSML, mas podemos modificar facilmente o código para fazer isso. No exemplo abaixo, adicionamos SSML à resposta do bot de eco, de modo que a voz alemã Rodrigo Apollo (uma voz masculina) será usada em vez da voz fêmea padrão. Consulte a lista de [vozes padrão](language-support.md#standard-voices) e [vozes neurais](language-support.md#neural-voices) com suporte para seu idioma.

1. Vamos começar abrindo `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Localize estas duas linhas:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Substituir por:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Crie sua solução no Visual Studio e corrija quaisquer erros de compilação.

O segundo argumento no método ' MessageFactory. Text ' define o [campo de fala de atividade](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) na resposta do bot. Com a alteração acima, ele foi substituído de texto simples para SSML a fim de especificar uma voz alemã não padrão.

### <a name="redeploy-your-bot"></a>Reimplantar o bot

Agora que você fez a alteração necessária no bot, a próxima etapa é republicá-la em seu serviço de Azure App e experimentá-la:

1. Na janela Gerenciador de Soluções, clique com o botão direito do mouse no projeto **EchoBot** e selecione **publicar**.
2. Sua configuração de implantação anterior já foi carregada como padrão. Basta clicar em **publicar** ao lado de **EchoBot20190805125647-implantação da Web**.
3. A mensagem **publicar com êxito** aparecerá na janela saída do Visual Studio e uma página da Web será iniciada com a mensagem "o bot está pronto!".
4. Abra o aplicativo cliente Direct line Speech, clique no botão Configurações (ícone de engrenagem superior direito) e verifique se você ainda tem `de-de` no campo idioma.
5. Siga as instruções em [criar o cliente de fala de linha direta](#build-the-direct-line-speech-client) para se reconectar com o bot implantado recentemente, fale na nova linguagem e ouça a resposta do bot nesse idioma com a nova voz.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando o Echo-bot implantado neste tutorial, poderá removê-lo e todos os seus recursos do Azure associados simplesmente excluindo o grupo de recursos do Azure **SpeechEchoBotTutorial-resourcegroup**.

1. No [portal do Azure](https://portal.azure.com), clique em **grupos de recursos** no painel de navegação esquerdo.
2. Localize o grupo de recursos denominado: **SpeechEchoBotTutorial-resourcegroup**. Clique nos três pontos (...).
3. Selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie seu próprio aplicativo cliente com o SDK de fala](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Ver também

* Implantando em uma [região do Azure perto de você](https://azure.microsoft.com/global-infrastructure/locations/) para ver o aperfeiçoamento do tempo de resposta do bot
* Implantando em uma [região do Azure que dá suporte a vozes de TTS de alta qualidade](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Preço associado ao canal de fala de linha direta:
  * [Preços do serviço bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Criando e implantando seu próprio bot habilitado para voz:
  * Crie um [bot de estrutura de bot](https://dev.botframework.com/). Registre-o com o [canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) e [Personalize seu bot para voz](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Explore as [soluções de bot-Framework](https://microsoft.github.io/botframework-solutions/index)existentes: Crie um [assistente virtual](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) e [estenda-o para a Direct line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
