---
title: Utilize um PC do Windows com Hadoop no HDInsight - Azure
description: Trabalhe a partir de um PC do Windows em Hadoop no HDInsight. Gerir e consultar clusters com ferramentas PowerShell, Visual Studio e Linux. Desenvolver grandes soluções de dados com .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75933940"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhe no ecossistema Apache Hadoop no HDInsight a partir de um PC windows

Conheça as opções de desenvolvimento e gestão no PC do Windows para trabalhar no ecossistema Apache Hadoop no HDInsight.

O HDInsight baseia-se nos componentes Apache Hadoop e Hadoop, tecnologias de código aberto desenvolvidas no Linux. A versão 3.4 e superior do HDInsight utiliza a distribuição Ubuntu Linux como o SISTEMA subjacente ao cluster. No entanto, pode trabalhar com o HDInsight a partir de um cliente Windows ou ambiente de desenvolvimento do Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Utilizar o PowerShell para tarefas de implementação e gestão

O Azure PowerShell é um ambiente de scripts que podes usar para controlar e automatizar tarefas de implementação e gestão em HDInsight a partir do Windows.

Exemplos de tarefas que pode fazer com o PowerShell:

* [Crie clusters usando powerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Executar consultas de Hiv Apache usando PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Gerir clusters com PowerShell](hdinsight-administer-use-powershell.md).

Siga os passos para instalar e configurar o [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) para obter a versão mais recente.

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que você pode executar em um navegador

Os seguintes utilitários têm um UI web que funciona num navegador:
* **[A Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** é uma concha interativa e de linha de comando que funciona no seu navegador e dentro do portal Azure.

* A **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** é um utilitário de gestão e monitorização disponível no portal Azure que pode ser usado para gerir diferentes tipos de empregos, tais como:
    * [Use Apache Ambari com a API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Vista da Colmeia Apache em Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Visão Apache Tez em Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Ferramentas do Lago de Dados (Hadoop) para estúdio visual

Utilize ferramentas de data lake para o Estúdio Visual para implementar e gerir topoologias storm. Data Lake Tools também instala o SCP.NET SDK, que permite desenvolver topologias de Tempestade C# com Estúdio Visual.

Antes de seguir para os seguintes exemplos, [instale e experimente Ferramentas data lake para estúdio visual](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Exemplos de tarefas que pode fazer com Visual Studio e Data Lake Tools para Estúdio Visual:
* [Implementar e gerir topoologias storm do Estúdio Visual](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Desenvolva topoologias C# para tempestade utilizando o Estúdio Visual](storm/apache-storm-develop-csharp-visual-studio-topology.md). Os bits incluem modelos de exemplo para topoologias de tempestade que você pode ligar a bases de dados, tais como Azure Cosmos DB e SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Estúdio Visual e o .NET SDK

Pode utilizar o Visual Studio com o .NET SDK para gerir clusters e desenvolver aplicações de big data. Pode utilizar outros IDEs para as seguintes tarefas, mas exemplos são mostrados no Estúdio Visual.

Exemplos de tarefas que pode fazer com o .NET SDK em Estúdio Visual:
* [Azure HDInsight SDK para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Executar consultas de Hive Apache utilizando o .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Utilize funções c# definidas pelo utilizador com a Apache Hive e o Apache Pig a transmitir em Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA e Eclipse IDE para clusters de faíscas

Tanto o [Intellij IDEA](https://www.jetbrains.com/idea/download) como o [Eclipse IDE](https://www.eclipse.org/downloads/) podem ser usados para:
* Desenvolva e envie uma aplicação Scala Spark num cluster HDInsight Spark.
* Acesso aos recursos do cluster Spark.
* Desenvolva e execute uma aplicação Scala Spark localmente.

Estes artigos mostram como:
* Intellij IDEA: [Crie aplicações Apache Spark utilizando o Kit de Ferramentas Azure para plug-in Intellij e o Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE ou Scala IDE para Eclipse: [Criar aplicações Apache Spark e o Kit de Ferramentas Azure para Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Cadernos sobre Faísca para cientistas de dados

Os clusters Apache Spark em HDInsight incluem cadernos Apache Zeppelin e miolos que podem ser usados com cadernos Jupyter.

* [Saiba como usar núcleos em aglomerados Apache Spark com cadernos Jupyter para testar aplicações spark](spark/apache-spark-zeppelin-notebook.md)
* [Aprenda a usar os cadernos Apache Zeppelin em clusters Apache Spark para executar empregos spark](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas e tecnologias baseadas em Linux no Windows

Se encontrar uma situação em que deve utilizar uma ferramenta ou tecnologia que só esteja disponível no Linux, considere as seguintes opções:

* **A bash em Ubuntu no Windows 10** fornece um subsistema Linux no Windows. A Bash permite-lhe executar diretamente os utilitários Linux sem ter de manter uma instalação dedicada ao Linux. Consulte o Subsistema Windows para obter o Guia de [Instalação do Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para obter as etapas de instalação.  Outras [conchas unix](https://www.gnu.org/software/bash/) também funcionarão.
* **O Docker for Windows** fornece acesso a muitas ferramentas baseadas em Linux, e pode ser executado diretamente a partir do Windows. Por exemplo, pode usar o Docker para executar o cliente Beeline para a Hive diretamente a partir do Windows. Também pode usar o Docker para executar um portátil jupyter local e ligar-se remotamente à Spark no HDInsight. [Começa com o Docker para windows](https://docs.docker.com/docker-for-windows/)
* **[O MobaXTerm](https://mobaxterm.mobatek.net/)** permite-lhe navegar graficamente no sistema de ficheiros de cluster sondar uma ligação SSH.

## <a name="cross-platform-tools"></a>Ferramentas transversais

A interface de linha de comandos (CLI) do Azure é a experiência de linha de comandos entre plataformas da Microsoft para gerir os recursos do Azure.  Para mais informações, consulte a Interface de [Linha de Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Passos seguintes

Se você é novo em trabalhar em clusters baseados em Linux, consulte os seguintes artigos:
* [Configurar Apache Hadoop, Apache Kafka, Apache Spark, ou outros aglomerados](hdinsight-hadoop-provision-linux-clusters.md)
* [Dicas para clusters HDInsight em Linux](hdinsight-hadoop-linux-information.md)
