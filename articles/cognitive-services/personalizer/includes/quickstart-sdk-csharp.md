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
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122888"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [do NuGet ( do pacote)de](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [amostras biblioteca](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Usando este guia de início rápido

Há várias etapas para usar este guia de início rápido:

* No portal do Azure, criar um recurso personalizador
* Na portal do Azure, para o recurso personalizador, na página **configuração** , altere a frequência de atualização do modelo para um intervalo muito curto
* Em um editor de código, crie um arquivo de código e edite o arquivo de código
* Na linha de comando ou terminal, instale o SDK da linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido.

Em uma janela de console (como cmd, PowerShell ou bash), use o comando dotnet `new` para criar um novo aplicativo de console com o nome `personalizer-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: `Program.cs`.

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

## <a name="install-the-sdk"></a>Instalar o SDK

No diretório do aplicativo, instale a biblioteca de cliente do personalizador para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

O cliente do personalizador é um objeto [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie um [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)e, em seguida, passe-o para o [cliente. ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview)Método de classificação. O método Rank retorna um RankResponse.

Para enviar uma pontuação de recompensa para o personalizador, crie um [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)e, em seguida, passe-o para o [cliente. ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)Método de recompensa.

A determinação da Pontuação de recompensa, neste guia de início rápido, é trivial. Em um sistema de produção, a determinação do que afeta a [Pontuação de recompensa](../concept-rewards.md) e o quanto pode ser um processo complexo, que você pode decidir alterar ao longo do tempo. Essa decisão de design deve ser uma das principais decisões em sua arquitetura de personalização.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente do personalizador para .NET:

* [Criar um cliente personalizado](#create-a-personalizer-client)
* [API de classificação](#request-the-best-action)
* [A API de recompensa](#send-a-reward)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no seu editor ou IDE preferido. Substitua o código de `using` existente pelas seguintes diretivas de `using`:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do personalizador

Na classe **programa** , crie variáveis para a chave do Azure do recurso e o ponto de extremidade extraído das variáveis de ambiente, chamadas `PERSONALIZER_RESOURCE_KEY` e `PERSONALIZER_RESOURCE_ENDPOINT`. Se você criou as variáveis de ambiente depois que o aplicativo é iniciado, o editor, IDE ou shell que o executa precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente neste guia de início rápido.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizado

Em seguida, crie um método para retornar um cliente personalizado. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Obter itens de alimentos como ações de classificação

Ações representam as opções de conteúdo das quais você deseja que o personalizado selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e seus recursos.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Obter preferências do usuário para o contexto

Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual. Eles serão usados como recursos de contexto.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Ambos os métodos usam o método `GetKey` para ler a seleção do usuário na linha de comando.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizagem

O loop de aprendizagem personalizador é um ciclo de chamadas de [classificação](#request-the-best-action) e [recompensa](#send-a-reward) . Neste guia de início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao personalizado o quão bem o serviço foi executado.

O código a seguir percorre um ciclo de solicitar ao usuário suas preferências na linha de comando, enviando essas informações ao personalizador para selecionar a melhor ação, apresentar a seleção ao cliente para escolher entre a lista e, em seguida, enviar uma pontuação de recompensa para Personalizar a sinalização do quão bem o serviço fez em sua seleção.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-food-items-as-rankable-actions), antes de executar o arquivo de código:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Solicitar a melhor ação

Para concluir a solicitação de classificação, o programa solicita as preferências do usuário para criar uma `currentContent` das opções de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions`. A solicitação de classificação precisa das ações e seus recursos, recursos de currentContext, excludeActions e uma ID de evento exclusiva, para receber a resposta.

Este guia de início rápido tem recursos de contexto simples de hora do dia e preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa a ser enviada na solicitação de recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico à seleção e, em seguida, envia a ID de evento exclusiva e a pontuação de recompensa como o valor numérico para a API de recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o comando dotnet `run` no diretório do aplicativo.

```console
dotnet run
```

![O programa de início rápido faz algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, e fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código-fonte deste guia de início rápido](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) está disponível no repositório GitHub de exemplos do personalizador.
