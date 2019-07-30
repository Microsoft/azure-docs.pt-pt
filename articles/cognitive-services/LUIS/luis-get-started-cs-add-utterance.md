---
title: Alterar, treinar aplicativo, C# -Luis
titleSuffix: Azure Cognitive Services
description: Neste início rápido do C#, adicione expressões de exemplo a uma aplicação de Automatização de Casa e treine a aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 55251a553921e947e7c6522677e51e4eb1d3fa5b
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619946"
---
# <a name="quickstart-change-model-using-c"></a>Início rápido: Alterar modelo usandoC#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Linguagem de programação do C# instalada.
* Pacotes NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) e [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Criar código de início rápido 

No Visual Studio, crie um novo aplicativo de **console da área de trabalho clássica do Windows** usando o .NET Framework. Nomeie o projeto `ConsoleApp1`.

![Tipo de projeto do Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Adicionar a dependência System.Web

O projeto do Visual Studio precisa de **System.Web**. Na Gerenciador de Soluções, clique com o botão direito do mouse em **referências** e selecione **Adicionar referência** na seção assemblies.

![Adicionar referência System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Adicionar outras dependências

O projeto do Visual Studio precisa de **JsonFormatterPlus** e **CommandLineParser**. No Explorador de Soluções, clique com o botão direito do rato em **Referências** e, em seguida, clique em **Gerir Pacotes NuGet...** . Procure e adicione cada um dos dois pacotes. 

![Adicionar dependências de terceiros](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Escrever o código C#
O ficheiro **Program.cs** deve ser:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Atualize as dependências para que sejam:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Adicione os IDs e as cadeias do LUIS à classe **Programa**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Adicione a classe para gerir parâmetros da linha de comandos para a classe **Programa**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Adicione o método de pedido GET à classe **Programa**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Adicione o método de pedido POST à classe **Programa**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Adicione expressões de exemplo do método de ficheiro à classe **Programa**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Depois das alterações serem aplicadas ao modelo, treine-o. Adicione o método à classe **Programa**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

O treino não pode ser concluído imediatamente, veja o estado para verificar se o treino foi concluído. Adicione o método à classe **Programa**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Para gerir os argumentos de linha de comandos, adicione o código principal. Adicione o método à classe **Programa**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Copiar utterances.json para o diretório de saída

No Gerenciador de soluções, adicione o `utterances.json` clicando com o botão direito do mouse no nome do projeto do Gerenciador de soluções, selecionando **Adicionar**e, em seguida, selecionando **Item existente**. Selecione o `utterances.json` arquivo. Isso adiciona o arquivo ao projeto. Em seguida, ele precisa ser adicionado ao diretório de saída. Clique com o botão `utterances.json` direito do mouse no e selecione **Propriedades**. Na janela de propriedades, marque a **Ação de Compilação** de `Content` e **Copiar para Diretório de Saída** de `Copy Always`.  

![Marcar o ficheiro JSON como conteúdo](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Compilar código

Compile código no Visual Studio. 

## <a name="run-code"></a>Executar código

No diretório /bin/Debug do projeto, execute a aplicação a partir de uma linha de comandos. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

Esta linha de comandos apresenta os resultados da chamada à API de expressões a adicionar. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado o início rápido, remova todos os ficheiros criados neste início rápido. 

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Criar uma aplicação LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md) 
