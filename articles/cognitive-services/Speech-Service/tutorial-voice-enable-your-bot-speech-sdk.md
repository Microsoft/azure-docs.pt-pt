---
title: 'Tutorial: Vozes permitem o seu bot usando O SDK de Fala - Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai criar um Echo Bot usando o Microsoft Bot-Framework, implantá-lo para O Azure, e registrá-lo com o canal bot-framework direct line speech. Em seguida, configurará uma aplicação de cliente de amostra para windows que lhe permite falar com o seu bot e ouvi-lo responder-lhe.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: dapine
ms.openlocfilehash: 6b037ced7acb94340214ce401ffee9d940312de8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562646"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: Ativar a voz do seu bot usando o SDK da Fala

Agora pode usar o poder do serviço De Fala para ativar facilmente um bot de chat.

Neste tutorial, você vai criar um Echo Bot usando o Microsoft Bot-Framework, implantá-lo para O Azure, e registrá-lo com o canal bot-framework direct line speech. Em seguida, configurará uma aplicação de cliente de amostra para windows que lhe permite falar com o seu bot e ouvi-lo responder-lhe.

Este tutorial é projetado para desenvolvedores que estão apenas começando a sua jornada com Azure, bots bot-framework, Direct Line Speech, ou o Speech SDK, e querem construir rapidamente um sistema de trabalho com codificação limitada. Não é necessária experiência ou familiaridade com estes serviços.

No final deste exercício, terá criado um sistema que funcionará da seguinte forma:

1. A aplicação do cliente da amostra está configurada para ligar ao canal Direct Line Speech e ao Echo Bot
1. O áudio é gravado a partir do microfone predefinido na pressão do botão (ou continuamente gravado se a palavra-chave personalizada for ativada)
1. Opcionalmente, a deteção de palavras-chave personalizadas acontece, agando o streaming de áudio para a nuvem
1. Utilizando o Speech SDK, a aplicação conecta-se ao canal Direct Line Speech e transmite áudio
1. Opcionalmente, verificação de palavras-chave de maior precisão acontece no serviço
1. O áudio é passado para o serviço de reconhecimento de voz e transcrito para texto
1. O texto reconhecido é passado para o Echo-Bot como uma Atividade-Quadro Bot 
1. O texto de resposta é transformado em áudio pelo serviço Text-to-Speech (TTS) e transmitido de volta para a aplicação do cliente para reprodução

![diagrama-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "O fluxo do Canal da Fala")

> [!NOTE]
> Os passos neste tutorial não requerem um serviço remunerado. Como novo utilizador do Azure, poderá utilizar créditos da sua subscrição gratuita de teste Azure e do nível gratuito do serviço Speech para completar este tutorial.

Aqui está o que este tutorial cobre:
> [!div class="checklist"]
> * Criar novos recursos Azure
> * Construir, testar e implementar a amostra Echo Bot para um Serviço de Aplicações Azure
> * Registe o seu bot com canal direct line Speech
> * Construa e execute o Cliente de Discurso de Linha Direta para interagir com o seu Echo Bot
> * Adicionar ativação de palavras-chave personalizadas
> * Aprenda a mudar a linguagem do discurso reconhecido e falado

## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que precisa para completar este tutorial:

