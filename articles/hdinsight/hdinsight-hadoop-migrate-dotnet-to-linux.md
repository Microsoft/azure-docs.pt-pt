---
title: Use .NET com Hadoop MapReduce on Linux-based HDInsight - Azure
description: Saiba como utilizar aplicações .NET para streaming MapReduce no HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272373"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrar soluções .NET para HDInsight baseado no Windows para HDInsight baseado em Linux

Os clusters HDInsight baseados em Linux usam [mono (https://mono-project.com) ](https://mono-project.com) para executar aplicações .NET. Mono permite-lhe utilizar componentes .NET tais como MapReduce aplicações com HDInsight baseado em Linux. Neste documento, aprenda a migrar as soluções .NET criadas para clusters HDInsight baseados no Windows para trabalhar com Mono no HDInsight baseado em Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilidade mono com .NET

A versão mono 4.2.1 está incluída na versão 3.6 da HDInsight. Para obter mais informações sobre a versão de Mono incluída com hDInsight, consulte [as versões componentes HDInsight](hdinsight-component-versioning.md).

Para obter mais informações sobre compatibilidade entre Mono e .NET, consulte a [compatibilidade monohttps://www.mono-project.com/docs/about-mono/compatibility/) (documento).](https://www.mono-project.com/docs/about-mono/compatibility/)

> [!IMPORTANT]  
> O quadro SCP.NET é compatível com Mono. Para obter mais informações sobre a utilização SCP.NET com Mono, consulte [use visual studio para desenvolver topologias C# para Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análise automatizada de portabilidade

O [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) pode ser usado para gerar um relatório de incompatibilidades entre a sua aplicação e Mono. Utilize os seguintes passos para configurar o analisador para verificar a sua aplicação para a portabilidade mono:

1. Instale o [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante a instalação, selecione a versão do Estúdio Visual para utilizar.

2. A partir do Visual Studio 2015, selecione __'Analisar__ > Definições de Analisador de__Portabilidade',__ e certifique-se de que __o 4.5__ é verificado na secção __Mono.__

    ![4.5 verificado na secção Mono para as definições do analisador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecione __OK__ para salvar a configuração.

3. __Selecione analisar a__ > __portabilidade do conjunto__. Selecione o conjunto que contém a sua solução e, em seguida, __selecione Open__ para iniciar a análise.

4. Uma vez concluída a análise, selecione __análises de Análise de Visualização__ > __View analysis reports__. Em Resultados de __Análise de Portabilidade,__ selecione __relatório aberto__ para abrir um relatório.

    ![Diálogo de resultados do analisador de portabilidade](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> O analisador não pode apanhar todos os problemas com a sua solução. Por exemplo, um `c:\temp\file.txt` caminho de ficheiro sé considerado OK se Mono estiver a funcionar no Windows. O mesmo caminho não é válido numa plataforma Linux.

## <a name="manual-portability-analysis"></a>Análise de portabilidade manual

Efetue uma auditoria manual do seu código utilizando as informações no documento de Portabilidade da [Aplicação .https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/)

## <a name="modify-and-build"></a>Modificar e construir

Pode continuar a utilizar o Visual Studio para construir as suas soluções .NET para HDInsight. No entanto, deve certificar-se de que o projeto está configurado para utilizar .NET Framework 4.5.

## <a name="deploy-and-test"></a>Implantação e teste

Depois de ter modificado a sua solução utilizando as recomendações do Analisador de Portabilidade .NET ou de uma análise manual, deve testá-la com o HDInsight. Testar a solução num cluster HDInsight baseado em Linux pode revelar problemas subtis que precisam de ser corrigidos. Recomendamos que ative o registo adicional na sua aplicação enquanto a testa.

Para obter mais informações sobre o acesso aos registos, consulte os seguintes documentos:

* [Aceder aos registos de aplicações do YARN apache hadoop no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Passos seguintes

* [Use C# com MapReduce no HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Utilize funções c# definidas pelo utilizador com A Colmeia Apache e O Porco Apache](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desenvolver topoologias C# para Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
