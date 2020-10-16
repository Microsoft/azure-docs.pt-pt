---
title: Use Personalizar no chat bot - Personalizer
description: Personalize um bot de chat C# .NET com um loop Personalizer para fornecer o conteúdo correto a um utilizador com base em ações (com funcionalidades) e funcionalidades de contexto.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ae22294d86ab65be0f09b734735885177c1cf63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777314"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Tutorial: Use personalizar em .NET chat bot

Utilize um bot de chat C# .NET com um loop Personalizer para fornecer o conteúdo correto a um utilizador. Este bot de chat sugere um café ou chá específico para um utilizador. O utilizador pode aceitar ou rejeitar essa sugestão. Isto dá informações personalizadas para ajudar a tornar a próxima sugestão mais apropriada.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurar recursos do Azure
> * Configurar e executar bot
> * Interaja com bot usando Bot Framework Emulator
> * Entenda onde e como o bot usa Personalizer


## <a name="how-does-the-chat-bot-work"></a>Como funciona o chat bot?

Um chat bot é tipicamente uma conversa de ida e volta com um utilizador. Este chat bot específico usa o Personalizer para selecionar a melhor ação (café ou chá) para oferecer ao utilizador. Personalizer usa a aprendizagem de reforço para fazer essa seleção.

O chat bot precisa de gerir as voltas na conversa. O bot de chat usa [o Bot Framework](https://github.com/microsoft/botframework-sdk) para gerir a arquitetura e conversa bot e usa o Serviço Cognitivo, [Compreensão da Linguagem](../LUIS/index.yml) (LUIS), para entender a intenção da linguagem natural do utilizador.

O chat bot é um site com uma rota específica disponível para responder a pedidos, `http://localhost:3978/api/messages` . Você pode usar o emulador de bot para interagir visualmente com o bot de chat em execução enquanto você está desenvolvendo um bot localmente.

### <a name="user-interactions-with-the-bot"></a>Interações do utilizador com o bot

Este é um simples chat bot que permite introduzir consultas de texto.

|O utilizador introduz texto|Bot responde com texto|Descrição da ação que bot toma para determinar texto de resposta|
|--|--|--|
|Nenhum texto inserido - bot começa a conversa.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|O bot inicia a conversa com texto instrutivo e permite-lhe saber qual é o contexto: `Tuesday` `Snowy` . . .|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Determine a intenção de consulta usando LUIS e, em seguida, mostrar opções de menu de itens de café e chá. As características das ações são |
|`What do you suggest`|`How about Latte?`|Determine a intenção de consulta usando LUIS, em seguida, ligue **para Rank API,** e mostrar a escolha de topo como uma questão `How about {response.RewardActionId}?` . Também exibe chamada e resposta JSON para fins de ilustração.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Determine a intenção de consulta usando LUIS, em seguida, ligue para **a Reward API** com recompensa `1` de, exibe chamada json e resposta para fins de ilustração.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Determine a intenção de consulta usando LUIS, em seguida, ligue para **a Reward API** com recompensa `0` de, exibe chamada json e resposta para fins de ilustração.|
|`Reset`|Devolve texto instrutivo.|Determine a intenção de consulta utilizando o LUIS, em seguida, exibe o texto instrutivo e repõe o contexto.|


### <a name="personalizer-in-this-bot"></a>Personalizar neste bot

Este chat bot usa Personalizer para selecionar a ação de topo (café ou chá específico), com base numa lista de _ações_ (algum tipo de conteúdo) e características de contexto.

O bot envia a lista de ações, juntamente com as características de contexto, para o loop Personalizer. O personalização devolve a melhor ação ao seu bot, que o seu bot exibe.

Neste tutorial, as **ações** são tipos de café e chá:

|Café|Chá|
|--|--|
|Cappuccino<br>Expresso<br>Latte<br>Rio Mocha|GreenTea<br>Rooibos|

**Classificação API:** Para ajudar o Personaler a conhecer as suas ações, o bot envia o seguinte com cada pedido de API de classificação:

* Ações _com características_
* Características de contexto

Uma **característica** do modelo é informação sobre a ação ou contexto que pode ser agregado (agrupado) entre membros da sua base de utilizadores de chat bot. Uma funcionalidade _não é_ individualmente específica (como um ID do utilizador) ou altamente específica (por exemplo, uma hora exata do dia).

As funcionalidades são usadas para alinhar ações com o contexto atual no modelo. O modelo é uma representação do conhecimento passado do Personalizador sobre ações, contexto e suas características que lhe permitem tomar decisões educadas.

O modelo, incluindo funcionalidades, é atualizado num horário baseado na definição **de frequência de atualização** do seu Modelo no portal Azure.

As funcionalidades devem ser selecionadas com o mesmo planeamento e design que se aplicaria a qualquer esquema ou modelo na sua arquitetura técnica. Os valores de funcionalidade podem ser definidos com lógica de negócio ou sistemas de terceiros.

> [!CAUTION]
> As funcionalidades desta aplicação são para demonstração e podem não ser necessariamente as melhores funcionalidades para usar numa aplicação web para o seu caso de utilização.

#### <a name="action-features"></a>Características de ação

Cada ação (item de conteúdo) tem características para ajudar a distinguir o item do café ou do chá.

As funcionalidades não são configuradas como parte da configuração do loop no portal Azure. Em vez disso, são enviados como um objeto JSON com cada chamada API rank. Isto permite flexibilidade para que as ações e as suas características cresçam, mudem e encolhem ao longo do tempo, o que permite ao Personaler seguir tendências.

As características do café e do chá incluem:

* Localização de origem de grão de café como Quénia e Brasil
* O café ou o chá são orgânicos?
* Assado claro ou escuro de café

Enquanto o café tem três características na lista anterior, o chá só tem uma. Apenas passe recursos para Personalizer que façam sentido para a ação. Não passe num valor vazio por um recurso se não se aplicar à ação.

#### <a name="context-features"></a>Características de contexto

As funcionalidades de contexto ajudam o Personalizador a compreender o contexto do ambiente, como o dispositivo de exibição, o utilizador, a localização e outras funcionalidades que são relevantes para o seu caso de utilização.

O contexto deste chat bot inclui:

* Tipo de tempo (nevado, chuvoso, ensolarado)
* Dia da semana

A seleção de funcionalidades é aleatória neste chat bot. Num bot real, utilize dados reais para as suas funcionalidades de contexto.

### <a name="design-considerations-for-this-bot"></a>Considerações de design para este bot

Há algumas advertências a notar sobre esta conversa:
* **Interação bot**: A conversa é muito simples porque está demonstrando Rank and Reward em um caso de uso simples. Não demonstra a funcionalidade completa do Bot Framework SDK ou do Emulator.
* **Personalização**: As funcionalidades são selecionadas aleatoriamente para simular a utilização. Não aleatoriamente aleatoriamente características num cenário de personalização de produção.
* **Compreensão linguística (LUIS)**: As poucas expressões de exemplo do modelo LUIS destinam-se apenas a esta amostra. Não utilize tão poucas expressões de exemplo na sua aplicação LUIS de produção.


## <a name="install-required-software"></a>Instalar o software necessário
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). O repositório de amostras transferíveis inclui instruções se preferir utilizar o CLI .NET Core.
- [O Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) é uma aplicação para desktop que permite aos desenvolvedores de bots testar e depurar os seus bots na localidade ou correr remotamente através de um túnel.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Descarregue o código de amostra do chat bot

