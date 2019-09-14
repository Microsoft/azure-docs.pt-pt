---
title: 'Início rápido: Biblioteca de cliente do personalizador para .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do personalizador para .NET usando um loop de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 2fc9e91a34975642a666aa794694cfcc3321c93f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962551"
---
# <a name="quickstart-personalize-client-library-for-net"></a>Início rápido: Personalizar a biblioteca de cliente para .NET

Exiba o conteúdo personalizado neste C# guia de início rápido com o serviço personalizado.

Introdução à biblioteca de cliente do personalizador para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

 * Classifique uma lista de ações para personalização.
 * Relatório de Pontuação de recompensa indicando o sucesso da ação de classificação mais alta.

[](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) |  | [Exemplos](https://github.com/Azure-Samples/cognitive-services-personalizer-samples) de pacote de[código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | da biblioteca de documentação de referência

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-personalizer-azure-resource"></a>Criar um recurso personalizado do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para personalizador usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

<!-- rename TBD_KEY to something meaningful for your service, like TEXT_ANALYTICS_KEY -->
Depois de obter uma chave de sua assinatura ou recurso de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY`para a chave de recurso.
* `PERSONALIZER_RESOURCE_ENDPOINT`para o ponto de extremidade do recurso.

No portal do Azure, os valores de chave e ponto de extremidade estão disponíveis na página **início rápido** .

### <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No recurso personalizado no portal do Azure, altere a **frequência de atualização do modelo** para 10 segundos. Isso treinará o serviço rapidamente, permitindo que você veja como as principais ações são alteradas para cada iteração.

![Alterar a frequência de atualização do modelo](./media/settings/configure-model-update-frequency-settings.png)

Quando um loop do personalizador é instanciado pela primeira vez, não há nenhum modelo, pois não há chamadas de API de recompensa para treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve sempre classificar o conteúdo usando a saída de RewardActionId.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

Em uma janela de console (como cmd, PowerShell ou bash), use o comando dotnet `new` para criar um novo aplicativo de console com o nome `personalizer-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de `Program.cs`origem:. 

```console
dotnet new console -n personalizer-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Instalar o SDK

No diretório do aplicativo, instale a biblioteca de cliente do personalizador para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

O cliente do personalizador é um objeto [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Para solicitar uma classificação do conteúdo, crie um [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)e, em seguida, passe-o para o [cliente. ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview)Método de classificação. O método Rank retorna um RankResponse, que contém o conteúdo classificado. 

Para enviar um recompensa ao personalizador, crie um [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)e, em seguida, passe-o para o [cliente. ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)Método de recompensa. 

A determinação da recompensa, neste guia de início rápido, é trivial. Em um sistema de produção, a determinação do que afeta a [Pontuação de recompensa](concept-rewards.md) e o quanto pode ser um processo complexo, que você pode decidir alterar ao longo do tempo. Essa deve ser uma das principais decisões de design na arquitetura do personalizador. 

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do personalizador para .NET:

* [Criar um cliente personalizado](#create-a-personalizer-client)
* [Solicitar uma classificação](#request-a-rank)
* [Enviar uma recompensa](#send-a-reward)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no seu editor ou IDE preferido. Substitua o código `using` existente pelas seguintes `using` diretivas:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do personalizador

Na classe **programa** , crie variáveis para a chave do Azure do recurso e o ponto de extremidade extraídos das variáveis `PERSONALIZER_RESOURCE_KEY` de `PERSONALIZER_RESOURCE_ENDPOINT`ambiente, nomeadas e. Se você criou as variáveis de ambiente depois que o aplicativo é iniciado, o editor, IDE ou shell que o executa precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente neste guia de início rápido.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizado

Em seguida, crie um método para retornar um cliente personalizado. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-content-choices-represented-as-actions"></a>Obter opções de conteúdo representadas como ações

As ações representam as opções de conteúdo que você deseja que o personalizado classifique. Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Ambos os métodos usam `GetKey` o método para ler a seleção do usuário na linha de comando. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizagem

O loop de aprendizagem personalizador é um ciclo de chamadas de classificação e recompensa. Neste guia de início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao personalizado o quão bem o serviço classificou o conteúdo. 

O código a seguir no `main` método do programa executa um loop em um ciclo de solicitar ao usuário suas preferências na linha de comando, enviando essas informações ao personalizador para classificação, apresentando a seleção classificada ao cliente para escolher entre as e, em seguida, enviar um prêmio para o personalizado sinalizando o quão bem o serviço fez ao classificar a seleção.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Initialize Personalizer client.
    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        // <rank>
        // Get context information from the user.
        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        // Create current context from user specified data.
        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        // Exclude an action for personalizer ranking. This action will be held at its current position.
        // This simulates a business rule to force the action "juice" to be ignored in the ranking.
        // As juice is excluded, the return of the API will always be with a probability of 0.
        IList<string> excludeActions = new List<string> { "juice" };

        // Generate an ID to associate with the request.
        string eventId = Guid.NewGuid().ToString();

        // Rank the actions
        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);
        // </rank>

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        // <reward>
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
        // </reward>

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

## <a name="request-a-rank"></a>Solicitar uma classificação

Para concluir a solicitação de classificação, o programa solicita as preferências do usuário para criar `currentContent` uma das opções de conteúdo. O processo pode criar conteúdo para excluir da classificação, mostrada como `excludeActions`. A solicitação de classificação precisa das ações, currentContext, excludeActions e uma ID de evento de classificação exclusiva (como um GUID) para receber a resposta classificada. 

Este guia de início rápido tem recursos de contexto simples de hora do dia e preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](concept-feature-evaluation.md) [ações e recursos](concepts-features.md) pode ser uma questão não trivial.  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para concluir a solicitação de recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia a ID de evento de classificação exclusiva e o valor numérico para o método de recompensa.

Este início rápido atribui um número simples como um recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o comando `run` dotnet do diretório do aplicativo.

```console
dotnet run
```

![O programa de início rápido faz algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, e fornece a ação principal.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código-fonte deste guia de início rápido](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) está disponível no repositório GitHub de exemplos do personalizador.

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Como o personalizador funciona](how-personalizer-works.md)

* [O que é personalizador?](what-is-personalizer.md)
* [Onde você pode usar o personalizador?](where-can-you-use-personalizer.md)
* [Resolução de problemas](troubleshooting.md)

