---
title: Usar um computador Windows com Hadoop no HDInsight – Azure
description: Trabalhar de um computador Windows no Hadoop no HDInsight. Gerencie e consulte clusters com as ferramentas do PowerShell, do Visual Studio e do Linux. Desenvolva Big Data soluções com o .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 0fd299ea6ceb9631ff473ec2ac9f37be2f6be215
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495727"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhar no ecossistema de Apache Hadoop no HDInsight de um computador Windows

Saiba mais sobre as opções de desenvolvimento e gerenciamento no computador Windows para trabalhar no ecossistema de Apache Hadoop no HDInsight.

O HDInsight é baseado em componentes Apache Hadoop e Hadoop, tecnologias de software livre desenvolvidas no Linux. O HDInsight versão 3,4 e superior usa a distribuição de Ubuntu Linux como o sistema operacional subjacente para o cluster. No entanto, você pode trabalhar com o HDInsight de um ambiente de desenvolvimento do Windows ou cliente do Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Usar o PowerShell para tarefas de implantação e gerenciamento

Azure PowerShell é um ambiente de script que você pode usar para controlar e automatizar tarefas de implantação e gerenciamento no HDInsight do Windows.

Exemplos de tarefas que você pode fazer com o PowerShell:

* [Crie clusters usando o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Execute Apache Hive consultas usando o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Gerenciar clusters com o PowerShell](hdinsight-administer-use-powershell.md).

Siga as etapas para [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) para obter a versão mais recente.

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que você pode executar em um navegador

Os utilitários a seguir têm uma interface do usuário da Web que é executada em um navegador:
* **[Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** é um shell de linha de comando interativo que é executado no navegador e no portal do Azure.

* A **[interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)** é um utilitário de gerenciamento e monitoramento disponível no portal do Azure que pode ser usado para gerenciar diferentes tipos de trabalhos, como:
    * [Usar o Apache Ambari com a API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive exibição no Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez exibição no Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Ferramentas do Data Lake (Hadoop) para Visual Studio

Use as ferramentas de Data Lake para o Visual Studio para implantar e gerenciar topologias Storm. Data Lake ferramentas também instala o SDK do SCP.NET, que permite desenvolver C# topologias Storm com o Visual Studio.

Antes de ir para os exemplos a seguir, [Instale e experimente o data Lake Tools para Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Exemplos de tarefas que você pode fazer com o Visual Studio e o Data Lake Tools para Visual Studio:
* [Implantar e gerenciar topologias Storm do Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Desenvolver C# topologias para Storm usando o Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Os bits incluem modelos de exemplo para topologias Storm que você pode conectar a bancos de dados, como o Azure Cosmos DB e o SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e o SDK do .NET

Você pode usar o Visual Studio com o SDK do .NET para gerenciar clusters e desenvolver Big Data aplicativos. Você pode usar outros IDEs para as seguintes tarefas, mas exemplos são mostrados no Visual Studio.

Exemplos de tarefas que você pode fazer com o SDK do .NET no Visual Studio:
* [Crie clusters e trabalhe no HDInsight por meio de um aplicativo .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* [Execute Apache Hive consultas usando o SDK do .net](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Use C# funções definidas pelo usuário com Apache Hive e o streaming do Apache Pig no Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IntelliJ IDEA e IDE do Eclipse para clusters Spark

Tanto a [ideia IntelliJ](https://www.jetbrains.com/idea/download) quanto o [Eclipse IDE](https://www.eclipse.org/downloads/) podem ser usados para:
* Desenvolva e envie um aplicativo do Spark em um cluster HDInsight Spark.
* Acessar recursos de cluster do Spark.
* Desenvolva e execute um aplicativo exscale Spark localmente.

Estes artigos mostram como:
* IntelliJ ideia: [crie aplicativos de Apache Spark usando o plug-in do kit de ferramentas do Azure para IntelliJ e o SDK do Scale.](spark/apache-spark-intellij-tool-plugin.md)
* IDE Eclipse ou IDE escalar para Eclipse: [criar Apache Spark aplicativos e o Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooks no Spark para cientistas de dados

Apache Spark clusters no HDInsight incluem os notebooks Apache Zeppelin e kernels que podem ser usados com notebooks Jupyter.

* [Saiba como usar kernels em clusters Apache Spark com notebooks Jupyter para testar aplicativos Spark](spark/apache-spark-zeppelin-notebook.md)
* [Saiba como usar os notebooks do Apache Zeppelin em clusters Apache Spark para executar trabalhos do Spark](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas e tecnologias baseadas em Linux no Windows

Se você vir uma situação em que deve usar uma ferramenta ou tecnologia disponível apenas no Linux, considere as seguintes opções:

* O **bash no Ubuntu no Windows 10** fornece um subsistema Linux no Windows. O bash permite que você execute diretamente utilitários do Linux sem precisar manter uma instalação dedicada do Linux. Consulte o [Guia de instalação do subsistema do Windows para Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outros [shells do UNIX](https://www.gnu.org/software/bash/) também funcionarão.
* O **Docker for Windows** fornece acesso a muitas ferramentas baseadas em Linux e pode ser executado diretamente do Windows. Por exemplo, você pode usar o Docker para executar o cliente beeline para o hive diretamente do Windows. Você também pode usar o Docker para executar um notebook Jupyter local e conectar-se remotamente ao Spark no HDInsight. [Introdução ao Docker for Windows](https://docs.docker.com/docker-for-windows/)
* O **[MobaXTerm](https://mobaxterm.mobatek.net/)** permite que você navegue graficamente no sistema de arquivos de cluster em uma conexão SSH.

## <a name="cross-platform-tools"></a>Ferramentas de plataforma cruzada

A interface de linha de comandos (CLI) do Azure é a experiência de linha de comandos entre plataformas da Microsoft para gerir os recursos do Azure.  Para obter mais informações, consulte [interface de linha de comando (CLI) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Passos seguintes

Se você não estiver familiarizado com o trabalho em clusters baseados em Linux, consulte os seguintes artigos:
* [Configurar Apache Hadoop, Apache Kafka, Apache Spark ou outros clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Dicas para clusters HDInsight no Linux](hdinsight-hadoop-linux-information.md)
