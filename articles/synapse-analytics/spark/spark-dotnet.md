---
title: Use .NET para Apache Spark com Azure Synapse Analytics
description: Aprenda a usar .NET e Apache Spark para fazer processamento de lotes, streaming em tempo real, machine learning, e escreva consultas ad-hoc em cadernos Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: fcac7e47570cf10388891f2e9b81da896acc5c02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699341"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Use .NET para Apache Spark com Azure Synapse Analytics

[.NET para Apache Spark](https://dot.net/spark) fornece suporte gratuito, de código aberto e cross-platform .NET para Spark. 

Fornece encadernações .NET para a Spark que lhe permitem aceder a APIs Spark através de C# e F#. Com .NET para Apache Spark, também tem a capacidade de escrever e executar funções definidas pelo utilizador para a Spark escritaem em .NET. As APIs .NET para Spark permitem-lhe aceder a todos os aspetos dos Spark DataFrames que o ajudam a analisar os seus dados, incluindo Spark SQL, Delta Lake e Structured Streaming.

Pode analisar dados com .NET para Apache Spark através de definições de trabalho de lote spark ou com cadernos interativos Azure Synapse Analytics. Neste artigo, aprende-se a usar .NET para Apache Spark com Azure Synapse utilizando ambas as técnicas.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Submeta trabalhos de lote utilizando a definição de trabalho spark

Visite o tutorial para aprender a usar a Azure Synapse Analytics para criar definições de [trabalho da Apache Spark para piscinas Synapse Spark.](apache-spark-job-definitions.md) Se não embalou a sua aplicação para se submeter ao Azure Synapse, complete os seguintes passos.

1. Execute os seguintes comandos para publicar a sua aplicação. Certifique-se de substituir o *mySparkApp* pelo caminho para a sua aplicação.

   **Nas janelas:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

   **Em Linux:**

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET para Apache Spark em cadernos Azure Synapse Analytics 

Os cadernos são uma ótima opção para protótipos do seu .NET para os oleodutos e cenários da Apache Spark. Pode começar a trabalhar com, compreensão, filtragem, exibição e visualização dos seus dados de forma rápida e eficiente. Engenheiros de dados, cientistas de dados, analistas de negócios e engenheiros de machine learning são todos capazes de colaborar sobre um documento partilhado e interativo. Você vê resultados imediatos da exploração de dados, e pode visualizar os seus dados no mesmo caderno.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Como usar .NET para os cadernos Apache Spark

Quando cria um novo caderno, escolhe um núcleo linguístico que deseja expressar a sua lógica de negócio. Há apoio de kernel para várias línguas, incluindo C#.

Para utilizar .NET para Apache Spark no seu caderno Azure Synapse Analytics, **selecione .NET Spark (C#)** como o seu núcleo e fixe o caderno a uma piscina spark existente.

O notebook .NET Spark baseia-se nas experiências interativas .NET e proporciona experiências interativas de C# com a capacidade de utilizar .NET fora da caixa com a variável spark session `spark` já predefinida.

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET para características do kernel Apache Spark C#

As seguintes funcionalidades estão disponíveis quando utilizar .NET para Apache Spark no caderno Azure Synapse Analytics:

* HTML declarativo: Gere a saída das suas células utilizando html-sintaxe, tais como cabeçalhos, listas de balas e até mesmo exibindo imagens.
* Declarações C# simples (tais como atribuições, impressão para consola, arremesso de exceções, e assim por diante).
* Blocos de código C# multi-linhas (tais como declarações, circuitos foreach, definições de classe, e assim por diante).
* Acesso à biblioteca C# padrão (como System, LINQ, Enumerables, e assim por diante).
* Suporte para [características linguísticas C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* 'faísca' como variável pré-definida para lhe dar acesso à sua sessão Apache Spark.
* Suporte para definir [funções definidas pelo utilizador .NET que podem ser executadas dentro](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)da Apache Spark .
* Suporte para visualizar a produção dos seus trabalhos spark usando diferentes gráficos (como linha, barra ou histograma) e layouts (como single, overlaid, e assim por diante) usando a `XPlot.Plotly` biblioteca.
* Capacidade de incluir pacotes NuGet no seu caderno C#.

## <a name="next-steps"></a>Passos seguintes

* [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interativo](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
