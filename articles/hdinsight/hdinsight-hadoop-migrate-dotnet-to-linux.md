---
title: Use .NET com Hadoop MapReduce em HDInsight - Azure baseado em Linux
description: Saiba como utilizar aplicações .NET para streaming de MapReduce no HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a6e9bea5d600771a7754142a4df4c0af503ccf5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86075489"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrar soluções .NET para HDInsight baseado no Windows para o HDInsight baseado em Linux

Os clusters HDInsight baseados em Linux utilizam [Mono (para https://mono-project.com) ](https://mono-project.com) executar aplicações .NET. O Mono permite-lhe utilizar componentes .NET, como aplicações MapReduce com HDInsight baseada em Linux. Neste documento, aprenda a migrar soluções .NET criadas para clusters HDInsight baseados no Windows para trabalhar com a Mono em HDInsight baseada em Linux.

## <a name="mono-compatibility-with-net"></a>Mono compatibilidade com .NET

A versão mono 4.2.1 está incluída na versão HDInsight 3.6. Para obter mais informações sobre a versão de Mono incluída com HDInsight, consulte as [versões do componente HDInsight](hdinsight-component-versioning.md).

Para obter mais informações sobre a compatibilidade entre Mono e .NET, consulte a [compatibilidade mono https://www.mono-project.com/docs/about-mono/compatibility/) (documento.](https://www.mono-project.com/docs/about-mono/compatibility/)

> [!IMPORTANT]  
> A estrutura SCP.NET é compatível com a Mono. Para obter mais informações sobre a utilização de SCP.NET com a Mono, consulte [Use Visual Studio para desenvolver topologias C# para Apache Storm em HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análise automatizada de portabilidade

O [Analisador de Portabilidade .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) pode ser utilizado para gerar um relatório de incompatibilidades entre a sua aplicação e a Mono. Utilize os seguintes passos para configurar o analisador para verificar a sua aplicação de portabilidade Mono:

1. Instale o [Analisador de Portabilidade .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante a instalação, selecione a versão do Visual Studio para usar.

2. A partir do Visual Studio 2015, selecione __Analisar__  >  __definições de analisador de portabilidade__e certifique-se de que __o 4.5__ é verificado na secção __Mono.__

    ![4.5 verificado na secção Mono para as definições do analisador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecione __OK__ para guardar a configuração.

3. Selecione __Analyze__  >  __analisar a portabilidade do conjunto de análises__. Selecione o conjunto que contém a sua solução e, em seguida, selecione __Abrir__ para iniciar a análise.

4. Uma vez concluída __Analyze__a análise, selecione  >  __relatórios de análise de__Análise de Análise . Nos __Resultados da Análise de Portabilidade,__ selecione __Relatório Aberto__ para abrir um relatório.

    ![Diálogo de resultados do analisador de portabilidade](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> O analisador não pode apanhar todos os problemas com a sua solução. Por exemplo, um caminho de ficheiro `c:\temp\file.txt` é considerado OK se Mono estiver em execução no Windows. O mesmo caminho não é válido numa plataforma Linux.

## <a name="manual-portability-analysis"></a>Análise manual da portabilidade

Efetue uma auditoria manual do seu código utilizando as informações na Portabilidade da [Aplicação https://www.mono-project.com/docs/getting-started/application-portability/) (documento.](https://www.mono-project.com/docs/getting-started/application-portability/)

## <a name="modify-and-build"></a>Modificar e construir

Pode continuar a utilizar o Visual Studio para construir as suas soluções .NET para HDInsight. No entanto, deve certificar-se de que o projeto está configurado para utilizar o Quadro .NET 4.5.

## <a name="deploy-and-test"></a>Implantar e testar

Depois de modificar a sua solução utilizando as recomendações do Analisador de Portabilidade .NET ou de uma análise manual, deve testá-la com HDInsight. Testar a solução num cluster HDInsight baseado em Linux pode revelar problemas subtis que precisam de ser corrigidos. Recomendamos que ative o registo adicional na sua aplicação enquanto o testa.

Para obter mais informações sobre o acesso aos registos, consulte os seguintes documentos:

* [Aceder a registos de aplicações apache Hadoop YARN em HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Passos seguintes

* [Use C# com MapReduce em HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Use funções definidas pelo utilizador C# com A Colmeia Apache e Porco Apache](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desenvolver topologias C# para Apache Storm em HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
