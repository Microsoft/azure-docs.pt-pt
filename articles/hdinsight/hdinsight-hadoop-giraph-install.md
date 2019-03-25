---
title: Instale e utilize o Giraph nos clusters do Hadoop no HDInsight - Azure
description: Saiba como personalizar o cluster do HDInsight com o Giraph e como utilizar o Giraph.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 37e8b0a5cc89eded1890eebbeb93cc82d54c9f05
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360899"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Instalar e usar o Apache Giraph nos clusters do HDInsight baseado em Windows

Saiba como personalizar o cluster de HDInsight com base do Windows com o Apache Giraph com ação de Script e como utilizar Giraph para processar gráficos em grande escala. Para obter informações sobre como utilizar o Giraph com um cluster baseado em Linux, consulte [instalar o Apache Giraph nos clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]  
> Os passos neste documento funcionam apenas com clusters do HDInsight baseado em Windows. HDInsight só está disponível no Windows para versões mais baixos do que o HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre como instalar o Giraph num cluster do HDInsight baseado em Linux, consulte [instalar o Apache Giraph nos clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Pode instalar o Giraph num qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight ao utilizar *ação de Script*. Um script de exemplo para instalar o Giraph num cluster do HDInsight está disponível a partir de um blob de armazenamento do Azure só de leitura em [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). O script de exemplo só funciona com clusters do HDInsight versão 3.1. Para obter mais informações sobre versões de cluster do HDInsight, consulte [versões de cluster do HDInsight](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Artigos relacionados**

* [Instalar o Apache Giraph nos clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Criar clusters do Apache Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters do HDInsight.
* [Personalizar clusters do HDInsight com ação de Script] [hdinsight-cluster-personalizar]: informações gerais sobre como personalizar clusters do HDInsight com ação de Script.
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>O que é o Giraph?
<a href="https://giraph.apache.org/" target="_blank">O Apache Giraph</a> permite-lhe efetuar o processamento através do Hadoop de gráficos e pode ser utilizado com o Azure HDInsight. Gráficos de modelam relações entre objetos, tais como as ligações entre routers na rede de grandes dimensões, como a Internet ou as relações entre as pessoas nas redes sociais (por vezes referidos como um gráfico social). Processamento de gráficos permite-lhe de ponderar sobre as relações entre objetos num gráfico, tais como:

* Identificar amigos potenciais com base nas suas relações atuais.
* Identificar a rota mais curta entre dois computadores numa rede.
* Calcular a classificação de página de páginas da Web.

## <a name="install-giraph-using-portal"></a>Instalar o Giraph através do portal
1. Começar a criar um cluster utilizando o **criação personalizada** opção, conforme descrito na [criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md).
2. Sobre o **ações de Script** página do assistente, clique em **adicione a ação de script** para fornecer detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "utiliza a ação de Script para personalizar um cluster")

    |Propriedade|Value|  
    |---|---|  
    |Name|Especifique um nome para a ação de script. Por exemplo, **instalar Giraph**|
    |URI do script|Especifique o identificador URI (Uniform Resource) para o script que é invocado para personalizar o cluster. Por exemplo, *https:\//hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1*|
    |Tipo de Nó|Especifique os nós em que o script de personalização é executado. Pode escolher **todos os nós**, **apenas nós principais**, ou **apenas nós de trabalho**.
    |Parâmetros|Especifique os parâmetros, se necessário, o script. O script para instalar o Giraph não requer quaisquer parâmetros, para que pode deixar em branco.|  

    Pode adicionar mais do que uma ação de script para instalar vários componentes no cluster. Depois de ter adicionado os scripts, clique na marca de verificação para começar a criar o cluster.

## <a name="use-giraph"></a>Utilizar Giraph
Usamos o exemplo de SimpleShortestPathsComputation para demonstrar o básico <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementação para localizar o caminho mais curto entre objetos num gráfico. Utilize os seguintes passos para carregar os dados de exemplo e o jar do exemplo, execute uma tarefa usando o exemplo de SimpleShortestPathsComputation e, em seguida, ver os resultados.

1. Carregar um ficheiro de dados de exemplo para o armazenamento de Blobs do Azure. Na estação de trabalho local, crie um novo ficheiro designado **tiny_graph.txt**. Esta deve conter as seguintes linhas:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Carregar o ficheiro de tiny_graph.txt para armazenamento primário para o seu cluster do HDInsight. Para obter instruções sobre como carregar dados, consulte [carregar dados para as tarefas do Apache Hadoop no HDInsight](hdinsight-upload-data.md).

    Estes dados descreve uma relação entre objetos num gráfico direcionado, utilizando o formato [origem\_id, origem\_valor, [[dest\_id], [edge\_valor],...]]. Cada linha representa uma relação entre um **origem\_id** objeto e um ou mais **dest\_id** objetos. O **edge\_valor** (ou de peso) pode ser considerada como a força ou a distância da ligação entre **source_id** e **dest\_id**.

    Desenhado, e utilizar o valor (ou o peso) como a distância entre objetos, os dados acima podem ser assim:

    ![tiny_graph.txt desenhado como círculos com linhas de variados distância entre](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

2. Execute o exemplo de SimpleShortestPathsComputation. Utilize os seguintes cmdlets PowerShell do Azure para executar o exemplo ao utilizar o ficheiro de tiny_graph.txt como entrada.

    > [!IMPORTANT]  
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Gestor de Serviços do Azure está **preterido**, e foi removido a 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga os passos em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets que funcionam com o Azure Resource Manager, veja [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    No exemplo acima, substitua **clustername** com o nome do cluster do HDInsight com o Giraph instalado.
3. Veja os resultados. Assim que a tarefa estiver concluída, os resultados serão armazenados em dois arquivos de saída no **wasb: / / / exemplo/out/shotestpaths** pasta. Os ficheiros são chamados **parte-m-00001** e **parte-m-00002**. Execute os seguintes passos para transferir e ver o resultado:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Esta ação irá criar a **exemplo/saída/shortestpaths** estrutura de diretórios no diretório atual na sua estação de trabalho e transferir os dois ficheiros de saída para essa localização.

    Utilize o **Cat** cmdlet para exibir o conteúdo dos ficheiros:

        Cat example/output/shortestpaths/part*

    O resultado deverá ser semelhante ao seguinte:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exemplo estiver codificado para começar a 1 de ID de objeto e localizar o caminho mais curto para outros objetos. Para que a saída deve ser lida como `destination_id distance`, em que a distância é o valor (ou o peso) das margens deslocadas entre 1 de ID de objeto e o ID de destino.

    Visualizar isso, pode verificar os resultados ao viajando os caminhos mais curto entre 1 de ID e todos os outros objetos. Tenha em atenção que o caminho mais curto entre ID 1 e 4 de ID é 5. Esta é a distância total entre <span style="color:orange">ID de 1 e 3</span>e, em seguida <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como círculos com caminhos mais desenhados entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-azure-powershell"></a>Instalar o Giraph com o Azure PowerShell
Ver [HDInsight personalizar clusters com ação de Script](hdinsight-hadoop-customize-cluster-linux.md).  O exemplo demonstra como instalar o Apache Spark com o Azure PowerShell. É necessário personalizar o script a utilizar [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Instalar o Giraph com o .NET SDK
Ver [HDInsight personalizar clusters com ação de Script](hdinsight-hadoop-customize-cluster-linux.md). O exemplo demonstra como instalar o Spark com o SDK .NET. É necessário personalizar o script a utilizar [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Consulte também
* [Instalar o Apache Giraph nos clusters do HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Criar clusters do Apache Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de clusters do HDInsight.
* [Personalizar clusters de HDInsight com a ação de Script](hdinsight-hadoop-customize-cluster-linux.md): informações gerais sobre como personalizar clusters do HDInsight com ação de Script.
* [Desenvolver scripts de ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalar e utilizar o Apache Spark no HDInsight clusters][hdinsight-install-spark]: Exemplo de ação de script sobre como instalar o Spark.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
