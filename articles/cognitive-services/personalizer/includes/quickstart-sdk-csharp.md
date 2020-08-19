---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: fdae79912e6fe3bf2f7d55b7405cb7883e484c47
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602506"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Usando este arranque rápido

Existem vários passos para usar este arranque rápido:

* No portal Azure, crie um recurso Personalizer
* No portal Azure, para o recurso Personalizer, na página **Configuração,** altere a frequência de atualização do modelo para um intervalo muito curto
* Num editor de código, crie um ficheiro de código e edite o ficheiro de código
* Na linha de comando ou terminal, instale o SDK a partir da linha de comando
* Na linha de comando ou terminal, execute o ficheiro de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

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

## <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório de aplicações, instale a biblioteca cliente Personalizer para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)em seguida, passe-o ao [cliente. Método de classificação.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) O método Rank devolve um RankResponse.

Para enviar uma pontuação de recompensa para o Personaler, crie um [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)e, em seguida, passe-o ao [cliente. Método de recompensa.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)

Determinar a pontuação da recompensa, neste arranque rápido é trivial. Num sistema de produção, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e o quanto pode ser um processo complexo, que poderá decidir mudar com o tempo. Esta decisão de design deve ser uma das principais decisões da sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca cliente Personalizer para .NET:

* [Criar um cliente personalizante](#create-a-personalizer-client)
* [Classificação API](#request-the-best-action)
* [Recompensa API](#send-a-reward)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro **Program.cs** no seu editor preferido ou IDE. Substitua o código existente `using` pelas `using` seguintes diretivas:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recurso personalizador

Na classe **Programa,** edite as variáveis chave e ponto final para o topo do ficheiro de código para a chave Azure e ponto final do seu recurso. 

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizante

Em seguida, crie um método para devolver um cliente Personalizer. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY` .

[!code-csharp[Create the Personalizer client](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Obtenha itens alimentares como ações classificadas

As ações representam as escolhas de conteúdo a partir das quais deseja que o Personaler selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e as suas características. 

[!code-csharp[Food items as actions](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Obtenha preferências de utilizador para o contexto

Adicione os seguintes métodos à classe Programa para obter a entrada de um utilizador da linha de comando para a hora do dia e a preferência alimentar atual. Estes serão usados como características de contexto.

[!code-csharp[Present time out day preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTastePreference)]

Ambos os métodos utilizam o `GetKey` método para ler a seleção do utilizador a partir da linha de comando.

[!code-csharp[Read user's choice from the command line](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [Rank](#request-the-best-action) and [Reward.](#send-a-reward) Neste quickstart, cada chamada Rank, para personalizar o conteúdo, é seguida por uma chamada Reward para dizer ao Personaler o quão bem o serviço foi executado.

O código que se segue passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personaler para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma pontuação de recompensa para Personalizer sinalizando o quão bem o serviço se saiu na sua seleção.

[!code-csharp[Learning loop](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=mainLoop)]

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-food-items-as-rankable-actions)antes de executar o ficheiro de código:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido rank, o programa pede às preferências do utilizador para criar uma `currentContext` das escolhas de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions` . O pedido rank precisa das ações e suas funcionalidades, funcionalidades atualescontexto, exclusões Deacções, e um ID de evento único, para receber a resposta.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e características](../concepts-features.md) pode ser uma questão não trivial.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID do evento único e a pontuação da recompensa como o valor numérico para a API Reward.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Decompras](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação com o `run` comando dotnet a partir do seu diretório de candidaturas.

```console
dotnet run
```

![O programa quickstart faz algumas perguntas para recolher as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código fonte para este arranque rápido](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) está disponível.
