---
title: 'Tutorial: Criar um oleoduto ETL de ponta a ponta para obter insights de vendas no Azure HDInsight'
description: Aprenda a usar a criação de oleodutos ETL com o Azure HDInsight para obter insights a partir de dados de vendas utilizando clusters spark on-demand e Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247270"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para obter insights de vendas no Azure HDInsight

Neste tutorial, você vai construir um pipeline de dados de ponta a ponta que executa operações de extração, transformação e carga (ETL). O gasoduto utilizará os clusters [Apache Spark](./spark/apache-spark-overview.md) e Apache Hive em funcionamento no Azure HDInsight para consulta e manipulação dos dados. Também utilizará tecnologias como o Azure Data Lake Storage Gen2 para armazenamento de dados e Power BI para visualização.

Este pipeline de dados combina os dados de várias lojas, remove quaisquer dados indesejados, anexa novos dados e carrega tudo isso de volta ao seu armazenamento para visualizar insights de negócio. Leia mais sobre os oleodutos ETL em [Extrato, transformação e carga (ETL) à escala](./hadoop/apache-hadoop-etl-at-scale.md).

![Arquitetura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Azure CLI. Ver [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Um membro do [papel azure incorporado - proprietário](../role-based-access-control/built-in-roles.md).

* [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) para visualizar insights de negócio no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clone o repositório com scripts e dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Abra a Cloud Shell Azure a partir da barra de menu seleções. Selecione a sua subscrição para criar uma partilha de ficheiros se a Cloud Shell lhe solicitar.

   ![Abrir o Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. No menu de entrega do **ambiente Select,** escolha **Bash**.

1. Certifique-se de que é membro do [proprietário](../role-based-access-control/built-in-roles.md)do papel Azure. Substitua-a `user@contoso.com` pela sua conta e, em seguida, introduza o comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se nenhum registo for devolvido, não é membro e não poderá completar este tutorial.

1. Enumera rindo as suas assinaturas que entram no comando:

    ```azurecli
    az account list --output table
    ```

    Tenha em atenção a identificação da subscrição que utilizará para este projeto.

1. Detete a subscrição que utilizará para este projeto. Substitua-o `SUBSCRIPTIONID` pelo valor real e, em seguida, introduza o comando.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Crie um novo grupo de recursos para o projeto. Substitua `RESOURCEGROUP` pelo nome desejado e, em seguida, introduza o comando.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Descarregue os dados e scripts para este tutorial a partir do [repositório de vendas Da HDInsight .](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)  Introduza o seguinte comando:

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

1. Execute o script. `RESOURCE_GROUP_NAME` Substitua `LOCATION` e com os valores relevantes, em seguida, insira o comando:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    O comando irá utilizar os seguintes recursos:

    * Uma conta de armazenamento Azure Blob. Esta conta irá conter os dados de vendas da empresa.
    * Uma conta de Armazenamento de Lago De dados Azure Gen2. Esta conta servirá como conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre HDInsight e Data Lake Storage Gen2 na [integração Azure HDInsight com Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Uma identidade gerida atribuída ao utilizador. Esta conta dá aos clusters HDInsight acesso à conta Data Lake Storage Gen2.
    * Um aglomerado de Faíscas Apache. Este cluster será usado para limpar e transformar os dados brutos.
    * Um aglomerado de [consulta interativa](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Este cluster permitirá consultar os dados de vendas e visualizá-lo com o Power BI.
    * Uma rede virtual Azure apoiada por regras do grupo de segurança de rede (NSG). Esta rede virtual permite que os clusters comuniquem e garantam as suas comunicações.

A criação de clusterpode levar cerca de 20 minutos.

O `resources.sh` guião contém os seguintes comandos. Não é necessário que execute estes comandos se já executou o guião no passo anterior.

* `az group deployment create`- Este comando utiliza um modelo`resourcestemplate.json`de Gestor de Recursos Azure () para criar os recursos especificados com a configuração desejada.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch`- Este comando envia os dados de venda .csv ficheiros para a conta de armazenamento Blob recém-criada utilizando este comando:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

A palavra-passe padrão para o acesso `Thisisapassword1`sSH aos clusters é . Se quiser alterar a palavra-passe, vá ao `resourcesparameters.json` ficheiro `sparksshPassword` `sparkClusterLoginPassword`e `llapClusterLoginPassword`altere a palavra-passe para os parâmetros e `llapsshPassword` parâmetros.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implementação e recolher informações sobre recursos

1. Se quiser verificar o estado da sua implantação, vá ao grupo de recursos no portal Azure. Selecione **Implementações** em **Definições**. Selecione o nome `ResourcesDeployment`da sua implantação, . Aqui pode ver os recursos que conseguiram mobilizar e os recursos que ainda estão em curso.

1. Para ver os nomes dos clusters, insira o seguinte comando:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Para visualizar a conta de armazenamento azure e a chave de acesso, insira o seguinte comando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Para ver a conta de Armazenamento de Data Lake Gen2 e a chave de acesso, insira o seguinte comando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

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

Para configurar o seu gasoduto Azure Data Factory, execute o seguinte comando:

```bash
./scripts/adf.sh
```

Este guião faz as seguintes coisas:

1. Cria um diretor `Storage Blob Data Contributor` de serviço com permissões na conta de armazenamento gen2 de armazenamento do Lago de Dados.
1. Obtém um símbolo de autenticação para autorizar pedidos POST para o sistema de [ficheiros Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real da sua conta de armazenamento `sparktransform.py` `query.hql` gen2 de armazenamento data Lake no e arquivos.
1. Obtém chaves de armazenamento para as contas de armazenamento do Data Lake Gen2 e Blob.
1. Cria outra implantação de recursos para criar um oleoduto Azure Data Factory, com os seus serviços e atividades associados. Passa as chaves de armazenamento como parâmetros para o ficheiro do modelo para que os serviços ligados possam aceder corretamente às contas de armazenamento.

O gasoduto Data Factory é implantado através do seguinte comando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-data-factory-activities"></a>Desencadear as atividades da Fábrica de Dados

A primeira atividade no oleoduto data factory que criou move os dados do armazenamento blob para data lake storage Gen2. A segunda atividade aplica as transformações de Spark nos dados e guarda os ficheiros .csv transformados para um novo local. Todo o oleoduto pode levar alguns minutos para terminar.

Para acionar os oleodutos, pode:

* Desencadear os oleodutos data factory na PowerShell. Substitua-a `DataFactoryName` com o nome real data Factory e, em seguida, execute os seguintes comandos:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    Ou

* Abra a fábrica de dados e selecione **Author & Monitor**. Aciona o gasoduto de cópia e, em seguida, o oleoduto Spark a partir do portal. Para obter informações sobre o desencadeamento de gasodutos através do portal, consulte Create a pedido dos [clusters Apache Hadoop em HDInsight utilizando](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)a Azure Data Factory .

Para verificar se os gasodutos estão em execução, pode tomar qualquer uma das seguintes etapas:

* Vá à secção **Monitor** na sua fábrica de dados através do portal.
* No Azure Storage Explorer, aceda à sua conta de armazenamento data Lake Gen 2. Vá ao `files` sistema de ficheiros `transformed` e, em seguida, vá à pasta e verifique o seu conteúdo para ver se o gasoduto foi bem sucedido.

Para outras formas de transformar dados utilizando o HDInsight, consulte este artigo sobre a utilização do [Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Crie uma tabela sobre o cluster de consulta interativa para ver dados sobre Power BI

1. Copie `query.hql` o ficheiro para o cluster LLAP utilizando o SCP. Substitua `LLAPCLUSTERNAME` pelo nome real e, em seguida, introduza o comando:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Utilize o SSH para aceder ao cluster LLAP. Substitua-o `LLAPCLUSTERNAME` pelo nome real e, em seguida, introduza o comando. Se não alterou o `resourcesparameters.json` ficheiro, a `Thisisapassword1`palavra-passe é .

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Utilize o seguinte comando para executar o script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Este script criará uma tabela gerida no cluster De consulta interativa a que pode aceder a partir de Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um dashboard Power BI a partir de dados de vendas

1. Abra o Power BI Desktop.
1. Selecione **Obter Dados**.
1. Pesquisa por **HDInsight Interactive Query cluster**.
1. Cola o URI para o teu aglomerado lá. Deverá estar no formato `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Insira `default` a base de dados.
1. Introduza o nome de utilizador e a palavra-passe que utiliza para aceder ao cluster.

Depois de carregados os dados, pode experimentar com o painel de instrumentos que pretende criar. Consulte os seguintes links para começar com os dashboards Power BI:

* [Introdução aos dashboards para designers do Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Começar com o serviço Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a usar esta aplicação, elimine todos os recursos utilizando o seguinte comando para que não seja cobrado por eles.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
