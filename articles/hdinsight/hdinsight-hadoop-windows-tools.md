---
title: Utilize um PC Windows com Hadoop em HDInsight - Azure
description: Trabalhe a partir de um PC Windows em Hadoop em HDInsight. Gerir e consultar agrupamentos com ferramentas PowerShell, Visual Studio e Linux. Desenvolver soluções de big data com .NET.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: ceb83c8c1754f0bc298290f522a8ae532db32140
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434615"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhar no ecossistema Apache Hadoop em HDInsight a partir de um PC windows

Conheça as opções de desenvolvimento e gestão no Pc do Windows para trabalhar no ecossistema Apache Hadoop em HDInsight.

O HDInsight baseia-se em componentes Apache Hadoop e Hadoop, tecnologias de código aberto desenvolvidas no Linux. A versão 3.4 e superior da HDInsight utiliza a distribuição Ubuntu Linux como o SISTEMA subjacente ao cluster. No entanto, pode trabalhar com o HDInsight a partir de um ambiente de desenvolvimento de clientes Windows ou Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Utilizar o PowerShell para tarefas de implantação e gestão

O Azure PowerShell é um ambiente de scripts que podes usar para controlar e automatizar tarefas de implementação e gestão em HDInsight a partir do Windows.

Exemplos de tarefas que pode fazer com o PowerShell:

* [Criar clusters utilizando o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Executar consultas de Colmeia Apache usando PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Gerir agrupamentos com PowerShell](hdinsight-administer-use-powershell.md).

Siga os passos para [instalar e configurar a Azure Powershell](/powershell/azure/install-az-ps) para obter a versão mais recente.

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que você pode executar em um navegador

Os seguintes utilitários têm uma UI web que funciona num browser:
* **[Azure Cloud Shell](../cloud-shell/overview.md)** é uma concha interativa de linha de comando que funciona no seu navegador e dentro do portal Azure.

* **[A Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** é uma utilidade de gestão e monitorização disponível no portal Azure que pode ser usada para gerir diferentes tipos de empregos, tais como:
    * [Use Apache Ambari com a API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Vista de Colmeia Apache em Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Vista Apache Tez em Apache Ambari](./index.yml)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Ferramentas do Lago de Dados (Hadoop) para Estúdio Visual

Utilize ferramentas do Data Lake para o Estúdio Visual para implementar e gerir as topologias da Tempestade. Data Lake Tools também instala o SCP.NET SDK, que permite desenvolver topologias de tempestade C# com Visual Studio.

Antes de ir aos seguintes exemplos, [instale e experimente ferramentas data lake para o Estúdio Visual.](hadoop/apache-hadoop-visual-studio-tools-get-started.md)

Exemplos de tarefas que pode fazer com Visual Studio e Data Lake Tools para Estúdio Visual:
* [Implementar e gerir topologias de tempestade do Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Desenvolver topologias C# para tempestade usando o Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Os bits incluem modelos de exemplo para topologias storm que você pode ligar a bases de dados, tais como Azure Cosmos DB e SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Estúdio Visual e .NET SDK

Pode utilizar o Visual Studio com o .NET SDK para gerir clusters e desenvolver aplicações de big data. Pode utilizar outros IDEs para as seguintes tarefas, mas os exemplos são mostrados no Visual Studio.

Exemplos de tarefas que pode fazer com o .NET SDK em Visual Studio:
* [Azure HDInsight SDK para .NET](/dotnet/api/overview/azure/hdinsight).
* [Executar consultas de Colmeia Apache utilizando o .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Utilize funções definidas pelo utilizador C# com o fluxo apache hive e apache pig em Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA e Eclipse IDE para clusters de faíscas

Tanto [o Intellij IDEA](https://www.jetbrains.com/idea/download) como o Eclipse [IDE](https://www.eclipse.org/downloads/) podem ser usados para:
* Desenvolver e submeter uma aplicação Scala Spark num cluster HDInsight Spark.
* Aceder aos recursos do cluster Spark.
* Desenvolver e executar uma aplicação Scala Spark localmente.

Estes artigos mostram como:
* Ideia Intellij: [Criar aplicações Apache Spark utilizando o Azure Toolkit para o plug-in Intellij e o Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE ou Scala IDE para Eclipse: [Criar aplicações Apache Spark e o Azure Toolkit para Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Cadernos em Faísca para cientistas de dados

Os aglomerados apache spark em HDInsight incluem cadernos Apache Zeppelin e núcleos que podem ser usados com cadernos Jupyter.

* [Saiba como usar núcleos em aglomerados Apache Spark com cadernos Jupyter para testar aplicações Spark](spark/apache-spark-zeppelin-notebook.md)
* [Aprenda a usar cadernos Apache Zeppelin em aglomerados Apache Spark para gerir trabalhos de faísca](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas e tecnologias baseadas em Linux no Windows

Se encontrar uma situação em que deve utilizar uma ferramenta ou tecnologia que só esteja disponível no Linux, considere as seguintes opções:

* **A Bash on Ubuntu no Windows 10** fornece um subsistema Linux no Windows. A Bash permite-lhe executar diretamente os utilitários Linux sem ter de manter uma instalação linux dedicada. Consulte [o subsistema Windows para o Guia de Instalação do Linux para o Windows 10](/windows/wsl/install-win10) para obter etapas de instalação.  Outras [conchas Unix](https://www.gnu.org/software/bash/) também funcionarão.
* **O Docker for Windows** fornece acesso a muitas ferramentas baseadas no Linux e pode ser executado diretamente a partir do Windows. Por exemplo, podes usar o Docker para executar o cliente Beeline para a Hive diretamente a partir do Windows. Também pode usar o Docker para executar um Bloco de Notas Jupyter local e ligar-se remotamente ao Spark on HDInsight. [Começa com o Docker para o Windows](https://docs.docker.com/docker-for-windows/)
* **[O MobaXTerm](https://mobaxterm.mobatek.net/)** permite-lhe navegar graficamente no sistema de ficheiros de cluster sobre uma ligação SSH.

## <a name="cross-platform-tools"></a>Ferramentas de plataforma cruzada

A interface de linha de comandos (CLI) do Azure é a experiência de linha de comandos entre plataformas da Microsoft para gerir os recursos do Azure.  Para mais informações, consulte [Azure Command-Line Interface (CLI)](/cli/azure/).

## <a name="next-steps"></a>Passos seguintes

Se é novo a trabalhar em clusters baseados em Linux, consulte os seguintes artigos:
* [Configurar Apache Hadoop, Apache Kafka, Apache Spark, ou outros clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Dicas para clusters HDInsight em Linux](hdinsight-hadoop-linux-information.md)