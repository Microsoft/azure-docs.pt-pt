---
title: 'Quickstart: Crie o cluster Apache Spark em Azure HDInsight com Azure PowerShell'
description: Este guia de início rápido mostra como pode utilizar o Azure PowerShell para criar um cluster do Apache Spark no Azure HDInsight e executar uma consulta simples do Spark SQL.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 7a04d65d5032e37433fb61c73e5f64fe89bb2d2c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930494"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Quickstart: Criar cluster Apache Spark em Azure HDInsight usando PowerShell

Neste arranque rápido, você usa Azure PowerShell para criar um cluster Apache Spark em Azure HDInsight. Em seguida, você cria um Caderno Jupyter, e usá-lo para executar consultas Spark SQL contra as mesas apache hive. O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. A estrutura Apache Spark para Azure HDInsight permite uma análise rápida de dados e computação de cluster utilizando o processamento na memória. O Jupyter Notebook permite interagir com os seus dados, combinar código com texto de marcação e fazer visualizações simples.

[Visão geral: Apache Spark em Azure HDInsight](apache-spark-overview.md)  |  [Faísca](https://spark.apache.org/)  |  Apache [Colmeia](https://hive.apache.org/)  |  Apache [Caderno Jupyter](https://jupyter.org/)

Se estiver a utilizar vários clusters em conjunto, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark também vai querer usar o Conector do Armazém da Colmeia. Para obter mais informações, consulte [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) and Integrate Apache Spark and Apache [Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisite"></a>Pré-requisito

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- O [módulo PowerShell Az](/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Criar um cluster Apache Spark em HDInsight

> [!IMPORTANT]  
> A faturação dos clusters do HDInsight é rateada ao minuto, quer esteja a utilizá-los ou não. Confirme que elimina o cluster depois de o utilizar. Para obter mais informações, consulte a secção [Limpar recursos](#clean-up-resources) deste artigo.

Para criar um cluster no HDInsight, terá de criar, entre outros, os seguintes objetos e recursos do Azure:

- Um grupo de recursos do Azure. Um grupo de recursos do Azure é um contentor de recursos do Azure.
- Uma conta de armazenamento Azure ou armazenamento do Lago de Dados Azure.  Cada cluster do HDInsight necessita de um armazenamento de dados dependente. Neste arranque rápido, você cria um cluster que usa Azure Storage Blobs como o armazenamento de cluster. Para obter mais informações sobre como utilizar o Armazenamento do Data Lake Ger2, veja [Início Rápido: Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
- Um aglomerado de diferentes tipos de cluster em HDInsight.  Neste guia de início rápido, irá criar um cluster do Apache Spark 2.3.

Irá utilizar um script do PowerShell para criar os recursos. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Quando executar o script PowerShell, é-lhe solicitado que introduza os seguintes valores:

|Parâmetro|Valor|
|------|------|
|Nome do grupo de recursos do Azure | Indique um nome exclusivo para o grupo de recursos.|
|Localização| Especifique a região do Azure, por exemplo "E.U.A. Central". |
|Nome predefinido da conta de armazenamento | Indique um nome único para a conta de armazenamento. |
|Nome do cluster | Forneça um nome único para o cluster HDInsight.|
|Credenciais de início de sessão do cluster | Esta conta é utilizada para se ligar ao dashboard do cluster mais adiante no guia de início rápido.|
|Credenciais de utilizador SSH | Os clientes SSH podem ser usados para criar uma sessão de linha de comando remota com os clusters em HDInsight.|

1. Selecione **Experimente-o** no canto superior direito para abrir o bloco [de códigos Azure Cloud Shell](../../cloud-shell/overview.md)e, em seguida, siga as instruções para ligar ao Azure.

2. Copie e cole o seguinte script PowerShell na Cloud Shell.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   A criação do cluster demora cerca de 20 minutos. Tem de criar o cluster antes de poder avançar para a sessão seguinte.

Se tiver um problema com a criação de clusters HDInsight, pode ser que não tenha as permissões certas para o fazer. Para obter mais informações, veja [Access control requirements](../hdinsight-hadoop-customize-cluster-linux.md#access-control) (Requisitos do controlo de acesso).

## <a name="create-a-jupyter-notebook"></a>Criar um Bloco de Notas do Jupyter Notebook

[Jupyter Notebook](https://jupyter.org/) é um ambiente de caderno interativo que suporta várias linguagens de programação. O bloco de notas permite-lhe interagir com os seus dados, combinar código com texto markdown e realizar visualizações simples.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **clusters HDInsight**.
   
   ![A screenshot mostra a pesquisa do portal Azure para H D Insight.](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. A partir da lista, selecione o cluster que criou.
   
   ![A screenshot mostra os clusters H D Insight com o cluster que criou.](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. Na página de **visão geral** do cluster, selecione **dashboards cluster** e, em seguida, selecione **Jupyter Notebook**. Se lhe for pedido, introduza as credenciais de início de sessão do cluster.

   ![Open Jupyter Notebook para executar consulta interativa Spark SQL](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook para executar consulta interativa Spark SQL")

1. Selecione **New**  >  **PySpark** para criar um caderno.

   ![Crie um Caderno Jupyter para executar consulta interativa spark SQL](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Crie um Caderno Jupyter para executar consulta interativa spark SQL")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Executar declarações de Apache Spark SQL

SQL (Structured Query Language) é a linguagem mais comum e mais utilizada para consultar e definir dados. O Spark SQL funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

1. Verifique se o kernel está pronto. O kernel está pronto quando vir um círculo hollow junto ao nome do kernel no bloco de notas. O círculo sólido indica que o kernel está ocupado.

    ![estado do kernel](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "estado do kernel")

    Quando inicia o bloco de notas pela primeira vez, o kernel efetua algumas tarefas em segundo plano. Aguarde que o kernel esteja preparado. 
1. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. O comando lista as tabelas do Hive no cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Quando utilizar um Bloco de Notas Jupyter com o seu cluster Spark em HDInsight, obtém-se um predefinição `sqlContext` que pode utilizar para executar consultas de Hive utilizando o Spark SQL. `%%sql` indica ao Bloco de Notas do Jupyter que utilize o `sqlContext` predefinido para executar a consulta do Hive. A consulta devolve as primeiras dez linhas de uma tabela do Hive (**hivesampletable**) que vem em todos os clusters do HDInsight por predefinição. São necessários cerca de 30 segundos para receber os resultados. O resultado tem o seguinte aspeto:

    ![Consulta de Colmeia Apache em Faísca em HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Consulta de colmeia em HDInsight Spark")

    Sempre que executar uma consulta no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)** juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito.

1. Execute outra consulta para ver os dados no `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    O ecrã deve atualizar-se e mostrar o resultado da consulta.

    ![Saída de consulta de colmeia em HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Saída de consulta de colmeia em HDInsight")

1. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)**. Encerrar o bloco de notas liberta os recursos do cluster.

## <a name="clean-up-resources"></a>Limpar os recursos

O HDInsight permite guardar os seus dados no Armazenamento do Azure ou no Armazenamento do Azure Data Lake, pelo que pode eliminar um cluster em segurança quando este não estiver a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Se tenciona trabalhar imediatamente no tutorial listado nos [Passos seguintes](#next-steps), convém manter o cluster.

Regresse ao portal do Azure e selecione **Eliminar**.

![Portal Azure elimina um cluster HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinido.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Limpeza fragmentada com módulo PowerShell Az

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximos passos

Neste quickstart, você aprendeu a criar um cluster Apache Spark em HDInsight e executar uma consulta básica spark SQL. Avance para o próximo tutorial para aprender a usar um cluster HDInsight para executar consultas interativas em dados de amostras.

> [!div class="nextstepaction"]
> [Executar consultas interativas sobre Apache Spark](./apache-spark-load-data-run-query.md)