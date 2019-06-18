---
title: Utilizar .NET com o Hadoop MapReduce no HDInsight baseado em Linux - Azure
description: Saiba como utilizar aplicações de .NET para transmissão em fluxo MapReduce no HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a1d1488840ca2b17c83f380af4fa24105bb36202
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729480"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrar soluções .NET para o HDInsight baseado em Windows para HDInsight baseado em Linux

Utilização de clusters do HDInsight baseado em Linux [Mono (https://mono-project.com) ](https://mono-project.com) para executar aplicações de .NET. Mono permite-lhe utilizar componentes .NET como aplicativos de MapReduce com o HDInsight baseado em Linux. Neste documento, saiba como migrar soluções .NET criadas para os clusters do HDInsight baseado em Windows funcionar com o Mono no HDInsight baseado em Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilidade mono com .NET

Versão mono 4.2.1 está incluída com o HDInsight versão 3.6. Para obter mais informações sobre a versão do Mono incluído com o HDInsight, consulte [versões de componente de HDInsight](hdinsight-component-versioning.md).

Para obter mais informações sobre a compatibilidade entre o Mono e .NET, consulte a [compatibilidade Mono (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) documento.

> [!IMPORTANT]  
> Da arquitetura SCP.NET é compatível com Mono. Para obter mais informações sobre como utilizar SCP.NET com Mono, consulte [Use o Visual Studio para desenvolver topologias c# para Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análise de portabilidade automatizada

O [analisador de portabilidade do .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) pode ser utilizado para gerar um relatório de incompatibilidades entre a aplicação e Mono. Utilize os seguintes passos para configurar o analyzer para verificar a sua aplicação para portabilidade Mono:

1. Instalar o [analisador de portabilidade do .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante a instalação, selecione a versão do Visual Studio para utilizar.

2. A partir do Visual Studio 2015, selecione __Analyze__ > __configurações do analisador de portabilidade__e certifique-se de que __4.5__ é verificado o __Mono__ secção.

    ![4.5 marcada na secção Mono para as definições de analisador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecione __OK__ para guardar a configuração.

3. Selecione __analisar__ > __analisar a portabilidade de Assembly__. Selecione o assembly que contém a sua solução e, em seguida, selecione __aberto__ para iniciar uma análise.

4. Assim que a análise estiver concluída, selecione __Analyze__ > __ver relatórios de análise de__. Na __resultados de análise de portabilidade__, selecione __abrir relatório__ para abrir um relatório.

    ![Caixa de diálogo de resultados de analisador de portabilidade](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> O analisador não é possível capturar todos os problemas com a sua solução. Por exemplo, um caminho de ficheiro de `c:\temp\file.txt` é considerado OK se Mono está em execução no Windows. O mesmo caminho não é válido numa plataforma Linux.

## <a name="manual-portability-analysis"></a>Análise de portabilidade manual

Realizar uma auditoria manual de seu código usando as informações a [portabilidade da aplicação (https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) documento.

## <a name="modify-and-build"></a>Modificar e criar

Pode continuar a utilizar o Visual Studio para criar as suas soluções .NET para o HDInsight. No entanto, tem de se certificar de que o projeto está configurado para utilizar o .NET Framework 4.5.

## <a name="deploy-and-test"></a>Implementar e testar

Depois de modificar sua solução com as recomendações do analisador de portabilidade do .NET ou de uma análise manual, é necessário testá-lo com o HDInsight. Teste da solução num cluster do HDInsight baseado em Linux pode revelar problemas sutis que precisam ser corrigidas. Recomendamos que ative o registo adicional na sua aplicação ao testá-lo.

Para obter mais informações sobre como acessar os registos, consulte os seguintes documentos:

* [Registos de aplicações do Apache Hadoop YARN de acesso no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar c# com o MapReduce no HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Utilize C# funções definidas pelo utilizador com o Apache Hive e Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desenvolva C# topologias do Apache Storm no HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
