---
title: Entenda o analisador de modelos Digital Twins Microsoft Docs
description: Como desenvolvedor, aprenda a usar o parser DTDL para validar modelos
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352434"
---
# <a name="understand-the-digital-twins-model-parser"></a>Compreender o parser de modelos de duplos digitais

A Linguagem de Definição de Gémeos Digitais (DTDL) está descrita na [Especificação DTDL.](https://github.com/Azure/opendigitaltwins-dtdl) Os utilizadores podem utilizar o pacote _Digital Twins Model Parser_ NuGet para validar e consultar um modelo definido em vários ficheiros.

## <a name="install-the-dtdl-model-parser"></a>Instale o analisador de modelos DTDL

O parser está disponível em NuGet.org com o ID: [Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Para instalar o parser, utilize qualquer gestor de pacotes NuGet compatível, como o do Visual Studio ou no `dotnet` CLI.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Use o parser para validar um modelo

O modelo que pretende validar pode ser composto por uma ou mais interfaces descritas nos ficheiros JSON. Pode utilizar o analisador para carregar todos os ficheiros numa determinada pasta e utilizar o parser para validar todos os ficheiros como um todo, incluindo quaisquer referências entre os ficheiros:

1. Criar uma `IEnumerable<string>` lista com uma lista de todos os conteúdos do modelo:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instantaneamente o `ModelParser` e `ParseAsync` ligue:

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Verifique se há erros de validação. Se o analisador encontrar algum erro, lança uma `AggregateException` lista de mensagens de erro detalhadas:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Inspecione o `Model` . Se a validação for bem sucedida, pode utilizar a API do analisador de modelos para inspecionar o modelo. O seguinte corte de código mostra como iterar sobre todos os modelos analisados e exibe as propriedades existentes:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Passos seguintes

O modelo parser API revisto neste artigo permite a muitos cenários automatizar ou validar tarefas que dependem de modelos DTDL. Por exemplo, você poderia dinamicamente construir uma UI a partir da informação no modelo.
