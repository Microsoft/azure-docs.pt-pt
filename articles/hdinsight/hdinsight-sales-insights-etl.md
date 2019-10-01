---
title: 'Tutorial: Criar um pipeline de extração, transformação, carregamento (ETL) de ponta a ponta para derivar informações de vendas'
description: Saiba como usar criar pipelines de ETL com o Azure HDInsight para derivar informações de dados de vendas usando clusters e Power BI do Spark sob demanda.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695821"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para derivar informações de vendas

Neste tutorial, você criará um pipeline de dados de ponta a ponta, que executa operações de extração, transformação e carregamento. O pipeline usará Apache Spark e Apache Hive clusters em execução no Azure HDInsight para consultar e manipular os dados. Outras tecnologias usadas incluem Data Lake Storage Gen2 para armazenamento de dados e Power BI para visualização.

Esse pipeline de dados combina os dados de todos os diferentes repositórios, remove os dados indesejados, acrescenta novos dados e carrega isso no armazenamento para visualizar as informações de negócios. Leia mais sobre [extração, transformação e carregamento de pipelines de ETL (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md).

![Arquitetura de ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Baixe [Power bi desktop](https://www.microsoft.com/download/details.aspx?id=45331) para visualizar informações de negócios no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonar o repositório com scripts e dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Abra o Cloud Shell na barra de menus superior. Selecione sua assinatura para criar um compartilhamento de arquivos se você for solicitado por Azure Cloud Shell.

    ![Abrir o Azure cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Selecione "bash" no menu suspenso "selecionar ambiente".
1. Faça logon em sua conta do Azure e defina a assinatura. 
1. Configure o grupo de recursos para o projeto.
    1. Escolha um nome de grupo de recursos exclusivo.
    1. Execute o trecho de código abaixo na Azure Cloud Shell para definir variáveis que serão usadas em etapas posteriores

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Baixe os dados e scripts deste tutorial no [repositório ETL de insights de vendas do HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) digitando os seguintes comandos no Cloud Shell:

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. Digite `ls` no prompt do Shell para ver os seguintes arquivos e diretórios foram criados:

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implantar os recursos do Azure necessários para o pipeline 

1. Adicionar permissões de execução para o script `chmod +x scripts/*.sh`
1. Use o comando `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` para executar o script para implantar os seguintes recursos no Azure:

    1. Uma conta de armazenamento de BLOBs do Azure-essa conta irá conter os dados de vendas da empresa
    2. Uma conta de Azure Data Lake Storage Gen2-essa conta servirá como a conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre o HDInsight e o Data Lake Storage Gen2 na [integração do Azure hdinsight com o data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. Uma identidade gerenciada atribuída pelo usuário-essa conta fornece ao cluster do HDInsight acesso à conta de Data Lake Storage Gen2.
    4. Um Apache Spark cluster-este cluster será usado para limpar e transformar os dados brutos
    5. Um Apache Hive cluster de consulta interativa-esse cluster permitirá consultar os dados de vendas visualizando-os com Power BI
    6. Uma rede virtual do Azure com suporte das regras do NSG (grupo de segurança de rede)-essa rede virtual permite que os clusters se comuniquem e também protejam suas comunicações. 

A criação do cluster pode levar cerca de 20 minutos.

O script `resources.sh` contém o comando a seguir, que usa um modelo do Resource Manager (`resourcestemplate.json`) para criar os recursos especificados com a configuração desejada.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

O script `resources.sh` também carrega os arquivos CSV de dados de vendas na conta de armazenamento de BLOBs recém-criada usando o comando:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

A senha padrão usada para acesso SSH aos clusters é `Thisisapassword1`. Se você quiser alterar a senha, navegue até o arquivo `resourcesparameters.json` e altere a senha para os parâmetros `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implantação e coletar informações de recursos

1. Se você quiser verificar o status da sua implantação, navegue até o grupo de recursos na portal do Azure. Clique em **implantações** em **configurações**. Clique no nome da sua implantação `ResourcesDeployment`. Aqui você pode ver os recursos que foram implantados com êxito e os que ainda estão em andamento.
1. Depois que a implantação for concluída, vá para o portal do Azure > **grupos de recursos** > < RESOURCE_GROUP_NAME >
1. Localize a nova conta de armazenamento do Azure que foi criada para armazenar os arquivos de vendas. O nome da conta de armazenamento começa com `blob` e, em seguida, contém uma cadeia de caracteres aleatória. 
    1. Anote o nome da conta de armazenamento para uso posterior.
    1. Clique no nome da conta de armazenamento de BLOBs.
    1. No lado esquerdo do portal em **configurações**, clique em **chaves de acesso**.
    1. Copie a cadeia de caracteres na caixa **key1** e salve-a para uso posterior.
1. Localize a conta de Data Lake Storage Gen2 que foi criada como armazenamento para os clusters HDInsight. Essa conta está localizada no mesmo grupo de recursos que a conta de armazenamento de BLOBs, mas começa com `adlsgen2`.
    1. Anote o nome da conta de Data Lake Storage Gen2.
    1. Clique no nome da conta de Data Lake Storage Gen2.
    1. No lado esquerdo do portal, em **configurações**, clique em **chaves de acesso**
    1. Copie a cadeia de caracteres na caixa **key1** e salve-a para uso posterior.

> [!Note]
> Depois de saber os nomes das contas de armazenamento, você também pode obter as chaves de conta usando o seguinte comando no prompt de Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Criar um Azure Data Factory

Azure Data Factory é uma ferramenta que ajuda a automatizar Azure Pipelines. Não é a única maneira de realizar essas tarefas, mas é uma ótima maneira de automatizar esses processos. Para obter mais informações sobre Azure Data Factory, consulte Leia mais sobre a [documentação Azure data Factory](https://azure.microsoft.com/services/data-factory/). 

Esse Azure Data Factory terá um pipeline com duas atividades: 

1. A primeira atividade copiará os dados do armazenamento de BLOBs do Azure para a conta de armazenamento Data Lake Storage Gen 2 para imitar a ingestão de dados.
2. A segunda atividade irá transformar os dados no cluster do Spark. O script transforma os dados removendo colunas indesejadas, bem como acrescentando uma nova coluna que calcula a receita gerada por uma única transação.

Para configurar o pipeline de Azure Data Factory, execute o script `adf.sh`:

1. Adicionar permissões de execução no arquivo usando `chmod +x adf.sh`
1. Execute o script com `./adf.sh` 

Esse script faz o seguinte:

1. Cria uma entidade de serviço com permissões `Storage Blob Data Contributor` na conta de armazenamento Data Lake Storage Gen2.
1. Obtém um token de autenticação para autorizar solicitações POST para a [API REST do sistema de arquivos data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real de sua conta de armazenamento Data Lake Storage Gen2 nos arquivos `sparktransform.py` e `query.hql`.
1. Obtém as chaves de armazenamento para a Data Lake Storage Gen2 e a conta de armazenamento de BLOBs.
1. Cria outra implantação de recurso para criar um pipeline de Azure Data Factory, com seus serviços e atividades vinculados associados. Ele passa as chaves de armazenamento como parâmetros para o arquivo de modelo para que os serviços vinculados possam acessar as contas de armazenamento corretamente.

O pipeline do ADF é implantado usando o seguinte comando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-adf-activities"></a>Disparar as atividades do ADF

A primeira atividade no pipeline do ADF que você criou move os dados do armazenamento de BLOBs para Data Lake Storage Gen2. A segunda atividade, aplica as transformações do Spark nos dados e salva os arquivos CSV transformados em um novo local. O pipeline inteiro pode levar alguns minutos para ser concluído.

Para disparar os pipelines, você pode:

1. Execute os comandos a seguir para disparar os pipelines do ADF no PowerShell. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Você também pode abrir o Data Factory, selecionar autor & monitor e disparar o pipeline de cópia e, em seguida, o pipeline do Spark do Portal. Confira [Criar clusters de Apache Hadoop sob demanda no HDInsight usando Azure data Factory](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline) para obter informações sobre como disparar pipelines por meio do Portal.

Para verificar se os pipelines foram executados, você pode executar uma das seguintes etapas:

1. Navegue até a seção **Monitor** em seu Azure data Factory por meio do Portal.
2. Acesse o Gerenciador de armazenamento da conta de armazenamento do Data Lake Storage Gen 2, vá para o sistema de arquivos do `files` e navegue até a pasta `transformed` e verifique seu conteúdo para ver se o pipeline foi bem-sucedido.

Para outras maneiras de transformar dados usando o HDInsight, confira este artigo sobre como usar o [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Crie uma tabela no cluster de consulta interativa para exibir dados em Power BI

1. Copie o arquivo Query. HQL para o cluster LLAP usando o SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Use o SSH no cluster LLAP usando o comando a seguir e insira sua senha. Se você não tiver alterado o arquivo `resourcesparameters.json`, a senha será `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Execute o comando a seguir para executar o script

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Esse script criará uma tabela gerenciada no cluster de consulta interativa que você pode acessar de Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um painel de Power BI de dados de vendas

1. Abrir Power BI Desktop
1. Selecione **Obter Dados**.
1. Pesquise **cluster de consulta interativa do HDInsight**.
1. Cole o URI do seu cluster lá. Ele deve estar no formato `https://<LLAP CLUSTER NAME>.azurehdinsight.net` `default` para o banco de dados.
1. Insira o nome de usuário e a senha que você usa para acessar o cluster.

Depois que os dados forem carregados, você poderá experimentar o painel que deseja criar. Consulte os links a seguir para obter uma introdução aos painéis de Power BI:

* [Introdução aos painéis para designers de Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Introdução ao serviço do Power BI @ no__t-0

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar este aplicativo, exclua todos os recursos com as etapas a seguir para que não sejam cobrados por eles.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extração, transformação e carregamento (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
