---
title: 'Tutorial: Criar um oleoduto ETL de ponta a ponta para obter insights de vendas em Azure HDInsight'
description: Aprenda a usar os oleodutos ETL com Azure HDInsight para obter insights a partir de dados de vendas utilizando clusters spark on-demand e Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 1031c34a44a253c7458ef78c6371b88014e882ed
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746472"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para obter insights de vendas em Azure HDInsight

Neste tutorial, você vai construir um pipeline de dados de ponta a ponta que executa operações de extração, transformação e carga (ETL). O oleoduto utilizará aglomerados [apache spark](./spark/apache-spark-overview.md) e Apache Hive em execução em Azure HDInsight para consulta e manipulação dos dados. Você também usará tecnologias como Azure Data Lake Storage Gen2 para armazenamento de dados, e Power BI para visualização.

Este pipeline de dados combina os dados de várias lojas, remove quaisquer dados indesejados, anexa novos dados e carrega tudo isso de volta ao seu armazenamento para visualizar insights de negócio. Leia mais sobre os gasodutos ETL em [Extrato, transformação e carga (ETL) à escala.](./hadoop/apache-hadoop-etl-at-scale.md)

![Arquitetura ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Azure CLI - pelo menos a versão 2.2.0. Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli).

* jq, um processador JSON de linha de comando.  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)Ver.

* Membro do [papel incorporado Azure - proprietário](../role-based-access-control/built-in-roles.md).

* Se utilizar o PowerShell para ativar o gasoduto Data Factory, necessitará do [Módulo Az](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) para visualizar insights de negócios no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clone o repositório com scripts e dados

1. Faça login na sua assinatura Azure. Se pretender utilizar o Azure Cloud Shell, selecione **Experimente-o** no canto superior direito do bloco de código. Caso contrário, insira o comando abaixo:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Certifique-se de que é membro do [dono](../role-based-access-control/built-in-roles.md)da função Azure. `user@contoso.com`Substitua-a pela sua conta e, em seguida, insira o comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se nenhum registo for devolvido, não é membro e não poderá completar este tutorial.

