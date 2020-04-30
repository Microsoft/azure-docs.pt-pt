---
title: 'Tutorial: Criar um oleoduto ETL de ponta a ponta para obter insights de vendas no Azure HDInsight'
description: Aprenda a usar a criação de oleodutos ETL com o Azure HDInsight para obter insights a partir de dados de vendas utilizando clusters spark on-demand e Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535235"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para obter insights de vendas no Azure HDInsight

Neste tutorial, você vai construir um pipeline de dados de ponta a ponta que executa operações de extração, transformação e carga (ETL). O gasoduto utilizará os clusters [Apache Spark](./spark/apache-spark-overview.md) e Apache Hive em funcionamento no Azure HDInsight para consulta e manipulação dos dados. Também utilizará tecnologias como o Azure Data Lake Storage Gen2 para armazenamento de dados e Power BI para visualização.

Este pipeline de dados combina os dados de várias lojas, remove quaisquer dados indesejados, anexa novos dados e carrega tudo isso de volta ao seu armazenamento para visualizar insights de negócio. Leia mais sobre os oleodutos ETL em [Extrato, transformação e carga (ETL) à escala](./hadoop/apache-hadoop-etl-at-scale.md).

![Arquitetura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Azure CLI - pelo menos versão 2.2.0. Ver [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* jq, um processador JSON de linha de comando.  Vê. [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

* Um membro do [papel azure incorporado - proprietário](../role-based-access-control/built-in-roles.md).

* Se utilizar o PowerShell para acionar o gasoduto Data Factory, necessitará do [Módulo Az](https://docs.microsoft.com/powershell/azure/overview).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) para visualizar insights de negócio no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clone o repositório com scripts e dados

1. Faça login na sua assinatura Azure. Se planeia utilizar a Azure Cloud Shell, então selecione **Experimente no** canto superior direito do bloco de código. Caso contrário, insira o comando abaixo:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Certifique-se de que é membro do [proprietário](../role-based-access-control/built-in-roles.md)do papel Azure. Substitua-a `user@contoso.com` pela sua conta e, em seguida, introduza o comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se nenhum registo for devolvido, não é membro e não poderá completar este tutorial.

1. Descarregue os dados e scripts para este tutorial a partir do [repositório de vendas Da HDInsight .](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) Introduza o seguinte comando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Certifique-se `salesdata scripts templates` de que foram criados. Verifique com o seguinte comando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implementar recursos Azure necessários para o oleoduto

1. Adicione permissões de execução para todos os scripts, entrando:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Definir variável para grupo de recursos. Substitua-o `RESOURCE_GROUP_NAME` pelo nome de um grupo de recursos existente ou novo e, em seguida, introduza o comando:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Execute o script. Substitua-a `LOCATION` por um valor desejado e, em seguida, introduza o comando:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Se não tiver a certeza de qual região especificar, pode obter uma lista de regiões suportadas para a sua subscrição com o comando de localização da [lista az.](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations)

    O comando irá utilizar os seguintes recursos:

    * Uma conta de armazenamento Azure Blob. Esta conta irá conter os dados de vendas da empresa.
    * Uma conta de Armazenamento de Lago De dados Azure Gen2. Esta conta servirá como conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre HDInsight e Data Lake Storage Gen2 na [integração Azure HDInsight com Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Uma identidade gerida atribuída ao utilizador. Esta conta dá aos clusters HDInsight acesso à conta Data Lake Storage Gen2.
    * Um aglomerado de Faíscas Apache. Este cluster será usado para limpar e transformar os dados brutos.
    * Um aglomerado de [consulta interativa](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Este cluster permitirá consultar os dados de vendas e visualizá-lo com o Power BI.
    * Uma rede virtual Azure apoiada por regras do grupo de segurança de rede (NSG). Esta rede virtual permite que os clusters comuniquem e garantam as suas comunicações.

A criação de clusterpode levar cerca de 20 minutos.

A palavra-passe padrão para o acesso `Thisisapassword1`sSH aos clusters é . Se quiser alterar a palavra-passe, vá ao `./templates/resourcesparameters_remainder.json` ficheiro `sparksshPassword` `sparkClusterLoginPassword`e `llapClusterLoginPassword`altere a palavra-passe para os parâmetros e `llapsshPassword` parâmetros.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implementação e recolher informações sobre recursos

1. Se quiser verificar o estado da sua implantação, vá ao grupo de recursos no portal Azure. Em **Definições**, selecione **Implementações**e, em seguida, a sua implantação. Aqui pode ver os recursos que conseguiram mobilizar e os recursos que ainda estão em curso.

1. Para ver os nomes dos clusters, insira o seguinte comando:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Para visualizar a conta de armazenamento azure e a chave de acesso, insira o seguinte comando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Para ver a conta de Armazenamento de Data Lake Gen2 e a chave de acesso, insira o seguinte comando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

A Azure Data Factory é uma ferramenta que ajuda a automatizar os gasodutos Azure. Não é a única maneira de realizar estas tarefas, mas é uma ótima maneira de automatizar os processos. Para obter mais informações sobre a Azure Data Factory, consulte a [documentação da Azure Data Factory.](https://azure.microsoft.com/services/data-factory/)

Esta fábrica de dados terá um oleoduto com duas atividades:

* A primeira atividade irá copiar os dados do armazenamento do Azure Blob para a conta de armazenamento do Data Lake Gen 2 para imitar a ingestão de dados.
* A segunda atividade transformará os dados no cluster Spark. O script transforma os dados removendo colunas indesejadas. Também anexa uma nova coluna que calcula as receitas que uma única transação gera.

Para configurar o seu oleoduto Azure Data Factory, execute o comando abaixo.  Ainda devias estar `hdinsight-sales-insights-etl` no diretório.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Este guião faz as seguintes coisas:

1. Cria um diretor `Storage Blob Data Contributor` de serviço com permissões na conta de armazenamento gen2 de armazenamento do Lago de Dados.
1. Obtém um símbolo de autenticação para autorizar pedidos POST para o sistema de [ficheiros Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real da sua conta de armazenamento `sparktransform.py` `query.hql` gen2 de armazenamento data Lake no e arquivos.
1. Obtém chaves de armazenamento para as contas de armazenamento do Data Lake Gen2 e Blob.
1. Cria outra implantação de recursos para criar um oleoduto Azure Data Factory, com os seus serviços e atividades associados. Passa as chaves de armazenamento como parâmetros para o ficheiro do modelo para que os serviços ligados possam aceder corretamente às contas de armazenamento.

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-data-factory-activities"></a>Desencadear as atividades da Fábrica de Dados

A primeira atividade no oleoduto data factory que criou move os dados do armazenamento blob para data lake storage Gen2. A segunda atividade aplica as transformações de Spark nos dados e guarda os ficheiros .csv transformados para um novo local. Todo o oleoduto pode levar alguns minutos para terminar.

Para recuperar o nome Data Factory, insira o seguinte comando:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Para acionar o oleoduto, pode:

* Desencadear o oleoduto data factory na PowerShell. `RESOURCEGROUP`Substitua, `DataFactoryName` e com os valores apropriados, em seguida, executar os seguintes comandos:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Reexecutar `Get-AzDataFactoryV2PipelineRun` conforme necessário para monitorizar o progresso.

    Ou

* Abra a fábrica de dados e selecione **Author & Monitor**. Desencadeie o `IngestAndTransform` oleoduto a partir do portal. Para obter informações sobre o desencadeamento de gasodutos através do portal, consulte Create a pedido dos [clusters Apache Hadoop em HDInsight utilizando](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)a Azure Data Factory .

Para verificar se o gasoduto está em execução, pode tomar qualquer uma das seguintes etapas:

* Vá à secção **Monitor** na sua fábrica de dados através do portal.
* No Azure Storage Explorer, aceda à sua conta de armazenamento data Lake Gen 2. Vá ao `files` sistema de ficheiros `transformed` e, em seguida, vá à pasta e verifique o seu conteúdo para ver se o gasoduto foi bem sucedido.

Para outras formas de transformar dados utilizando o HDInsight, consulte este artigo sobre a utilização do [Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Crie uma tabela sobre o cluster de consulta interativa para ver dados sobre Power BI

1. Copie `query.hql` o ficheiro para o cluster LLAP utilizando o SCP. Insira o comando:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Lembrete: A palavra-passe predefinida é `Thisisapassword1`.

1. Utilize o SSH para aceder ao cluster LLAP. Insira o comando:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Utilize o seguinte comando para executar o script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Este script criará uma tabela gerida no cluster De consulta interativa a que pode aceder a partir de Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um dashboard Power BI a partir de dados de vendas

1. Abra o Power BI Desktop.

1. Do menu, navegue para **obter mais dados...** > **More...**  > Consulta Interativa **Azure** > **HDInsight.**

1. Selecione **Ligar**.

1. A partir do diálogo **de consulta interativa HDInsight:**
    1. Na caixa de texto **Server,** introduza o nome `https://LLAPCLUSTERNAME.azurehdinsight.net`do seu cluster LLAP no formato de .
    1. Na caixa de texto `default`da base de **dados,** introduza .
    1. Selecione **OK**.

1. Do diálogo **AzureHive:**
    1. Na caixa de texto `admin`do nome do **utilizador,** introduza .
    1. Na caixa de texto `Thisisapassword1` **Password,** introduza .
    1. Selecione **Ligar**.

1. Do **Navigator,** `sales`selecione `sales_raw` e/ou para pré-visualizar os dados. Depois de carregados os dados, pode experimentar com o painel de instrumentos que pretende criar. Consulte os seguintes links para começar com os dashboards Power BI:

* [Introdução aos dashboards para designers do Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Começar com o serviço Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a usar esta aplicação, elimine todos os recursos utilizando o seguinte comando para que não seja cobrado por eles.

1. Para remover o grupo de recursos, insira o comando:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Para remover o diretor de serviço, insira os comandos:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
