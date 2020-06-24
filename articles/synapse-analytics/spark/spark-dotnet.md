---
title: Utilize .NET para Apache Spark com Azure Synapse Analytics
description: Aprenda a usar .NET e Apache Spark para fazer processamento de lotes, streaming em tempo real, machine learning e escrever consultas ad-hoc em cadernos Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: f71d231f01f2f19bd63fb9ec8c32b98fcb3e9aee
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194727"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Utilize .NET para Apache Spark com Azure Synapse Analytics

[.NET for Apache Spark](https://dot.net/spark) fornece suporte gratuito, de código aberto e cross-platform .NET para a Spark. 

Fornece encadernações .NET para a Spark que lhe permitem aceder a APIs de Faísca através de C# e F#. Com .NET para Apache Spark, também tem a capacidade de escrever e executar funções definidas pelo utilizador para Spark escrita em .NET. As APIs .NET para spark permitem-lhe aceder a todos os aspetos dos DataFrames de Faíscas que o ajudam a analisar os seus dados, incluindo Spark SQL, Delta Lake e Structured Streaming.

Pode analisar dados com .NET para Apache Spark através de definições de trabalho em lote spark ou com cadernos Azure Synapse Analytics interativos. Neste artigo, você aprende a usar .NET para Apache Spark com Azure Synapse usando ambas as técnicas.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Submeta trabalhos de lote usando a definição de trabalho spark

Visite o tutorial para aprender a usar a Azure Synapse Analytics para criar definições de [emprego apache spark para piscinas de Faíscas sinapses.](apache-spark-job-definitions.md) Caso não tenha embalado a sua aplicação para submeter ao Azure Synapse, complete os seguintes passos.

1. Execute os seguintes comandos para publicar a sua aplicação. Não se esqueça de substituir *o mySparkApp* pelo caminho para a sua aplicação.

   **Nas janelas:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

   **Em Linux:**

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET para Apache Spark em Azure Synapse Analytics cadernos 

Os cadernos são uma ótima opção para prototipagem do seu .NET para os oleodutos e cenários apache spark. Pode começar a trabalhar com, compreender, filtrar, exibir e visualizar os seus dados de forma rápida e eficiente. Engenheiros de dados, cientistas de dados, analistas de negócios e engenheiros de machine learning são todos capazes de colaborar sobre um documento interativo partilhado. Você vê resultados imediatos da exploração de dados, e pode visualizar os seus dados no mesmo caderno.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Como usar .NET para cadernos Apache Spark

Quando cria um novo caderno, escolhe um núcleo linguístico que deseja expressar a sua lógica de negócio. Existe suporte kernel para várias línguas, incluindo C#.

Para utilizar .NET para Apache Spark no seu caderno Azure Synapse Analytics, selecione **.NET Spark (C#)** como seu núcleo e prenda o caderno a uma piscina spark existente.

O caderno .NET Spark baseia-se nas experiências interativas .NET e proporciona experiências interativas C# com a capacidade de utilizar .NET para Spark fora da caixa com a variável de sessão Spark `spark` já predefinida.

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET para apache spark c# recursos de kernel

As seguintes funcionalidades estão disponíveis quando utiliza .NET para Apache Spark no caderno Azure Synapse Analytics:

* DECLARATIVO HTML: Gere a saída das suas células utilizando a sintaxe HTML, como cabeçalhos, listas de balas e até mesmo exibição de imagens.
* Declarações simples de C# (tais como atribuições, impressão para consola, lançamento de exceções, e assim por diante).
* Blocos de código C# multi-linha (como se declarações, circuitos de foreach, definições de classe, e assim por diante).
* Acesso à biblioteca C# padrão (como System, LINQ, Enumerables, e assim por diante).
* Suporte para [características linguísticas C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* 'faísca' como uma variável pré-definida para lhe dar acesso à sua sessão De Faísca Apache.
* Suporte para definir [funções definidas pelo utilizador .NET que podem ser executadas dentro do Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Suporte para visualizar a produção dos seus trabalhos spark usando diferentes gráficos (como linha, bar ou histograma) e layouts (como simples, sobreposto, e assim por diante) usando a `XPlot.Plotly` biblioteca.
* Capacidade de incluir pacotes NuGet no seu caderno C#.

## <a name="next-steps"></a>Passos seguintes

* [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interativo](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
