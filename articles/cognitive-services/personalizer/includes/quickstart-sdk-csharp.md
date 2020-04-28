---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 0b09f15eb05a94bb62f4484ac362467fbdb0af8b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188883"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | da biblioteca[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Usando este arranque rápido

Há vários passos para usar este arranque rápido:

* No portal Azure, crie um recurso Personalizer
* No portal Azure, para o recurso Personalizer, na página **de Configuração,** altere a frequência de atualização do modelo para um intervalo muito curto
* Num editor de código, crie um ficheiro de código e edite o ficheiro de código
* Na linha de comando ou terminal, instale o SDK a partir da linha de comando
* Na linha de comando ou terminal, executar o ficheiro de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor ou IDE preferido.

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando dotnet `new` para criar uma nova aplicação de consola com o nome `personalizer-quickstart`. Este comando cria um simples projeto C# "Hello `Program.cs`World" com um único ficheiro de origem: .

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

Dentro do diretório de aplicações, instale a biblioteca de clientes Personalizer para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) que autentica o Azure usando o Microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)em seguida, passe-o para o [cliente. Método de classificação.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) O método Rank devolve uma RankResponse.

Para enviar uma pontuação de recompensa ao Personalizer, crie um [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)e, em seguida, passe-o para o [cliente. Método](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) de recompensa.

Determinar a pontuação da recompensa, neste arranque rápido é trivial. Num sistema produtivo, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e pelo quanto pode ser um processo complexo, que pode decidir mudar ao longo do tempo. Esta decisão de design deve ser uma das decisões primárias na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Personalizer para .NET:

* [Criar um cliente Personalizer](#create-a-personalizer-client)
* [Classificação API](#request-the-best-action)
* [API recompensa](#send-a-reward)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro **Program.cs** no seu editor ou IDE preferido. Substitua o `using` código existente `using` pelas seguintes diretivas:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos personalizantes

Na aula de **Programa,** crie variáveis para a chave Azure do seu `PERSONALIZER_RESOURCE_KEY` recurso `PERSONALIZER_RESOURCE_ENDPOINT`e ponto final retirado das variáveis ambientais, nomeadas e . Se criou as variáveis ambientais após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde neste arranque rápido.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente Personalizer

Em seguida, crie um método para devolver um cliente Personalizer. O parâmetro para o `PERSONALIZER_RESOURCE_ENDPOINT` método é o e `PERSONALIZER_RESOURCE_KEY`o ApiKey é o .

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Obtenha itens alimentares como ações de classificação

As ações representam as escolhas de conteúdo a partir das quais pretende que o Personalizer selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e suas funcionalidades. 

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Obtenha preferências de utilizador para contexto

Adicione os seguintes métodos à classe Programa para obter a entrada de um utilizador da linha de comando para a hora do dia e a preferência alimentar atual. Estes serão usados como características de contexto.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Ambos os `GetKey` métodos utilizam o método para ler a seleção do utilizador a partir da linha de comando.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [De Rank](#request-the-best-action) e [Reward.](#send-a-reward) Neste arranque rápido, cada chamada de Rank, para personalizar o conteúdo, é seguida por uma chamada de Recompensa para dizer ao Personalizer o quão bem o serviço foi executado.

O código seguinte passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personalizer para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma pontuação de recompensa ao Personalizer sinalizando o quão bem o serviço fez na sua seleção.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-food-items-as-rankable-actions)antes de executar o ficheiro de código:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido de Rank, o programa pede `currentContent` às preferências do utilizador para criar uma das escolhas de conteúdo. O processo pode criar conteúdo para excluir `excludeActions`das ações, mostradas como . O pedido de Rank necessita das ações e suas funcionalidades, funcionalidades atuais Contexto, exclusão DeAções e um ID de evento único, para receber a resposta.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e funcionalidades](../concepts-features.md) pode ser uma questão não trivial.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID de evento único e a pontuação de recompensa como o valor numérico para a API recompensa.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Reward](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação `run` com o comando dotnet a partir do seu diretório de aplicações.

```console
dotnet run
```

![O programa quickstart faz algumas perguntas para reunir as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código fonte para este arranque rápido](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) está disponível no repositório de amostras De Personaler GitHub.
