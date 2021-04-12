---
title: 'Tutorial: Vozes permitem o seu bot usando o Speech SDK - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai criar um Echo Bot usando o Microsoft Bot Framework, implantá-lo para Azure, e registrá-lo com o canal Bot Framework Direct Line Speech. Em seguida, irá configurar uma aplicação de cliente de amostra para Windows que lhe permite falar com o seu bot e ouvi-lo responder-lhe de volta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fa449ad3d9a0e26bd0754a67581c8d63fa025e55
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552313"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: Ativar a voz do seu bot utilizando o SDK de discurso

Pode utilizar o serviço Speech para ativar um chat bot.

Neste tutorial, vai criar um bot que repete o que lhe diz.
Irá criar o seu bot utilizando o Microsoft Bot Framework, implantá-lo para o Azure e registá-lo com o canal Bot Framework Direct Line Speech.
Em seguida, irá configurar uma aplicação de cliente de amostra para Windows que lhe permite falar com o seu bot e ouvi-lo responder-lhe.

Este tutorial é projetado para desenvolvedores que são novos em Azure, bots de quadro bot, Direct Line Speech, ou o Speech SDK, e querem construir rapidamente um sistema de trabalho com codificação limitada. Não é necessária experiência ou familiaridade com estes serviços.

O chat bot ativado por voz que faz neste tutorial segue estes passos:

1. A aplicação do cliente da amostra está configurada para ligar ao canal Direct Line Speech e ao Echo Bot.
1. Quando o utilizador carrega num botão, o áudio de voz transmite-se a partir do microfone. (Ou o áudio é continuamente gravado quando uma palavra-chave personalizada é usada.)
1. Se for utilizada uma palavra-chave personalizada, a deteção de palavras-chave acontece no dispositivo local, com o streaming de áudio a correr para a nuvem.
1. Utilizando o Speech SDK, a aplicação do cliente da amostra liga-se ao canal Direct Line Speech e transmite áudio.
1. Opcionalmente, a verificação de palavras-chave de maior precisão acontece no serviço.
1. O áudio é passado para o serviço de reconhecimento de voz e transcrito para texto.
1. O texto reconhecido é passado para o Echo Bot como uma Atividade-Quadro bot.
1. O texto de resposta é transformado em áudio pelo serviço Text-to-Speech (TTS) e transmitido de volta para a aplicação do cliente para reprodução.

<!-- svg src in User Story 1754106 -->
![diagrama-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "O fluxo do Canal da Fala")

> [!NOTE]
> Os passos neste tutorial não requerem um serviço pago. Como novo utilizador do Azure, poderá utilizar os créditos da sua subscrição gratuita de testes Azure e do nível gratuito do serviço Speech para completar este tutorial.

Aqui está o que este tutorial cobre:
> [!div class="checklist"]
> * Criar novos recursos Azure
> * Construa, teste e implemente a amostra Echo Bot para um Serviço de Aplicações Azure
> * Registe o seu bot com o canal Direct Line Speech
> * Construa e execute o Cliente Assistente de Voz do Windows para interagir com o seu Echo Bot
> * Adicionar ativação de palavras-chave personalizadas
> * Aprenda a mudar a linguagem do discurso reconhecido e falado

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa para completar este tutorial:

- Um PC Windows 10 com microfone e altifalantes de funcionamento (ou auscultadores)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou superior, com a ASP.NET e a carga de trabalho **de desenvolvimento web** instalada
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) ou superior
- Uma conta do Azure. [Inscreva-se gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
- Uma conta do [GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

A aplicação de cliente que você vai criar neste tutorial usa um punhado de serviços Azure. Para reduzir o tempo de ida e volta para respostas do seu bot, você vai querer ter certeza de que estes serviços estão localizados na mesma região de Azure. Nesta secção, você vai criar um grupo de recursos na região **oeste dos EUA.** Este grupo de recursos será utilizado na criação de recursos individuais para o Quadro bot, o canal de fala da linha direta e o serviço Speech.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Criar um grupo de recursos </a>
1. Ser-lhe-á solicitado que forneça algumas informações:
   * Desa couuser a **Assinatura** **gratuita (também** pode utilizar uma subscrição existente).
   * Insira um nome para o seu **grupo de Recursos.** Recomendamos **o SpeechEchoBotTutorial-ResourceGroup**.
   * Da **Região,** selecione **West US.**
1. Clique em **Rever e criar**. Devia ver um banner que dizia **validação passada.**
1. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.
1. Tal como acontece com os recursos que irá criar mais tarde neste tutorial, é uma boa ideia fixar este grupo de recursos no seu painel de instrumentos para facilitar o acesso. Se quiser fixar este grupo de recursos, clique no ícone pino direito do nome do grupo de recursos.

### <a name="choosing-an-azure-region"></a>Escolher uma região de Azure

Se você quiser usar uma região diferente para este tutorial estes fatores podem limitar suas escolhas:

* Certifique-se de que utiliza uma [região de Azure apoiada.](regions.md#voice-assistants)
* O canal Direct Line Speech utiliza o serviço text-to-speech, que tem vozes neurais e padrão. Vozes neurais e padrão estão disponíveis nestas [regiões de Azure.](regions.md#neural-and-standard-voices)

Para obter mais informações sobre as regiões, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="create-resources"></a>Criar recursos

Agora que tem um grupo de recursos numa região apoiada, o próximo passo é criar recursos individuais para cada serviço que utilizará neste tutorial.

### <a name="create-a-speech-service-resource"></a>Criar um recurso do serviço de Voz

Siga estas instruções para criar um recurso de discurso:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de serviço de fala </a>
4. Ser-lhe-á solicitado que forneça algumas informações:
   * Dê um **nome** ao seu recurso. Recomendamos **DiscursoEchoBotTutorial-Discurso**
   * Para **subscrição,** certifique-se de que **o Teste Gratuito** está selecionado.
   * Para **localização**, selecione **West US**.
   * Para **o nível de preços**, selecione **F0**. Este é o nível livre.
   * Para **o grupo de recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
5. Depois de ter introduzido todas as informações necessárias, clique em **Criar**. Pode levar alguns minutos para criar o seu recurso.
6. Mais tarde neste tutorial você precisará de chaves de subscrição para este serviço. Pode aceder a estas teclas a qualquer momento a partir da **Visão Geral** do seu recurso (Gerir teclas) ou **Keys**.

Neste ponto, verifique se o seu grupo de recursos **(SpeechEchoBotTutorial-ResourceGroup)** tem um recurso de voz:

| Nome | Tipo  | Localização |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | E.U.A. Oeste |

### <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

O próximo passo é criar um Plano de Serviço de Aplicações. Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Criar um plano de Serviço de Aplicações Azure </a>
4. Ser-lhe-á solicitado que forneça algumas informações:
   * Desa couuser a **Assinatura** **gratuita (também** pode utilizar uma subscrição existente).
   * Para **o grupo de recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Dê um **nome** ao seu recurso. Recomendamos **SpeechEchoBotTutorial-AppServicePlan**
   * Para **o Sistema Operativo**, selecione **Windows**.
   * Para **a Região**, selecione **West US**.
   * Para **o Preço Tier,** certifique-se de que **o Standard S1** está selecionado. Este deve ser o valor padrão. Se não for, certifique-se de que define o **Sistema Operativo** para o **Windows** como descrito acima.
5. Clique em **Rever e criar**. Devia ver um banner que dizia **validação passada.**
6. Clique em **Criar**. Pode levar alguns minutos para criar o grupo de recursos.

Neste momento, verifique se o seu grupo de recursos **(SpeechEchoBotTutorial-ResourceGroup)** tem dois recursos:

| Nome | Tipo  | Localização |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | E.U.A. Oeste |

## <a name="build-an-echo-bot"></a>Construir um Echo Bot

Agora que criaste alguns recursos, vamos construir um bot. Vamos começar com a amostra do Echo Bot, que, como o nome indica, ecoa o texto que inseriu como resposta. Não se preocupe, o código de amostra está pronto para usar sem alterações. Está configurado para trabalhar com o canal Direct Line Speech, que ligaremos depois de colocarmos o bot no Azure.

> [!NOTE]
> As instruções que se seguem, bem como informações adicionais sobre o Echo Bot estão disponíveis na [readme da amostra no GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Coloque a amostra de bot na sua máquina

1. Clone o repositório de amostras:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Lançamento Visual Studio.
3. A partir da barra de ferramentas, selecione **File**  >  **Open**  >  **Project/Solution** e abra a solução de projeto Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Depois de o projeto estar carregado, pressione <kbd>f5</kbd> para construir e executar o projeto.
5. Um navegador será lançado e você verá um ecrã semelhante a este.
    > [!div class="mx-imgBorder"]
    > [![A screenshot mostra a página EchoBot com a mensagem O seu bot está pronto!](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot correndo na localidade")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Teste a amostra de bot com o Emulador De Estrutura bot

O [Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) é uma aplicação de ambiente de trabalho que permite aos desenvolvedores de bots testar e depurar os seus bots localmente (ou remotamente através de um túnel). O Emulador aceita o texto dactilografado como entrada (não voz). O bot também responderá com texto. Siga estes passos para utilizar o Emulador Quadro bot para testar o seu Echo Bot a funcionar localmente, com entrada de texto e saída de texto. Depois de colocarmos o bot no Azure vamos testá-lo com entrada de voz e saída de voz.

1. Instale a versão 4.3.0 ou superior do [Emulador De Quadro bot](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)
2. Lance o Emulador De Estrutura Bot e abra o seu bot:
   * **Arquivo**  ->  **Bot aberto.**
3. Insira a URL para o seu bot. Por exemplo:

   ```
   http://localhost:3978/api/messages
   ```
   e prima "Connect".
4. O bot deve recebê-lo com "Olá e bem-vindos!" . Digite qualquer mensagem de texto e confirme que obtém uma resposta do bot.
5. Isto é o que uma troca de comunicação com uma instância Echo Bot pode parecer: [ ![Screenshot mostra o Emulador Quadro bot.](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulador de estrutura de bot")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Implemente o seu bot num Serviço de Aplicações Azure

O próximo passo é colocar o Echo Bot em Azure. Existem algumas formas de implementar um bot, mas neste tutorial vamos focar-nos em publicar diretamente do Visual Studio.

> [!NOTE]
> Em alternativa, pode implantar um bot utilizando o [CLI Azure](/azure/bot-service/bot-builder-deploy-az-cli) e [os modelos de implantação](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot).

> [!NOTE]
> Se **publicar...** não aparece à medida que executa os seguintes passos, use o Instalador visual do Estúdio para adicionar a ASP.NET e a carga de trabalho **de desenvolvimento web.**

1. A partir do Visual Studio, abra o Echo Bot que foi configurado para uso com o canal Direct Line Speech:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. No **Solution Explorer,** clique à direita no projeto **EchoBot** e **selecione Publicar...**
1. Abre uma nova janela intitulada **Publish.**
1. Selecione **Azure**, clique **em Seguinte**, selecione **Azure App Service (Windows)**, clique em **Seguinte**, e clique em Criar um novo Serviço **de Aplicações Azure...** pelo sinal verde plus.
1. Quando aparece a janela **do Serviço de Aplicações (Windows):**
   * Clique **em Adicionar uma conta** e iniciar sação com as suas credenciais de conta Azure. Se já fez a sua assinatura, selecione a conta que pretende da lista de entrega.
   * Para o **nome,** terá de introduzir um nome globalmente único para o seu Bot. Este nome é usado para criar um URL de bot único. Um valor predefinido será preenchido incluindo a data e a hora (por exemplo: "EchoBot20190805125647"). Pode utilizar o nome predefinido para este tutorial.
   * Para **subscrição**, desemosseê-lo para **Teste Gratuito**
   * Para **o Grupo de Recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**
   * Para **o Plano de Hospedagem**, selecione **SpeechEchoBotTutorial-AppServicePlan**
1. Clique em **Criar**. No ecrã final do assistente, clique em **Terminar**.
1. Clique em **Publicar** no lado direito do ecrã Publicar. O Visual Studio implanta o bot para o Azure.
1. Deve ver uma mensagem de sucesso na janela de saída do Visual Studio que se parece com isto:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. O seu navegador predefinido deve abrir e exibir uma página que diz: "O seu bot está pronto!".
1. Neste momento, consulte o seu Grupo de Recursos **SpeechEchoBotTutorial-ResourceGroup** no portal Azure e confirme estes três recursos:

| Nome | Tipo  | Localização |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | E.U.A. Oeste |

## <a name="enable-web-sockets"></a>Ativar tomadas web

Terá de fazer uma pequena alteração de configuração para que o seu bot possa comunicar com o canal Direct Line Speech utilizando tomadas web. Siga estes passos para permitir tomadas web:

1. Navegue para o [portal Azure](https://portal.azure.com)e clique no seu Serviço de Aplicações. O recurso deve ser nomeado semelhante ao **EchoBot20190805125647** (nome exclusivo da aplicação).
2. No painel de navegação esquerdo, em **Definições,** clique em **Configuração**.
3. Selecione o **separador Definições Gerais.**
4. Localize o toggle para **tomadas Web** e coloque-o **em On**.
5. Clique em **Guardar**.

> [!TIP]
> Pode utilizar os controlos na parte superior da sua página de Serviço de Aplicações Azure para parar ou reiniciar o serviço. Isto pode ser útil quando se resolver problemas.

## <a name="create-a-channel-registration"></a>Criar um registo de canais

Agora que criou um Serviço de Aplicações Azure para hospedar o seu bot, o próximo passo é criar um **Registo de Canais Bot**. A criação de um registo de canais é um pré-requisito para registar o seu bot com canais Bot Framework, incluindo o canal Direct Line Speech. Se quiser saber mais sobre como os bots usam canais, consulte [o Connect a bot para os canais.](/azure/bot-service/bot-service-manage-channels)

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Criar um registo de canais bot Azure </a>
2. Ser-lhe-á solicitado que forneça algumas informações:
   * Para **o manípulo Bot**, insira **SpeechEchoBotTutorial-BotRegistration-###** e **####** substitua por você é um número de escolha. Note que o cabo Bot deve ser globalmente único. Se introduzir uma pega Bot mas receber a mensagem de erro _O ID do bot solicitado não está disponível,_ em seguida, escolha um número diferente. Nos exemplos abaixo usamos 8726
   * Para **subscrição**, selecione **Free Trial**.
   * Para **o grupo de recursos**, selecione **SpeechEchoBotTutorial-ResourceGroup**.
   * Para **localização**, selecione **West US**.
     * Para **o nível de preços**, selecione **F0**.
     * Para **o ponto final de mensagens,** insira o URL para a sua aplicação web com o caminho `/api/messages` anexado no final. Por exemplo: se o seu nome de aplicação globalmente único fosse **EchoBot20190805125647,** o seu ponto final de mensagens seria: `https://EchoBot20190805125647.azurewebsites.net/api/messages/` .
     * Para **insights de aplicação,** pode definir isto para **Desligar**. Para mais informações, consulte [a análise do Bot.](/azure/bot-service/bot-service-manage-analytics)
     * Ignore **Auto criar ID de aplicação e senha.**
5. Na parte inferior da lâmina de registo dos **canais bot,** clique em **Criar**.

Neste ponto, consulte o seu Grupo de Recursos **SpeechEchoBotTutorial-ResourceGroup** no portal Azure. Deve agora mostrar pelo menos quatro recursos:

| Nome | Tipo  | Localização |
|------|-------|----------|
| EchoBot20190805125647 | Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-AppServicePlan | Plano do Serviço de Aplicações | E.U.A. Oeste |
| SpeechEchoBotTutorial-BotRegistration-8726 | Registo de canais de bot | Global |
| SpeechEchoBotTutorial-Speech | Serviços Cognitivos | E.U.A. Oeste |

> [!IMPORTANT]
> O recurso De Registo de Canais Bot mostrará a região Global, apesar de ter selecionado os EUA Ocidentais. Isto era esperado.

## <a name="optional-test-in-web-chat"></a>Opcional: Teste no chat web

A página de Registo de Canais Bot Azure tem uma opção **de teste em Web Chat** sob **gestão bot.** Não funcionará por defeito com o seu bot, uma vez que o chat web precisa de autenticar contra o seu bot. Se quiser testar o seu bot implantado com entrada de texto, siga os passos abaixo. Note que estes passos são opcionais, e não são necessários para continuar com os próximos passos do tutorial. 

1. Localize e abra o seu recurso **EchoBotTutorial-BotRegistration-### ##** no [portal Azure](https://portal.azure.com)
1. A partir da navegação **de gestão bot,** selecione **Definições**. Copie o valor no **ID da Aplicação da Microsoft**
1. Abra a solução Visual Studio EchoBot. No explorador de solução, localize e clique duas vezes no **appsettings.jsem**
1. Substitua a cadeia vazia ao lado do **MicrosoftAppId** no ficheiro JSON pelo valor de ID copiado
1. Voltei ao portal Azure, na navegação de **gestão bot,** selecione **Definições** e clique em **(Gerir)** ao lado do **ID da Aplicação da Microsoft**
1. Clique no **novo segredo do cliente.** Adicione uma descrição (por exemplo, "web chat") e clique em **Adicionar**. Copiar o novo segredo
1. Substitua a cadeia vazia ao lado do **MicrosoftAppPassword** no ficheiro JSON pelo valor secreto copiado
1. Guarde o ficheiro JSON. Deve ter um aspeto semelhante a:
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. Re-publique a aplicação (clique à direita no projeto **EchoBot** no explorador de soluções Visual Studio, **selecione Publicar...** e clique no botão **Publicar)**
10. Agora está pronto para testar o bot no web chat!

## <a name="register-the-direct-line-speech-channel"></a>Registar o canal de fala de linha direta

Agora é hora de registar o seu bot no canal Direct Line Speech. Este canal cria uma ligação entre o seu bot e uma aplicação de cliente compilada com o Speech SDK.

1. Localize e abra o seu recurso **SpeechEchoBotTutorial-BotRegistration-###** # no [portal Azure](https://portal.azure.com).
1. A partir da navegação **de gestão bot,** selecione **Canais.**
   * Em **Mais canais,** clique em **Direct Line Speech**.
   * Reveja o texto na página intitulada **Configure Direct line Speech**, em seguida, expanda o menu de entrega de conta de **serviço cognitivo.**
   * Selecione o recurso de fala que criou anteriormente (por exemplo, **SpeechEchoBotTutorial-Speech**) do menu para associar o seu bot à tecla de subscrição de discurso.
   * Ignore o resto dos campos opcionais.
   * Clique em **Guardar**.

1. A partir da navegação **de gestão bot,** clique em **Definições**.
   * Verifique a caixa com a etiqueta **Enable Streaming Endpoint**. Isto é necessário para criar um protocolo de comunicação construído em tomadas web entre o seu bot e o canal Direct Line Speech.
   * Clique em **Guardar**.

> [!TIP]
> Se quiser saber mais, consulte [o Connect a Bot to Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech). Esta página inclui informações adicionais e questões conhecidas.

## <a name="run-the-windows-voice-assistant-client"></a>Executar o Cliente Assistente de Voz do Windows

Neste passo, irá executar o Cliente Assistente de Voz do Windows. O cliente é uma aplicação da Fundação de Apresentação do Windows (WPF) em C# que utiliza o [Speech SDK](./speech-sdk.md) para gerir a comunicação com o seu bot utilizando o canal Direct Line Speech. Use-o para interagir e testar o seu bot antes de escrever uma aplicação personalizada para o cliente. É de código aberto, por isso podes descarregar o executável e executá-lo, ou construí-lo tu mesmo.

O Cliente Assistente de Voz do Windows tem uma UI simples que lhe permite configurar a ligação ao seu bot, ver a conversa de texto, ver as atividades do Bot Framework no formato JSON e exibir cartões adaptativos. Também suporta o uso de palavras-chave personalizadas. Vai usar este cliente para falar com o seu bot e receber uma resposta de voz.

> [!NOTE]
> Neste ponto, confirme se o microfone e os altifalantes estão ativados e a funcionar.

1. Navegue no repositório GitHub para o [Cliente Assistente de Voz](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)do Windows .
1. Siga as instruções fornecidas para qualquer um
   * descarregar um executável pré-construído em um pacote ZIP para executar, ou
   * construir o executável por si mesmo, clonando o repositório e construindo o projeto.

1. Lance a aplicação do `VoiceAssistantClient.exe` cliente e configuure-a para ligar ao seu bot, seguindo as instruções no repositório GitHub.
1. Clique **em Reconectar** e certifique-se de ver a mensagem **Nova conversa iniciada - escreva ou prima o botão do microfone**.
1. Vamos testá-lo. Clique no botão do microfone e fale algumas palavras em inglês. O texto reconhecido aparecerá enquanto fala. Quando acabares de falar, o bot responderá com a sua própria voz, dizendo "eco" seguido das palavras reconhecidas.
1. Também pode usar texto para comunicar com o bot. Basta escrever o texto na barra inferior. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Erros de resolução de problemas no Cliente Assistente de Voz do Windows

Se receber uma mensagem de erro na janela da sua aplicação principal, utilize esta tabela para identificar e resolver o erro:

| Erro | O que deve fazer? |
|-------|----------------------|
|Erro (AutenticaçãoFailure) : WebSocket Upgrade falhou com um erro de autenticação (401). Verifique se a chave de subscrição correta (ou token de autorização) e o nome da região| Na página Definições da aplicação, certifique-se de que introduziu corretamente a tecla de Subscrição de Discurso e a sua região.<br>Certifique-se de que a sua chave de discurso e região-chave foram corretamente inseridas. |
|Erro (ConnectionFailure) : A ligação foi fechada pelo anfitrião remoto. Código de erro: 1011. Detalhes de erro: Não conseguimos ligar-nos ao bot antes de enviar uma mensagem | Certifique-se de que verificou a caixa ["Ativar o ponto final de streaming"](#register-the-direct-line-speech-channel) e/ou [as **tomadas Web** alternadas](#enable-web-sockets) para on.<br>Certifique-se de que o seu Serviço de Aplicações Azure está a funcionar. Se for, tente reiniciar o seu Serviço de Aplicações.|
|Erro (ConnectionFailure) : A ligação foi fechada pelo anfitrião remoto. Código de erro: 1002. Detalhes de erro: O servidor devolveu o código de estado '503' quando o código de estado '101' era esperado | Certifique-se de que verificou a caixa ["Ativar o ponto final de streaming"](#register-the-direct-line-speech-channel) e/ou [as **tomadas Web** alternadas](#enable-web-sockets) para on.<br>Certifique-se de que o seu Serviço de Aplicações Azure está a funcionar. Se for, tente reiniciar o seu Serviço de Aplicações.|
|Erro (ConnectionFailure) : A ligação foi fechada pelo anfitrião remoto. Código de erro: 1011. Detalhes de erro: O código de estado de resposta não indica sucesso: 500 (InternalServerError)| O seu bot especificou uma voz neural no campo De [Expressão](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) de Atividade de saída, mas a região Azure associada à sua chave de subscrição do Discurso não suporta vozes neurais. Ver [vozes neurais e padrão.](./regions.md#neural-and-standard-voices)|

Se o seu problema não for abordado na tabela, consulte [assistentes de voz: Perguntas frequentes](faq-voice-assistants.md). Se ainda não conseguir resolver o seu problema depois de seguir todos os passos deste tutorial, introduza um novo número na  [página do Assistente de Voz GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues).

#### <a name="a-note-on-connection-time-out"></a>Uma nota sobre o tempo de intervalo da ligação

Se estiver ligado a um bot e não tiver atividade nos últimos 5 minutos, o serviço fechará automaticamente a ligação websocket com o cliente e com o bot. Esta ação é propositada. Aparecerá uma mensagem na barra inferior: *"Ligação ativa cronometrada, mas pronta para voltar a ligar a pedido".* Não é necessário premir o botão "Reconectar" - basta premir o botão do microfone e começar a falar, digitar uma mensagem de texto ou dizer a palavra-chave (se estiver ativada). A ligação será restabelecida automaticamente.  
### <a name="view-bot-activities"></a>Ver atividades de bot

Cada bot envia e recebe mensagens **de atividade.** Na janela 'Registo de **Atividade'** do Cliente Assistente de Voz do Windows, verá registos com cartão temporizado com cada atividade que o cliente recebeu do bot. Também pode ver as atividades que o cliente enviou para o bot usando o [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)  método. Quando selecionar um item de registo, mostrará os detalhes da atividade associada como JSON.

Aqui está uma amostra json de uma atividade que o cliente recebeu:

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
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Para saber mais sobre o que é devolvido na saída JSON, consulte [os campos na Atividade.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md) Para efeitos deste tutorial, pode focar-se nos campos [Texto](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e [Fala.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Ver código fonte do cliente para chamadas para o SDK de discurso

O Cliente Assistente de Voz do Windows utiliza o pacote NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contém o SDK de voz. Um bom lugar para começar a rever o código de amostra é o método InitSpeechConnector() em [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) ficheiro, que cria estes dois objetos SDK do discurso:
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) - Para configurações de configuração (por exemplo, chave de subscrição de discursos, região-chave)
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - Gerir os eventos de ligação do canal e de subscrição de clientes para lidar com respostas reconhecidas de fala e bot.

## <a name="add-custom-keyword-activation"></a>Adicionar ativação de palavras-chave personalizadas

O SDK de discurso suporta a ativação de palavras-chave personalizadas. Semelhante a "Hey Cortana" para o Assistente da Microsoft, pode escrever uma aplicação que irá ouvir continuamente uma palavra-chave à sua escolha. Tenha em mente que uma palavra-chave pode ser uma palavra única ou uma frase multi-palavras.

> [!NOTE]
> O termo *palavra-chave* é frequentemente usado alternadamente com o termo *palavra despertar*, e pode ver ambos usados na documentação da Microsoft.

A deteção de palavras-chave é feita na aplicação do cliente. Se utilizar uma palavra-chave, o áudio só é transmitido para o canal Direct Line Speech se a palavra-chave for detetada. O canal Direct Line Speech inclui um componente chamado *verificação de palavras-chave (KWV),* que faz um processamento mais complexo na nuvem para verificar se a palavra-chave que escolheu está no início do fluxo de áudio. Se a verificação da palavra-chave for bem sucedida, então o canal comunicará com o bot.

Siga estes passos para criar um modelo de palavra-chave, configuure o Cliente Assistente de Voz do Windows para utilizar este modelo e, finalmente, teste-o com o seu bot.

1. Siga estas instruções para [criar uma palavra-chave personalizada utilizando o serviço Desempas .](./custom-keyword-basics.md)
2. Desaperte o ficheiro modelo que descarregou no passo anterior. Deve ser nomeado pela sua palavra-chave. Está à procura de um ficheiro `kws.table` chamado.
3. No Cliente Assistente de Voz do Windows, localize o menu **Definições** (procure o ícone de engrenagem no topo direito). Localize **o caminho do ficheiro do modelo** e introduza o nome do caminho completo para o ficheiro a partir do passo `kws.table` 2.
4. Certifique-se de verificar a caixa etiquetada **Ativada**. Deve ver esta mensagem ao lado da caixa de verificação: "Ouvirá a palavra-chave na próxima ligação". Se forneceu o ficheiro errado ou um caminho inválido, deverá ver uma mensagem de erro.
5. Insira a **tecla de subscrição de** discurso, **região-chave de subscrição** e, em seguida, clique **em OK** para fechar o menu **Definições.**
6. Clique **em Reconectar**. Deve ver uma mensagem onde se lê: "Nova conversa começou - escreva, pressione o botão do microfone ou diga a palavra-chave". A aplicação está agora a ouvir continuamente.
7. Diga qualquer frase que comece com a sua palavra-chave. Por exemplo: "**{your keyword}**, que horas são?". Não precisa parar depois de proferir a palavra-chave. Quando terminadas, duas coisas acontecem:
   * Verá uma transcrição do que falou.
   * Pouco depois, ouvirá a resposta do bot.
8. Continue a experimentar os três tipos de entrada que o seu bot suporta:
   * Texto dactilografado na barra inferior
   * Pressionando o ícone do microfone e falando
   * Dizendo uma frase que começa com a sua palavra-chave

### <a name="view-the-source-code-that-enables-keyword"></a>Ver o código fonte que permite a palavra-chave

No código fonte do Cliente assistente de voz do Windows, veja estes ficheiros para rever o código utilizado para permitir a deteção de palavras-chave:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) inclui uma chamada para o método SDK de [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) fala, que é usado para instantaneaizar o modelo a partir de um ficheiro local no disco.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) inclui uma chamada para o método SDK de [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) fala, que ativa a deteção contínua de palavras-chave.

## <a name="optional-change-the-language-and-bot-voice"></a>(Opcional) Mude a linguagem e a voz bot

O bot que criaste vai ouvir e responder em inglês, com uma voz de texto-a-voz em inglês padrão. No entanto, não se limita a usar inglês ou uma voz padrão. Nesta secção, aprenderás a mudar a linguagem que o teu bot vai ouvir e responder. Também aprenderá a selecionar uma voz diferente para essa língua.

### <a name="change-the-language"></a>Mudar a linguagem

Pode escolher entre qualquer uma das línguas mencionadas na tabela [de discursos para texto.](language-support.md#speech-to-text) No exemplo abaixo, mudaremos a língua para alemão.

1. Abra a aplicação Cliente Assistente de Voz do Windows, clique no botão de definições (ícone de engrenagem superior direita) e introduza `de-de` no campo Idioma (este é o valor Localo mencionado na tabela [discurso-a-texto).](language-support.md#speech-to-text) Isto define a língua falada para ser reconhecida, sobressaltendo o padrão `en-us` . Isto também instrui o canal Direct Line Speech a usar uma voz alemã predefinida para a resposta Bot.
2. Feche a página de definições e clique no botão Reconnect para estabelecer uma nova ligação ao seu bot de eco.
3. Clique no botão do microfone e diga uma frase em alemão. Verá o texto reconhecido e o bot de eco respondendo com a voz alemã padrão.

### <a name="change-the-default-bot-voice"></a>Mude a voz padrão do bot

Selecionar a voz de texto para voz e controlar a pronúncia pode ser feita se o Bot especificar a resposta sob a forma de uma Linguagem de Marcação de [Síntese](speech-synthesis-markup.md) de Fala (SSML) em vez de um texto simples. O bot de eco não usa SSML, mas podemos facilmente modificar o código para o fazer. No exemplo abaixo, adicionamos SSML à resposta do echo bot, de modo que a voz alemã Stefan Apollo (uma voz masculina) será usada em vez da voz feminina padrão. Consulte a lista de [Vozes Padrão](language-support.md#standard-voices) e [Vozes Neurais](language-support.md#neural-voices) suportadas para o seu idioma.

1. Vamos começar por `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` abrir.
2. Localize estas duas linhas:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Substitua-as por:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Construa a sua solução no Visual Studio e corrija quaisquer erros de construção.

O segundo argumento do método 'MessageFactory.Text' define o [campo de fala de atividade](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) na resposta bot. Com a alteração acima, foi substituído de texto simples para SSML para especificar uma voz alemã não padrão.

### <a name="redeploy-your-bot"></a>Recolocar o seu bot

Agora que fez a mudança necessária para o bot, o próximo passo é reeditá-lo ao seu Serviço de Aplicações Azure e experimentá-lo:

1. Na janela Solution Explorer, clique à direita no projeto **EchoBot** e **selecione Publicar**.
2. A configuração de implementação anterior já foi carregada como padrão. Basta clicar **Em Publicar** ao lado do **EchoBot20190805125647 - Web Deploy**.
3. A mensagem **Publish Succeeded** aparecerá na janela de saída do Estúdio Visual, e uma página web será lançada com a mensagem "O seu bot está pronto!".
4. Abra a aplicação Cliente Assistente de Voz do Windows, clique no botão de definições (ícone de engrenagem superior direita) e certifique-se de que ainda tem `de-de` no campo Idioma.
5. Siga as instruções em [Executar o Cliente Assistente de Voz do Windows](#run-the-windows-voice-assistant-client) para voltar a ligar-se ao seu robô recém-implantado, falar no novo idioma e ouvi-lo responder nesse idioma com a nova voz.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar o eco-bot implantado neste tutorial, pode removê-lo e todos os seus recursos Azure associados simplesmente eliminando o grupo de recursos Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. A partir do [portal Azure,](https://portal.azure.com)clique em **Grupos** de Recursos a partir da navegação **dos serviços Azure.**
2. Encontre o grupo de recursos nomeado: **SpeechEchoBotTutorial-ResourceGroup**. Clique nos três pontos (...).
3. Selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa a sua própria app de clientes com o Speech SDK](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>Ver também

* Implantação numa [região de Azure perto de si](https://azure.microsoft.com/global-infrastructure/locations/) para ver a melhoria do tempo de resposta do bot
* Implantação numa [região do Azure que suporta vozes neurais de alta qualidade](./regions.md#neural-and-standard-voices)
* Preços associados ao canal Direct Line Speech:
  * [Preços do Serviço bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Construindo e implantando o seu próprio bot ativado por voz:
  * Construa um [bot de estrutura bot.](https://dev.botframework.com/) Registe-o com [o canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech) e [personalize o seu bot para voz](/azure/bot-service/directline-speech-bot)
  * Explore [as soluções existentes do Quadro de Bot:](https://microsoft.github.io/botframework-solutions/index)Construa um [assistente virtual](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) e [estenda-o ao Discurso de Linha Direta](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