- Um PC Windows 10 com microfone e altifalantes de trabalho (ou auscultadores)
- [Estúdio Visual 2017](https://visualstudio.microsoft.com/downloads/) ou superior
- [.NET Core SDK](https://dotnet.microsoft.com/download) versão 2.1 ou posterior
- Uma conta do Azure. [Inscreva-se gratuitamente.](https://azure.microsoft.com/free/ai/)
- Uma conta [GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

A aplicação de clientes que irá criar neste tutorial utiliza um punhado de serviços Azure. Para reduzir o tempo de ida e volta para respostas do seu bot, você vai querer ter certeza de que estes serviços estão localizados na mesma região de Azure. Nesta secção, você vai criar um grupo de recursos na região **oeste dos EUA.** Este grupo de recursos será utilizado na criação de recursos individuais para o Bot-Framework, o canal Direct Line Speech e o serviço speech.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Criar um grupo de recursos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Ser-lhe-á solicitado que forneça algumas informações:
   * Definir **Subscrição** para **Teste Gratuito** (também pode utilizar uma subscrição existente).
   * Insira um nome para o seu **grupo Derecursos**. Recomendamos **o SpeechEchoBotTutorial-ResourceGroup**.
   * Da **região,** selecione **Oeste dos EUA.**
1. Clique em **Rever e criar**. Devia ver um banner que dizia **validação.**
1. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.
1. Tal como acontece com os recursos que irá criar mais tarde neste tutorial, é uma boa ideia fixar este grupo de recursos no seu painel de instrumentos para facilitar o acesso. Se quiser fixar este grupo de recursos, clique no ícone pin o canto superior direito do painel de instrumentos.

### <a name="choosing-an-azure-region"></a>Escolher uma região de Azure

Se quiser usar uma região diferente para este tutorial, estes fatores podem limitar as suas escolhas:

* Certifique-se de que utiliza uma [região Azure apoiada.](regions.md#voice-assistants)
* O canal Direct Line Speech utiliza o serviço de texto-a-fala, que tem vozes padrão e neurais. As vozes neurais [limitam-se a regiões específicas do Azure.](regions.md#standard-and-neural-voices)
* As chaves de ensaio gratuitas podem ser restritas a uma região específica.

Para mais informações sobre as regiões, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="create-resources"></a>Criar recursos

Agora que tem um grupo de recursos numa região apoiada, o próximo passo é criar recursos individuais para cada serviço que utilizará neste tutorial.

### <a name="create-a-speech-service-resource"></a>Criar um recurso de serviço da Fala

Siga estas instruções para criar um recurso da Fala:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de serviço da Fala<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Ser-lhe-á solicitado que forneça algumas informações:
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-Speech**
   * Para **subscrição,** certifique-se de que o **Teste Gratuito** é selecionado.
   * Para **localização,** selecione **West US**.
   * Para **o nível de preços,** selecione **F0**. Este é o nível livre.
   * Para **o grupo De recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
5. Depois de ter introduzido todas as informações necessárias, clique em **Criar**. Pode levar alguns minutos para criar o seu recurso.
6. Mais tarde neste tutorial vai precisar de chaves de subscrição para este serviço. Pode aceder a estas teclas a qualquer momento a partir da **visão geral** do seu recurso (Gerir teclas) ou **Teclas**.

Neste ponto, verifique se o seu grupo de recursos **(SpeechEchoBotTutorial-ResourceGroup)** tem um recurso da Fala:

| Nome | Tipo  | Localização |
|------|-------|----------|
| DiscursoEchoBotTutorial-Discurso | Serviços Cognitivos | E.U.A. Oeste |

### <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

O próximo passo é criar um Plano de Serviço de Aplicações. Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Criar um plano de serviço de aplicações Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Ser-lhe-á solicitado que forneça algumas informações:
   * Definir **Subscrição** para **Teste Gratuito** (também pode utilizar uma subscrição existente).
   * Para **o grupo De recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Dê um **nome**ao seu recurso. Recomendamos **SpeechEchoBotTutorial-AppServicePlan**
   * Para **sistema operativo,** selecione **Windows**.
   * Para **a Região,** selecione **Oeste dos EUA.**
   * Para o **Nível**de Preços, certifique-se de que o **Standard S1** é selecionado. Este deve ser o valor padrão. Caso não seja, certifique-se de que define o **Sistema Operativo** para **windows** como descrito acima.
5. Clique em **Rever e criar**. Devia ver um banner que dizia **validação.**
6. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.

Neste ponto, verifique se o seu grupo de recursos **(SpeechEchoBotTutorial-ResourceGroup)** tem dois recursos:

| Nome | Tipo  | Localização |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicações | E.U.A. Oeste |
| DiscursoEchoBotTutorial-Discurso | Serviços Cognitivos | E.U.A. Oeste |

## <a name="build-an-echo-bot"></a>Construa um Eco Bot

Agora que criaste alguns recursos, vamos construir um bot. Vamos começar com a amostra do Echo Bot, que como o nome indica, faz eco do texto que introduziu como resposta. Não se preocupe, o código da amostra está pronto para usar sem alterações. Está configurado para funcionar com o canal Direct Line Speech, que ligaremos depois de colocarmos o bot no Azure.

> [!NOTE]
> As instruções que se seguem, bem como informações adicionais sobre o Echo Bot estão disponíveis na [README da amostra no GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Coloque a amostra de bot na sua máquina

1. Clone o repositório de amostras:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Launch Visual Studio.
3. A partir da barra de ferramentas, selecione **File** > **Open** > **Project/Solution,** e abra a solução do projeto Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Depois de o projeto estar carregado, pressione <kbd>f5</kbd> para construir e executar o projeto.
5. Um navegador deve ser lançado e verá um ecrã semelhante a este.
    > [!div class="mx-imgBorder"]
    > ![ecobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot correndo em local anfitrião")

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testar a amostra bot com o Emulador Bot Framework

O [Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) é uma aplicação de desktop que permite aos desenvolvedores de bots testar e depurar os seus bots localmente ou remotamente através de um túnel. O Emulador suporta o texto dactilografado como a entrada (não a voz). O bot responderá com texto. Siga estes passos para utilizar o Emulador Bot Framework para testar o seu Echo Bot a funcionar localmente, com entrada de texto e saída de texto. Depois de colocarmos o bot para o Azure vamos testá-lo com entrada de voz e saída de voz.

1. Instale a versão [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 4.3.0 ou superior
2. Lance o Emulador Bot Framework e abra o seu bot:
   * **File** -> **Open Bot**.
3. Introduza o URL para o seu bot. Por exemplo:

   ```
   http://localhost:3978/api/messages
   ```
   e prima "Connect".
4. O bot deve recebê-lo imediatamente com "Olá e bem-vindos!" Mensagem. Digite qualquer mensagem de texto e confirme que obtém uma resposta do bot.
5. Isto é o que uma troca de comunicações com um exemplo de Echo Bot pode parecer: ![bot-framework-emulador](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulador-quadro bot")

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Desloque o seu bot para um Serviço de Aplicações Azure

O próximo passo é colocar o Echo Bot em Azure. Existem algumas formas de implementar um bot, mas neste tutorial vamos focar-nos em publicar diretamente do Visual Studio.

> [!NOTE]
> Em alternativa, pode utilizar um bot utilizando o [CLI Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) e [os modelos](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)de implantação .

1. A partir do Estúdio Visual, abra o Echo Bot que foi configurado para uso com o canal Direct Line Speech:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. No **Solution Explorer,** clique à direita no projeto **EchoBot** e **selecione Publicar...**
1. Uma nova janela intitulada **Pick a publish target** será aberta.
1. Selecione **o Serviço de Aplicações** a partir da navegação dos **serviços Azure,** selecione **Criar Novo**e, em seguida, clique em **Publicar**.
1. Quando aparece a janela **Create App Service:**
   * Clique em **Adicionar uma conta**e iniciar sessão com as credenciais da sua conta Azure. Se já se inscreveu, selecione a conta que deseja da lista de entregas.
   * Para o Nome da **Aplicação,** terá de introduzir um nome globalmente único para o seu Bot. Este nome é usado para criar um URL de bot único. Um valor predefinido será povoado, incluindo a data e a hora (por exemplo: "EchoBot20190805125647"). Pode usar o nome padrão para este tutorial.
   * Para **subscrição,** deite-o para **Teste Gratuito**
   * Para **o Grupo de Recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**
   * Para **plano de hospedagem,** selecione **SpeechEchoBotTutorial-AppServicePlan**
1. Clique em **Criar**.
1. Devias ver uma mensagem de sucesso no Estúdio Visual que se parece com isto:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. O seu navegador predefinido deve abrir e exibir uma página onde se lê: "O seu bot está pronto!".
1. Neste ponto, consulte o seu Grupo de Recursos **SpeechEchoBotTutorial-ResourceGroup** no portal Azure, e confirme que existem três recursos:

| Nome | Tipo  | Localização |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-AppServicePlan | Plano do App Service | E.U.A. Oeste |
| DiscursoEchoBotTutorial-Discurso | Serviços Cognitivos | E.U.A. Oeste |

## <a name="enable-web-sockets"></a>Ativar tomadas web

Terá de fazer uma pequena alteração de configuração para que o seu bot possa comunicar com o canal Direct Line Speech utilizando tomadas web. Siga estes passos para ativar as tomadas web:

1. Navegue para o [portal Azure](https://portal.azure.com)e localize o seu Serviço de Aplicações. O recurso deve ser nomeado semelhante ao **EchoBot20190805125647** (o seu nome de aplicação único).
2. Na navegação dos **serviços Azure,** em **Definições,** clique na **Configuração**.
3. Selecione o separador de **definições gerais.**
4. Localize o alternância para **as tomadas web** e coloque-o **para Ligado**.
5. Clique em **Guardar**.

> [!TIP]
> Pode utilizar os comandos na parte superior da página do Serviço de Aplicações Azure para parar ou reiniciar o serviço. Isto pode ser útil quando se resolução de problemas.

## <a name="create-a-channel-registration"></a>Criar um registo de canal

Agora que criou um Serviço de Aplicações Azure para hospedar o seu bot, o próximo passo é criar um Registo de **Canais Bot**. A criação de um registo de canal é um pré-requisito para registar o seu bot com canais Bot-Framework, incluindo o canal Direct Line Speech.

> [!NOTE]
> Se quiser saber mais sobre como os bots alavancam os canais, consulte [Connect um bot aos canais](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

<!-- https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage -->

1. O primeiro passo é criar um novo recurso para o registo. No [portal Azure,](https://portal.azure.com)clique **em Criar um recurso.**
2. No **bot**tipo barra de pesquisa, após a apareçam os resultados, selecione **Bot Channels Registration**.
3. Clique em **Criar**.
4. Ser-lhe-á solicitado que forneça algumas informações:
   * Para **manípulo bot,** insira **SpeechEchoBotTutorial-BotRegistration**.
   * Para **subscrição,** selecione **Free Trial**.
   * Para **o grupo De recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Para **localização,** selecione **West US**.
     * Para **o nível de preços,** selecione **F0**.
     * Para **o ponto final de Mensagens,** introduza o URL para a sua aplicação web com o caminho `/api/messages` anexado no final. Por exemplo: se o seu nome de aplicação globalmente único fosse **O EchoBot20190805125647,** o seu ponto final de mensagens seria: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Para **insights de aplicação,** pode definir isto para **Off**. Para mais informações, consulte [a análise do Bot.](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)
     * Ignore o Auto criar o ID do Aplicativo e a **palavra-passe.**
5. Na parte inferior da lâmina de registo dos **canais Bot,** clique em **Criar**.

Neste ponto, consulte o seu Grupo de Recursos **SpeechEchoBotTutorial-ResourceGroup** no portal Azure. Deve agora mostrar quatro recursos:

| Nome | Tipo  | Localização |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-AppServicePlan | Plano do App Service | E.U.A. Oeste |
| SpeechEchoBotTutorial-BotRegistration | Registo de Canais Bot | Global |
| DiscursoEchoBotTutorial-Discurso | Serviços Cognitivos | E.U.A. Oeste |

> [!IMPORTANT]
> O recurso de registo de canais Bot mostrará a região Global, mesmo que tenha selecionado os EUA Ocidentais. Isto era esperado.

## <a name="register-the-direct-line-speech-channel"></a>Registe o canal de discurso da linha direta

Agora é hora de registar o seu bot no canal Direct Line Speech. Este canal é o que é usado para criar uma ligação entre o seu eco bot e uma aplicação de cliente compilada com o Speech SDK.

1. Localize e abra o seu recurso **SpeechEchoBotTutorial-BotRegistration** no [portal Azure](https://portal.azure.com).
1. A partir da navegação de **serviços Azure,** selecione **Canais.**
   * Procure **mais canais,** localize e clique em **Direct Line Speech**.
   * Reveja o texto na página intitulada **Configure Direct line Speech,** em seguida, expandir o menu suspenso com o nome "Conta de serviço cognitivo".
   * Selecione o recurso de fala que criou anteriormente (por exemplo, **SpeechEchoBotTutorial-Speech**) do menu para associar o seu bot à chave de subscrição do seu discurso.
   * Clique em **Guardar**.

1. A partir da navegação dos **serviços Azure,** clique em **Definições**.
   * Verifique a caixa com a etiqueta **Ativa de streaming endpoint**. Isto é necessário para permitir um protocolo de comunicação construído em tomadas web entre o seu bot e o canal Direct Line Speech.
   * Clique em **Guardar**.

> [!TIP]
> Se quiser saber mais, consulte [Ligue um bot ao Discurso](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)de Linha Direta . Esta página inclui informações adicionais e questões conhecidas.

## <a name="build-the-direct-line-speech-client"></a>Construa o Cliente de Discurso de Linha Direta

Neste passo, vais construir o Cliente de Discurso de Linha Direta. O cliente é uma aplicação da C# Windows Presentation Foundation (WPF) na que utiliza o [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) para gerir a comunicação com o seu bot utilizando o canal Direct Line Speech. Use-o para interagir e testar o seu bot antes de escrever uma aplicação personalizada para o cliente.

O Cliente de Discurso de Linha Direta tem um UI simples que lhe permite configurar a ligação ao seu bot, ver a conversa de texto, ver as atividades bot-framework em formato JSON e exibir cartões adaptivos. Também suporta o uso de palavras-chave personalizadas. Vai usar este cliente para falar com o seu bot e receber uma resposta de voz.

Antes de seguirmos em frente, certifique-se de que o microfone e os altifalantes estão ativados e funcionam.

1. Navegue até ao repositório GitHub para o [Cliente de Discurso](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md)de Linha Direta .
2. Siga as instruções fornecidas para clonar o repositório, construir o projeto, configurar o cliente e lançar o cliente.
3. Clique em **Reconectar** e certifique-se de que vê a mensagem **Prima o botão do microfone ou escreva para começar a falar com o seu bot**.
4. Vamos testá-lo. Clique no botão do microfone e diga algumas palavras em inglês. O texto reconhecido aparecerá enquanto fala. Quando terminar de falar, o bot responderá com a sua própria voz, dizendo "eco" seguido das palavras reconhecidas.
5. Também pode utilizar texto para comunicar com o bot. Basta escrever o texto na barra inferior. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Erros de resolução de problemas no Cliente de Discurso de Linha Direta

Se receber uma mensagem de erro na janela principal da aplicação, utilize esta tabela para identificar e resolver o erro:

| Erro | O que deveria fazer? |
|-------|----------------------|
|Falha de autenticação de erro: A atualização da WebSocket falhou com um erro de autenticação (401). Verifique se a chave de subscrição correta (ou ficha de autorização) e o nome da região| Na página Definições da aplicação, certifique-se de que introduziu corretamente a chave de Subscrição do Discurso e a sua região.<br>Certifique-se de que a sua chave de discurso e a região-chave foram corretamente inseridas. |
|Falha de ligação de erro: A ligação foi fechada pelo hospedeiro remoto. Código de erro: 1011. Detalhes do erro: Não conseguimos ligar-nos ao bot antes de enviar uma mensagem | Certifique-se de que verificou a caixa ["Ativar o](#register-the-direct-line-speech-channel) ponto final de streaming" e/ou [as **tomadas web** toggled](#enable-web-sockets) para on.<br>Certifique-se de que o seu Serviço de Aplicações Azure está em funcionamento. Se for, tente reiniciar o seu Serviço de Aplicações.|
|Falha de ligação de erro: A ligação foi fechada pelo hospedeiro remoto. Código de erro: 1011. Detalhes do erro: O código de estado de resposta não indica o sucesso: 500 (InternalServerError)| O seu bot especificou uma voz neural no seu [campo](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) De Fala de Atividade de saída, mas a região azure associada à sua chave de subscrição do Discurso não suporta vozes neurais. Ver [Vozes Standard e neurais.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)|
|Falha de ligação de erro: A ligação foi fechada pelo hospedeiro remoto. Código de erro: 1000. Detalhes do erro: Duração máxima de ligação à tomada da web (> 300000 ms)| Este é um erro esperado quando uma ligação ao canal é deixada aberta e inativa por mais de cinco minutos. |

Se o seu problema não for abordado na tabela, consulte os assistentes de [voz: Perguntas frequentes](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Ver atividades de bot

Todos os bots enviam e recebem mensagens **de atividade.** Na janela de Registo de **Atividades** do Cliente de Discurso de Linha Direta, você verá registos timestamped com cada atividade que o cliente recebeu do bot. Pode também ver as atividades que o cliente enviou ao bot utilizando o método [`DialogServiceConnector.SendActivityAsync`.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) Ao selecionar um item de log, mostrará os detalhes da atividade associada como JSON.

Aqui está uma amostra de uma atividade que o cliente recebeu:
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

Para saber mais sobre o que é devolvido na saída json, consulte [campos na Atividade.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md) Para efeitos deste tutorial, pode focar-se nos campos [Texto](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e [Fala.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Ver código fonte do cliente para chamadas para o SDK de Fala

O Cliente de Discurso de Linha Direta utiliza o pacote NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contém o SDK de Fala. Um bom lugar para começar a rever o código da amostra é o método InitSpeechConnector() em [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)de ficheiros, que cria estes dois objetos SDK da fala:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) - Para configurações de configuração (por exemplo, chave de subscrição de discursos, região-chave)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - Gerir a ligação do canal e eventos de subscrição de clientes para lidar com respostas reconhecidas de discurso e bot.

## <a name="add-custom-keyword-activation"></a>Adicionar ativação de palavras-chave personalizadas

O SDK do Discurso suporta a ativação personalizada das palavras-chave. Semelhante a "Hey Cortana" para o Assistente da Microsoft, pode escrever uma aplicação que irá continuar a ouvir uma palavra-chave à sua escolha. Tenha em mente que uma palavra-chave pode ser uma palavra única ou uma frase multi-palavras.

> [!NOTE]
> O termo *palavra-chave* é frequentemente usado intercambiavelmente com o termo palavra de *despertar*, e você pode ver ambos usados na documentação da Microsoft.

A deteção de palavras-chave é feita na aplicação do cliente. Se utilizar uma palavra-chave, o áudio só é transmitido para o canal Direct Line Speech se a palavra-chave for detetada. O canal Direct Line Speech inclui um componente chamado verificação de *palavras-chave (KWV),* que faz um processamento mais complexo na nuvem para verificar se a palavra-chave que escolheu está no início do fluxo de áudio. Se a verificação da palavra-chave for bem sucedida, o canal comunicará com o bot.

Siga estes passos para criar um modelo de palavra-chave, configure o Cliente de Discurso de Linha Direta para usar este modelo e, finalmente, testá-lo com o seu bot.

1. Siga estas instruções para [criar uma palavra-chave personalizada utilizando o serviço De Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Desinser o ficheiro modelo que descarregou no passo anterior. Deve ser nomeado pela sua palavra-chave. Está à procura de um ficheiro chamado `kws.table`.
3. No cliente direct line Speech, localize o menu **Definições** (procure o ícone de engrenagem no direito superior). Localize o caminho do **ficheiro Modelo** e introduza o nome completo do caminho para o ficheiro `kws.table` a partir do passo 2.
4. Certifique-se de verificar a caixa com a etiqueta **ativada**. Deve ver esta mensagem ao lado da caixa de verificação: "Ouvirá a palavra-chave na próxima ligação". Se forneceu o ficheiro errado ou um caminho inválido, deve ver uma mensagem de erro.
5. Introduza a **chave de subscrição**do seu discurso, região chave de **subscrição,** e clique em **OK** para fechar o menu **Definições.**
6. Clique em **Voltar a ligar**. Deve ver uma mensagem onde se lê: "Nova conversa começou - escreva, pressione o botão do microfone ou diga a palavra-chave". A aplicação está agora continuamente a ouvir.
7. Fala qualquer frase que comece com a tua palavra-chave. Por exemplo: " **{a sua palavra-chave}** , que horas são?". Não precisas de parar depois de proferir a palavra-chave. Quando terminada, duas coisas acontecem:
   * Verá uma transcrição do que falou.
   * Pouco depois, ouvirá a resposta do bot.
8. Continue a experimentar os três tipos de entrada que o seu bot suporta:
   * Texto dactilografado na barra inferior
   * Premindo o ícone do microfone e falando
   * Dizendo uma frase que começa com a sua palavra-chave

### <a name="view-the-source-code-that-enables-keyword"></a>Ver o código fonte que permite a palavra-chave

No código fonte do Cliente do Discurso de Linha Direta, dê uma olhada nestes ficheiros para rever o código que é usado para permitir a deteção de palavras-chave:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) inclui uma chamada para o método Speech SDK [`KeywordRecognitionModel.fromFile()`, ](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)que é usado para instantaneamente o modelo a partir de um ficheiro local no disco.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) inclui uma chamada para [o ](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)método SDK da fala`DialogServiceConnector.StartKeywordRecognitionAsync()`, que ativa a deteção contínua de palavras-chave.

## <a name="optional-change-the-language-and-bot-voice"></a>(Opcional) Mude a linguagem e a voz do bot

O bot que criaste vai ouvir e responder em inglês, com uma voz de texto a falar inglês por defeito. No entanto, não se limita a usar inglês ou uma voz padrão. Nesta secção, aprenderá a mudar a linguagem que o seu bot irá ouvir e responder. Também aprenderá a selecionar uma voz diferente para essa linguagem.

### <a name="change-the-language"></a>Mudar a linguagem

Pode escolher entre qualquer uma das línguas mencionadas na tabela [de discursos a texto.](language-support.md#speech-to-text) No exemplo abaixo, mudaremos a língua para alemão.

1. Abra a aplicação Direct Line Speech Client, clique no botão de definições (ícone de engrenagem superior direita) e introduza `de-de` no campo Idioma (este é o valor Locale mencionado na tabela [discurso-texto).](language-support.md#speech-to-text) Isto define a linguagem falada para ser reconhecida, substituindo o padrão `en-us`. Isto também instrui o canal Direct Line Speech a usar uma voz alemã padrão para a resposta bot.
2. Feche a página de definições e clique no botão Reconnect para estabelecer uma nova ligação ao seu bot eco.
3. Clique no botão do microfone e diga uma frase em alemão. Verá o texto reconhecido e o eco bot respondendo com a voz alemã predefinida.


### <a name="change-the-default-bot-voice"></a>Alterar a voz padrão do bot

A seleção da voz texto-a-voz e a pronúncia de controlo podem ser feitas se o Bot especificar a resposta sob a forma de uma linguagem de [marcação](speech-synthesis-markup.md) de síntese da fala (SSML) em vez de texto simples. O bot eco não utiliza SSML, mas podemos facilmente modificar o código para o fazer. No exemplo abaixo adicionamos SSML à resposta do eco bot, de tal forma que a voz alemã Stefan Apollo (uma voz masculina) será usada em vez da voz feminina padrão. Consulte a lista de [Vozes Padrão](language-support.md#standard-voices) e [Vozes Neurais](language-support.md#neural-voices) suportadas para a sua língua.

1. Vamos começar por abrir `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Localize estas duas linhas:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Substitua-os por:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Construa a sua solução no Visual Studio e corrija quaisquer erros de construção.

O segundo argumento do método 'MessageFactory.Text' define o campo de [fala da Atividade](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) na resposta do bot. Com a alteração acima, foi substituído de texto simples para SSML, a fim de especificar uma voz alemã não predefinida.

### <a name="redeploy-your-bot"></a>Recoloque o seu bot

Agora que fez a mudança necessária para o bot, o próximo passo é reeditá-lo no seu Serviço de Aplicações Azure e experimentá-lo:

1. Na janela Solution Explorer, clique à direita no projeto **EchoBot** e **selecione Publicar**.
2. A configuração de implementação anterior já foi carregada como padrão. Basta clicar **Em Publicar** ao lado do **EchoBot20190805125647 - Web Deploy**.
3. A mensagem **Publish Succeeded** aparecerá na janela de saída do Estúdio Visual, e uma página web será lançada com a mensagem "O seu bot está pronto!".
4. Abra a aplicação Direct Line Speech Client, clique no botão de definições (ícone de engrenagem superior direita) e certifique-se de que ainda tem `de-de` no campo Idioma.
5. Siga as instruções em [Build the Direct Line Speech Client](#build-the-direct-line-speech-client) para se reconectar com o seu robô recém-implantado, falar na nova língua e ouvi-lo responder a essa língua com a nova voz.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a usar o eco-bot implantado neste tutorial, pode removê-lo e todos os seus recursos Azure associados simplesmente eliminando o grupo de recursos Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. A partir do [portal Azure,](https://portal.azure.com)clique em **Grupos de Recursos** da navegação de **serviços Azure.**
2. Encontre o grupo de recursos nomeado: **SpeechEchoBotTutorial-ResourceGroup**. Clique nos três pontos (...).
3. Selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa a sua própria aplicação de cliente com o Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Consulte também

* Implantação para uma [região de Azure perto de si](https://azure.microsoft.com/global-infrastructure/locations/) para ver a melhoria do tempo de resposta do bot
* Implantação para uma [região do Azure que suporta vozes de TTS neural](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices) de alta qualidade
* Preços associados ao canal Direct Line Speech:
  * [Preços do serviço bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Construindo e implantando o seu próprio bot ativado por voz:
  * Construa um [bot bot-framework](https://dev.botframework.com/). Registe-o com [o canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) e [personalize o seu bot para voz](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Explore [as soluções bot-quadro existentes](https://microsoft.github.io/botframework-solutions/index): Construa um [assistente virtual](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) e [estenda-o ao Discurso da Linha Direta](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