O chat bot está disponível no repositório de amostras personalizantes. Clone ou [descarregue](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) o repositório e, em seguida, abra a amostra no diretório com o `/samples/ChatbotExample` Visual Studio 2019.

Para clonar o repositório, utilize o seguinte comando Git numa casca bash (terminal).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Criar e configurar recursos personalizadores e LUIS

### <a name="create-azure-resources"></a>Criar recursos do Azure

Para utilizar este chat bot, você precisa criar recursos Azure para Personalizar e Compreensão de Linguagem (LUIS).

* [Criar recursos LUIS.](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) Selecione **ambos** no passo de criação porque você precisa tanto de recursos de autoria e previsão.
* [Crie o recurso Personalizer](how-to-create-resource.md) e copie a chave e o ponto final do portal Azure. Terá de definir estes valores no `appsettings.json` ficheiro do projeto .NET.

### <a name="create-luis-app"></a>Criar app LUIS

Se é novo no LUIS, tem de [se inscrever](https://www.luis.ai) e migrar imediatamente a sua conta. Não precisa de criar novos recursos, em vez de selecionar os recursos que criou na secção anterior deste tutorial.

1. Para criar uma nova aplicação LUIS, no [portal LUIS,](https://www.luis.ai)selecione o seu recurso de subscrição e autoria.
1. Em seguida, ainda na mesma página, selecione **+ Nova aplicação para conversação,** em **seguida, Import as JSON**.
1. No diálogo pop-up, selecione **Escolher o ficheiro** e, em seguida, selecione o `/samples/ChatbotExample/CognitiveModels/coffeebot.json` ficheiro. Insira o `Personalizer Coffee bot` nome.
1. Selecione o botão **Train** na navegação superior direita do portal LUIS.
1. Selecione o botão **Publicar** para publicar a aplicação na **ranhura de Produção** para o tempo de execução da previsão.
1. **Selecione Gerir**e, em seguida, **Configurações**. Copiar o valor do ID da **aplicação.** Terá de definir este valor no `appsettings.json` ficheiro do projeto .NET.
1. Ainda na secção **Gerir,** selecione **Recursos Azure**. Isto exibe os recursos associados na aplicação.
1. **Selecione Adicionar recurso de previsão**. No diálogo pop-up, selecione a sua subscrição e o recurso de previsão criado numa secção anterior deste tutorial e, em seguida, selecione **'Fazer**' ).
1. Copiar os valores da **chave primária** e **URL de ponto final**. Terá de definir estes valores no `appsettings.json` ficheiro do projeto .NET.

### <a name="configure-bot-with-appsettingsjson-file"></a>Configure bot com appsettings.jsno arquivo

1. Abra o ficheiro de solução chat `ChatbotSamples.sln` bot, com o Visual Studio 2019.
1. Aberto `appsettings.json` no diretório de raiz do projeto.
1. Defina as cinco definições copiadas na secção anterior deste tutorial.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Construir e executar o bot

Uma vez configurado o `appsettings.json` , você está pronto para construir e executar o chat bot. Quando o fizer, um navegador abre para o site de execução, `http://localhost:3978` .

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Screenshot do navegador exibindo o site do chat bot.":::

Mantenha o site em funcionamento porque o tutorial explica o que o bot está a fazer, para que possa interagir com o bot.


## <a name="set-up-the-bot-emulator"></a>Configurar o emulador de bot

1. Abra o Emulador De Estrutura bot e selecione **Open Bot**.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Screenshot do navegador exibindo o site do chat bot.":::


1. Configure o bot com o seguinte **URL bot** e, em seguida, selecione **Connect**:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Screenshot do navegador exibindo o site do chat bot.":::

    O emulador liga-se ao bot de chat e exibe o texto instrutivo, juntamente com informações de registo e depuragem úteis para o desenvolvimento local.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Screenshot do navegador exibindo o site do chat bot.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Use o bot no emulador de bot

1. Peça para ver o menu `I would like to see the menu` entrando. O bot de chat exibe os itens.
1. Deixe o bot sugerir um item introduzindo `Please suggest a drink for me.` O emulador exibe o pedido de Rank e resposta na janela de chat, para que possa ver o JSON completo. E o bot faz uma sugestão, algo como `How about Latte?`
1. Resposta que gostaria que aceitasse a melhor seleção classificada do Personalizer, `I like it.` o emulador exibe o pedido de Recompensa com a pontuação de recompensa 1 e resposta na janela de chat, para que possa ver o JSON completo. E o bot responde com `That’s great! I’ll keep learning your preferences over time.` e `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Se responder com `no` a seleção, a pontuação de recompensa de 0 é enviada para Personalizer.


## <a name="understand-the-net-code-using-personalizer"></a>Compreenda o código .NET usando o Personalador

A solução .NET é um simples bot de chat de estrutura bot. O código relacionado com o Personaler está nas seguintes pastas:
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` arquivo para a interação entre bot e Personalizer
* `/samples/ChatbotExample/ReinforcementLearning` - gere as ações e funcionalidades do modelo Personalizer
* `/samples/ChatbotExample/Model` - ficheiros para as ações e funcionalidades do Personaler, e para as intenções do LUIS

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs - trabalhar com o Personaler

A `PersonalizerChatbot` classe é derivada do `Microsoft.Bot.Builder.ActivityHandler` . Tem três propriedades e métodos para gerir o fluxo de conversação.

> [!CAUTION]
> Não copie o código deste tutorial. Utilize o código de amostra no [repositório de amostras do Personaler](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Os métodos pré-fixados com `Send` a gestão da conversa com o bot e o LUIS. Os métodos `ChooseRankAsync` e interagir com o `RewardAsync` Personaler.

#### <a name="calling-rank-api-and-display-results"></a>Chamando API de classificação e mostrar resultados

O método `ChooseRankAsync` constrói os dados JSON para enviar para a API do Ranking personalizador, recolhendo as ações com funcionalidades e funcionalidades de contexto.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Chamando API de recompensa e mostrar resultados

O método `RewardAsync` constrói os dados JSON para enviar para a API de Recompensa do Personaler, determinando a pontuação. A pontuação é determinada a partir da intenção LUIS identificada no texto do utilizador e enviada a partir do `OnTurnAsync` método.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Considerações de design para um bot

Esta amostra destina-se a demonstrar uma solução simples de personalização num bot. O seu caso de utilização pode ser mais complexo.

Se pretender utilizar o Personalizer num bot de produção, planeie:
* Acesso em tempo real ao Personalizer _sempre_ que precisar de uma seleção classificada. A API rank não pode ser empásida ou em cache.  A chamada de recompensa pode ser adiada ou descarregada para um processo separado e se não devolver uma recompensa no período cronometrado, então um valor de recompensa padrão é definido para o evento.
* Cálculo baseado em caso de utilização da recompensa: Este exemplo mostrou duas recompensas de zero e uma sem intervalo entre e nenhum valor negativo para uma pontuação. O seu sistema fez de necessidade de mais pontuação granular.
* Canais bot: Esta amostra utiliza um único canal mas se pretender utilizar mais do que um canal, ou variações de bots num único canal, isso poderá ter de ser considerado como parte das características de contexto do modelo Personalizer.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, limpe os seguintes recursos:

* Apague o seu diretório de projeto de amostra.
* Elimine o seu recurso Personalizer e LUIS no portal Azure.

## <a name="next-steps"></a>Passos seguintes
* [Como funciona o Personalizador](how-personalizer-works.md)
* [Características](concepts-features.md): aprenda conceitos sobre funcionalidades usando com ações e contexto
* [Recompensas](concept-rewards.md): conheça o cálculo das recompensas
