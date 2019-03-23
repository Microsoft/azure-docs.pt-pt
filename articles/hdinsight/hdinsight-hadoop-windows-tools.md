---
title: Utilizar um PC do Windows com o Hadoop no HDInsight - Azure
description: Trabalhar a partir de um PC do Windows no Hadoop no HDInsight. Gerir clusters e de consulta com as ferramentas do PowerShell, o Visual Studio e o Linux. Desenvolva soluções de macrodados com o .NET.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/17/2017
ms.openlocfilehash: b8bad1d0f53ce4e4c924734201165e1b050c153f
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361477"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabalhar no ecossistema do Apache Hadoop no HDInsight a partir de um PC Windows

Saiba mais sobre o desenvolvimento e as opções de gestão do PC Windows para trabalhar no ecossistema do Apache Hadoop no HDInsight. 

HDInsight baseia-se no Apache Hadoop e componentes do Hadoop, tecnologias de código-fonte aberto desenvolvidas no Linux. HDInsight versão 3.4 e superior utiliza a distribuição do Ubuntu Linux como sistema operacional subjacente para o cluster. No entanto, pode trabalhar com o HDInsight partir de um cliente do Windows ou o ambiente de desenvolvimento do Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Utilizar o PowerShell para tarefas de implementação e gestão
O Azure PowerShell é um ambiente de script que pode utilizar para controlar e automatizar a implantação e tarefas de gestão no HDInsight do Windows.

Exemplos de tarefas que pode fazer com o PowerShell:

* [Criar clusters com o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Executar consultas do Apache Hive com o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Gerir clusters com o PowerShell](hdinsight-administer-use-powershell.md).

Siga os passos para [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) para obter a versão mais recente. Se tiver scripts que precisam ser modificados para utilizar os novos cmdlets para o Azure Resource Manager, veja [migre para ferramentas de desenvolvimento baseado no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Utilitários que pode executar num browser
Os utilitários a seguir têm uma interface de Usuário que é executado num navegador da web:
* **[O Azure Cloud Shell (pré-visualização)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  é um shell interativo, da linha de comandos que é executado no seu browser e a partir do portal do Azure.
* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)**  é uma gestão e monitorização de utilitário, disponível no portal do Azure que pode ser utilizado para gerir diferentes tipos de tarefas, tais como:
    * [Utilizar o Apache Ambari com a API de REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive vista no Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Modo de exibição do Apache Tez no Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>N (Hadoop) do Data Lake Tools para Visual Studio
Utilize o Data Lake Tools para Visual Studio para implementar e gerir topologias do Storm. Ferramentas do Data Lake também instala o SDK de SCP.NET, que permite-lhe desenvolver topologias Storm em c# com o Visual Studio.

Antes de ir para os exemplos seguintes, [instale e experimente as ferramentas do Data Lake para Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

Exemplos de tarefas que pode fazer com o Visual Studio e ferramentas do Data Lake para Visual Studio:
* [Implementar e gerir topologias do Storm a partir do Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Desenvolver topologias c# para Storm com o Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Os bits incluem modelos de exemplo para topologias do Storm que pode ligar a bases de dados, como o Azure Cosmos DB e a base de dados SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e o SDK .NET 

Pode utilizar o Visual Studio com o SDK de .NET para gerir clusters e programar aplicações de macrodados. Pode utilizar outros IDEs para as seguintes tarefas, mas exemplos são mostrados no Visual Studio.

Exemplos de tarefas que pode fazer com o SDK de .NET no Visual Studio:
* [Criar clusters e trabalhar no HDInsight a partir de um aplicativo .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* [Executar consultas do Apache Hive com o SDK .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Utilize C# funções definidas pelo utilizador com o Apache Hive e Apache Pig, transmissão em fluxo no Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

> [!TIP]
> Se estiver a executar soluções .NET com clusters do HDInsight baseado em Windows, é um bom momento para planear uma migração para clusters baseados em Linux. Para obter mais informações, consulte [solução de .NET de migrar para o HDInsight baseado em Windows para HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEIA e Eclipse IDE para clusters do Spark
Ambos [Intellij IDEA](https://www.jetbrains.com/idea/download) e o [IDE do Eclipse](https://www.eclipse.org/downloads/) podem ser utilizados para:
* Programe e envie um aplicação Scala Spark num cluster do HDInsight Spark.
* Aceder aos recursos de cluster do Spark.
* Desenvolva e execute uma aplicação Scala Spark localmente.

Estes artigos mostram como: 
* Intellij IDEIA: [Crie aplicativos do Apache Spark com o Azure Toolkit para Intellij Plug-in e o SDK de Scala.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE ou Scala IDE do Eclipse: [Criar aplicações do Apache Spark e o Azure Toolkit para Eclipse](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Blocos de notas no Spark para cientistas de dados 
Clusters do Apache Spark no HDInsight incluem blocos de notas do Apache Zeppelin e kernels que podem ser utilizados com blocos de notas do Jupyter. 

* [Saiba como utilizar kernels nos clusters do Apache Spark com blocos de notas do Jupyter para testar aplicações do Spark](spark/apache-spark-zeppelin-notebook.md)
* [Saiba como utilizar blocos de notas do Apache Zeppelin em clusters do Apache Spark para executar tarefas do Spark](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Executar ferramentas baseadas em Linux e tecnologias no Windows

Se encontrar uma situação em que tem de utilizar uma ferramenta ou tecnologia que só está disponível no Linux, considere as seguintes opções:

* **Bash (beta) no Windows 10** fornece um subsistema de Linux no Windows. Bash permite-lhe executar diretamente os utilitários de Linux sem a necessidade de manter uma instalação de Linux dedicada. [Instalar e executar a versão beta do Bash no Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker para Windows** fornece acesso a muitas ferramentas baseado em Linux e podem ser executadas diretamente a partir do Windows. Por exemplo, pode utilizar o Docker para executar o cliente de Beeline para Hive diretamente a partir do Windows. Pode também utilizar o Docker para executar um bloco de notas Jupyter local e ligar remotamente ao Spark no HDInsight. [Introdução ao Docker para Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)**  permite que pesquise o sistema de ficheiros do cluster através de uma ligação de SSH.

## <a name="next-steps"></a>Passos Seguintes
Se trabalhou em clusters baseados em Linux, veja os artigos seguintes:
* [Configurar o Apache Hadoop, Apache Kafka, Apache Spark ou outros clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Dicas para clusters do HDInsight no Linux](hdinsight-hadoop-linux-information.md)