1. Descarregue os dados e scripts para este tutorial a partir do [repositório de vendas HDInsight ETL](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Introduza o seguinte comando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Certifique-se de `salesdata scripts templates` que foram criados. Verifique com o seguinte comando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Mobilizar recursos da Azure necessários para o oleoduto

1. Adicione permissões de execução para todos os scripts ao introduzir:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Definir variável para grupo de recursos. `RESOURCE_GROUP_NAME`Substitua-o pelo nome de um grupo de recursos existente ou novo e, em seguida, insira o comando:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Execute o script. `LOCATION`Substitua-o por um valor desejado e, em seguida, introduza o comando:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Se não tiver certeza de qual região especificar, pode recuperar uma lista de regiões apoiadas para a sua subscrição com o comando [de localizações da lista de conta az.](/cli/azure/account#az-account-list-locations)

    O comando irá utilizar os seguintes recursos:

    * Uma conta de armazenamento Azure Blob. Esta conta irá conter os dados de vendas da empresa.
    * Uma conta da Azure Data Lake Storage Gen2. Esta conta servirá como a conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre HDInsight e Data Lake Storage Gen2 em [integração Azure HDInsight com Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Uma identidade gerida atribuída pelo utilizador. Esta conta dá aos clusters HDInsight acesso à conta Desear o Armazém gen2 do Data Lake.
    * Um aglomerado de faíscas Apache. Este cluster será usado para limpar e transformar os dados brutos.
    * Um aglomerado [de consultas interativas](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Este cluster permitirá consultar os dados de vendas e visualizá-lo com o Power BI.
    * Uma rede virtual Azure suportada pelas regras do grupo de segurança da rede (NSG). Esta rede virtual permite que os clusters comuniquem e protejam as suas comunicações.

A criação de clusters pode demorar cerca de 20 minutos.

A palavra-passe padrão para o acesso do SSH aos clusters é `Thisisapassword1` . Se quiser alterar a palavra-passe, vá ao `./templates/resourcesparameters_remainder.json` ficheiro e altere a palavra-passe para os `sparksshPassword` `sparkClusterLoginPassword` `llapClusterLoginPassword` parâmetros e `llapsshPassword` parâmetros.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implementação e recolher informações sobre recursos

1. Se quiser verificar o estado da sua implantação, vá ao grupo de recursos do portal Azure. Em **Definições** , selecione **Implementações** e, em seguida, a sua implantação. Aqui pode ver os recursos que foram implementados com sucesso e os recursos que ainda estão em curso.

1. Para ver os nomes dos clusters, insira o seguinte comando:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Para ver a conta de armazenamento Azure e a chave de acesso, insira o seguinte comando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Para ver a conta de Data Lake Storage Gen2 e a chave de acesso, insira o seguinte comando:

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

A Azure Data Factory é uma ferramenta que ajuda a automatizar os gasodutos Azure. Não é a única maneira de realizar estas tarefas, mas é uma ótima maneira de automatizar os processos. Para obter mais informações sobre a Azure Data Factory, consulte a documentação da [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Esta fábrica de dados terá um oleoduto com duas atividades:

* A primeira atividade copiará os dados do armazenamento de Azure Blob para a conta de armazenamento do Data Lake Storage Gen 2 para imitar a ingestão de dados.
* A segunda atividade transformará os dados no cluster Spark. O script transforma os dados removendo colunas indesejadas. Também anexa uma nova coluna que calcula as receitas que uma única transação gera.

Para configurar o seu oleoduto Azure Data Factory, execute o comando abaixo.  Ainda devia estar no `hdinsight-sales-insights-etl` diretório.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Este guião faz as seguintes coisas:

1. Cria um principal de serviço com `Storage Blob Data Contributor` permissões na conta de armazenamento de Data Lake Gen2.
1. Obtém um token de autenticação para autorizar pedidos POST para o [sistema de ficheiros Desepi de Armazenamento de Dados Lake Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real da sua conta de armazenamento de Data Lake Gen2 nos `sparktransform.py` ficheiros e `query.hql` ficheiros.
1. Obtém chaves de armazenamento para as contas de armazenamento do Data Lake Storage Gen2 e Blob.
1. Cria outra implementação de recursos para criar um oleoduto Azure Data Factory, com os seus serviços e atividades associados. Ele passa as chaves de armazenamento como parâmetros para o ficheiro do modelo para que os serviços ligados possam aceder corretamente às contas de armazenamento.

## <a name="run-the-data-pipeline"></a>Executar o gasoduto de dados

### <a name="trigger-the-data-factory-activities"></a>Desencadear as atividades da Data Factory

A primeira atividade no oleoduto da Fábrica de Dados que criou move os dados do armazenamento blob para a Data Lake Storage Gen2. A segunda atividade aplica as transformações spark nos dados e guarda os ficheiros .csv transformados para uma nova localização. Todo o oleoduto pode levar alguns minutos para terminar.

Para recuperar o nome da Fábrica de Dados, insira o seguinte comando:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Para acionar o oleoduto, pode:

* Acione o oleoduto data factory em PowerShell. Substitua `RESOURCEGROUP` , e `DataFactoryName` pelos valores adequados, em seguida, executar os seguintes comandos:

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

    Re-executar `Get-AzDataFactoryV2PipelineRun` conforme necessário para monitorizar o progresso.

    Ou

* Abra a fábrica de dados e selecione **Author & Monitor** . Dispare o `IngestAndTransform` oleoduto do portal. Para obter informações sobre o desencadeamento de gasodutos através do portal, consulte [crie clusters Apache Hadoop a pedido em HDInsight utilizando a Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Para verificar se o gasoduto está a funcionar, pode tomar qualquer um dos seguintes passos:

* Aceda à secção **Monitor** da sua fábrica de dados através do portal.
* No Azure Storage Explorer, aceda à sua conta de armazenamento do Data Lake Storage Gen 2. Vá ao `files` sistema de ficheiros e, em seguida, vá à pasta e verifique o `transformed` seu conteúdo para ver se o pipeline foi bem sucedido.

Para outras formas de transformar dados utilizando o HDInsight, consulte [este artigo sobre a utilização do Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Criar uma tabela no cluster de consultainterstão interativa para visualizar dados sobre o Power BI

1. Copie o `query.hql` ficheiro para o cluster LLAP utilizando o SCP. Insira o comando:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Lembrete: A palavra-passe padrão é `Thisisapassword1` .

1. Utilize o SSH para aceder ao cluster LLAP. Insira o comando:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Utilize o seguinte comando para executar o script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Este script criará uma tabela gerida no cluster de consultaintere interativa a que pode aceder a partir do Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um painel Power BI a partir de dados de vendas

1. Abra o Power BI Desktop.

1. A partir do menu, navegue para **obter mais dados...**  >  **More...**  >  **Rio Azure**  >  **Consulta Interativa HDInsight** .

1. Selecione **Ligar** .

1. Do diálogo **de consulta interativa HDInsight:**
    1. Na caixa de texto do **Servidor,** insira o nome do seu cluster LLAP no formato de `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. Na caixa de texto da **base de dados,** insira `default` .
    1. Selecione **OK** .

1. Do diálogo **AzureHive:**
    1. Na caixa de texto **do nome do utilizador,** insira `admin` .
    1. Na caixa de texto da **palavra-passe,** insira `Thisisapassword1` .
    1. Selecione **Ligar** .

1. A partir do **Navigator,** selecione `sales` e/ou `sales_raw` para pré-visualizar os dados. Depois de os dados estiverem carregados, pode experimentar com o painel de instrumentos que pretende criar. Consulte os seguintes links para começar com os dashboards Power BI:

* [Introdução aos dashboards para designers do Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Introdução ao serviço Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a utilizar esta aplicação, elimine todos os recursos utilizando o seguinte comando para que não seja cobrado por eles.

1. Para remover o grupo de recursos, insira o comando:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Para remover o principal de serviço, insira os comandos:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
