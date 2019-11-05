---
title: Usar o .NET com o MapReduce do Hadoop no HDInsight baseado em Linux – Azure
description: Saiba como usar aplicativos .NET para o MapReduce de streaming no HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498230"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrar soluções .NET para HDInsight baseado em Windows para HDInsight baseado em Linux

Os clusters HDInsight baseados em Linux usam [mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .net. O mono permite que você use componentes .NET, como aplicativos MapReduce com o HDInsight baseado em Linux. Neste documento, saiba como migrar soluções .NET criadas para clusters HDInsight baseados no Windows para trabalhar com o mono no HDInsight baseado em Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilidade mono com .NET

A versão do mono 4.2.1 está incluída no HDInsight versão 3,6. Para obter mais informações sobre a versão do mono incluída com o HDInsight, consulte [versões de componente do hdinsight](hdinsight-component-versioning.md).

Para obter mais informações sobre a compatibilidade entre o mono e o .NET, consulte o documento [compatibilidade mono (https://www.mono-project.com/docs/about-mono/compatibility/)](https://www.mono-project.com/docs/about-mono/compatibility/) .

> [!IMPORTANT]  
> A estrutura SCP.NET é compatível com mono. Para obter mais informações sobre como usar o SCP.NET com o mono, consulte [usar C# o Visual Studio para desenvolver topologias para Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análise de portabilidade automatizada

O [.net Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) pode ser usado para gerar um relatório de incompatibilidades entre seu aplicativo e o mono. Use as etapas a seguir para configurar o analisador para verificar seu aplicativo para portabilidade mono:

1. Instale o [.net Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante a instalação, selecione a versão do Visual Studio a ser usada.

2. No Visual Studio 2015, selecione __analisar__ > __configurações do analisador de portabilidade__e verifique se __4,5__ está marcado na seção __mono__ .

    ![4,5 seção com check-in mono para as configurações do analisador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecione __OK__ para salvar a configuração.

3. Selecione __analisar__ > __analisar portabilidade do assembly__. Selecione o assembly que contém sua solução e, em seguida, selecione __abrir__ para iniciar a análise.

4. Após a conclusão da análise, selecione __analisar__ > __exibir relatórios de análise__. Em __resultados da análise de portabilidade__, selecione __abrir relatório__ para abrir um relatório.

    ![Caixa de diálogo resultados do analisador de portabilidade](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> O analisador não pode capturar todos os problemas com sua solução. Por exemplo, um caminho de arquivo de `c:\temp\file.txt` será considerado OK se o mono estiver em execução no Windows. O mesmo caminho não é válido em uma plataforma Linux.

## <a name="manual-portability-analysis"></a>Análise manual de portabilidade

Execute uma auditoria manual do seu código usando as informações no documento [portabilidade do aplicativo (https://www.mono-project.com/docs/getting-started/application-portability/)](https://www.mono-project.com/docs/getting-started/application-portability/) .

## <a name="modify-and-build"></a>Modificar e compilar

Você pode continuar a usar o Visual Studio para criar suas soluções .NET para o HDInsight. No entanto, você deve garantir que o projeto esteja configurado para usar o .NET Framework 4,5.

## <a name="deploy-and-test"></a>Implantar e testar

Depois de modificar sua solução usando as recomendações do analisador de portabilidade do .NET ou de uma análise manual, você deve testá-la com o HDInsight. Testar a solução em um cluster HDInsight baseado em Linux pode revelar problemas sutis que precisam ser corrigidos. Recomendamos que você habilite o log adicional em seu aplicativo ao testá-lo.

Para obter mais informações sobre como acessar logs, consulte os seguintes documentos:

* [Acessar Apache Hadoop logs de aplicativo do YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Passos seguintes

* [Usar C# com o MapReduce no HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Usar C# funções definidas pelo usuário com Apache Hive e Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desenvolver C# topologias para Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
