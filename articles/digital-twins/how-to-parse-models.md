---
title: Analisar e validar modelos
titleSuffix: Azure Digital Twins
description: Aprenda a usar a biblioteca de parser para analisar os modelos DTDL.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 155566a125485fda326f9f5e02d4aead0ffe30e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560750"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Parse e valide modelos com a biblioteca de parser DTDL

[Os modelos](concepts-models.md) em Azure Digital Twins são definidos usando a linguagem de definição de gémeos digitais baseada em JSON-LD (DTDL). **É aconselhável validar os seus modelos offline antes de os enviar para a sua instância Azure Digital Twins.**

Para o ajudar a fazê-lo, uma biblioteca de análise DTDL do lado do cliente .NET é fornecida no NuGet: [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Pode utilizar a biblioteca parser diretamente no seu código C# ou utilizar o projeto de amostra de código agnóstico linguístico que é construído na biblioteca de parser: [**amostra de validador DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Utilize a amostra de validador DTDL

O [**DTDL Validator**](/samples/azure-samples/dtdl-validator/dtdl-validator) é um projeto de amostra que pode validar documentos de modelo para garantir que o DTDL é válido. É construído na biblioteca .NET parser e é linguagem-agnóstico. Pode obtê-lo com o botão *Download ZIP* no link da amostra.

O código de origem mostra exemplos de como utilizar a biblioteca de parser. Pode utilizar a amostra validador como utilitário de linha de comando para validar uma árvore de diretório de ficheiros DTDL. Também fornece um modo interativo.

Na pasta para a amostra DTDL Validador, consulte o ficheiro *readme.md* para obter instruções sobre como embalar a amostra numa execução autossuficiente.

Depois de ter construído um pacote autónomo e adicionado o executável ao seu caminho, pode executar o validador com este comando numa consola na sua máquina:

```cmd/sh
DTDLValidator
```

Com as opções predefinidos, a amostra procurará `*.json` ficheiros no diretório atual e em todas as subdireições. Também pode adicionar a seguinte opção para ter a pesquisa da amostra no diretório indicado e todas as subdiretórios para ficheiros com a extensão *.dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Pode adicionar a `-i` opção para a amostra entrar no modo interativo:

```cmd/sh
DTDLValidator -i
```

Para obter mais informações sobre esta amostra, consulte o código de origem ou corra `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Utilize a biblioteca .NET parser 

A biblioteca [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) proporciona acesso de modelo às definições de DTDL, atuando essencialmente como o equivalente à reflexão C# para dTDL. Esta biblioteca pode ser utilizada independentemente de qualquer [SDK Azure Digital Twins](how-to-use-apis-sdks.md), especialmente para validação DTDL num editor visual ou de texto. É útil para garantir que os ficheiros de definição do seu modelo são válidos antes de tentar enviá-los para o serviço.

Para utilizar a biblioteca de parser, fornece-lhe um conjunto de documentos DTDL. Normalmente, você recuperaria estes documentos de modelo do serviço, mas também poderia tê-los disponíveis localmente, se o seu cliente fosse responsável pelo upload do serviço em primeiro lugar. 

Aqui está o fluxo de trabalho geral para a utilização do parser:
1. Recupere alguns ou todos os documentos DTDL do serviço.
2. Passe os documentos DTDL devolvidos e na memória para o analisador.
3. O parser validará o conjunto de documentos que lhe foram transmitidos e devolverá informações detalhadas sobre erros. Esta capacidade é útil em cenários de editores.
4. Utilize as APIs do parser para continuar a analisar os modelos incluídos no conjunto de documentos. 

As capacidades do parser incluem:
* Obtenha todas as interfaces de modelo implementadas (o conteúdo da secção da `extends` interface).
* Obtenha todas as propriedades, telemetria, comandos, componentes e relacionamentos declarados no modelo. Este comando também recebe todos os metadados incluídos nestas definições, e tem em conta a herança `extends` (secções).
* Obtenha todas as definições de modelos complexas.
* Determine se um modelo é atribuível a partir de outro modelo.

> [!NOTE]
> Os dispositivos [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) utilizam uma pequena variante de sintaxe para descrever a sua funcionalidade. Esta variante de sintaxe é um subconjunto semânticamente compatível do DTDL que é usado em Azure Digital Twins. Ao utilizar a biblioteca parser, não precisa de saber qual variante de sintaxe foi usada para criar o DTDL para o seu gémeo digital. O parser irá sempre, por defeito, devolver o mesmo modelo para a sintaxe PnP e Azure Digital Twins.

### <a name="code-with-the-parser-library"></a>Código com a biblioteca parser

Pode utilizar a biblioteca parser diretamente, para coisas como validar modelos na sua própria aplicação ou para gerar UI dinâmicos, orientados por modelos, dashboards e relatórios.

Para suportar o exemplo de código parser abaixo, considere vários modelos definidos numa instância Azure Digital Twins:

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

O código que se segue mostra um exemplo de como usar a biblioteca parser para refletir sobre estas definições em C#:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Passos seguintes

Assim que terminar de escrever os seus modelos, veja como os fazer upload (e fazer outras operações de gestão) com as APIs digitalTwinsModels:
* [*Como fazer: Gerir os modelos DTDL*](how-to-manage-model.md)