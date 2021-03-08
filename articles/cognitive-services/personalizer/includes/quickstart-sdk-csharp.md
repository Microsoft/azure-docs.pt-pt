---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: f98c34a53de60adc7866c1b57918232b966a0783
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444175"
---
[Documentação de referência](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title=" crie um recurso Personalizer no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API Personalizada. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE.

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando dotnet `new` para criar uma nova aplicação de consola com o nome `personalizer-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: `Program.cs` .

```console
dotnet new console -n personalizer-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Personalizer para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

A partir do diretório do projeto, abra o `Program.cs` arquivo no seu editor preferido ou IDE. Adicione as seguintes diretivas:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest)em seguida, passe-o ao [cliente. Método de classificação.](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank) O método Rank devolve um RankResponse.

Para enviar uma pontuação de recompensa para o Personaler, crie um [RewardRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest)e, em seguida, passe-o ao [cliente. Método de recompensa.](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward)

Determinar a pontuação da recompensa, neste arranque rápido é trivial. Num sistema de produção, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e o quanto pode ser um processo complexo, que poderá decidir mudar com o tempo. Esta decisão de design deve ser uma das principais decisões da sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca cliente Personalizer para .NET:

* [Criar um cliente personalizante](#authenticate-the-client)
* [Classificação API](#request-the-best-action)
* [Recompensa API](#send-a-reward)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Nesta secção, farás duas coisas:
* Especifique a sua chave e ponto final
* Criar um cliente personalizante

Comece por adicionar as seguintes linhas à sua aula de Programa. Certifique-se de adicionar a sua chave e ponto final do seu recurso Personalizer.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

Em seguida, adicione um método ao seu programa para criar um novo cliente Personalizer.

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>Obtenha itens alimentares como ações classificadas

As ações representam as escolhas de conteúdo a partir das quais deseja que o Personaler selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e as suas características. 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
        },

        new RankableAction
        {
            Id = "ice cream",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
        },

        new RankableAction
        {
            Id = "juice",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
        },

        new RankableAction
        {
            Id = "salad",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>Obtenha preferências de utilizador para o contexto

Adicione os seguintes métodos à classe Programa para obter a entrada de um utilizador da linha de comando para a hora do dia e a preferência alimentar atual. Estes serão usados como características de contexto.

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

Ambos os métodos utilizam o `GetKey` método para ler a seleção do utilizador a partir da linha de comando.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [Rank](#request-the-best-action) and [Reward.](#send-a-reward) Neste quickstart, cada chamada Rank, para personalizar o conteúdo, é seguida por uma chamada Reward para dizer ao Personaler o quão bem o serviço foi executado.

O código que se segue passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personaler para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma pontuação de recompensa para Personalizer sinalizando o quão bem o serviço se saiu na sua seleção.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-food-items-as-rankable-actions)antes de executar o ficheiro de código:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido rank, o programa pede às preferências do utilizador para criar uma `currentContext` das escolhas de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions` . O pedido rank precisa das ações e suas funcionalidades, funcionalidades atualescontexto, exclusões Deacções, e um ID de evento único, para receber a resposta.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e características](../concepts-features.md) pode ser uma questão não trivial.

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID do evento único e a pontuação da recompensa como o valor numérico para a API Reward.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Decompras](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação com o `run` comando dotnet a partir do seu diretório de candidaturas.

```console
dotnet run
```

![O programa quickstart faz algumas perguntas para recolher as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código fonte para este arranque rápido](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) está disponível.
