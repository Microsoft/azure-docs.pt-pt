---
title: 'Tutorial: Criar um pipeline de ETL de ponta a ponta para derivar informações de vendas'
description: Saiba como usar criar pipelines de ETL com o Azure HDInsight para derivar informações de dados de vendas usando clusters e Power BI do Spark sob demanda.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: b9bcaf4b7497e8beba377eb7e47a44a6eb061299
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178011"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para derivar informações de vendas

Neste tutorial, você criará um pipeline de dados de ponta a ponta que executa operações de extração, transformação e carregamento (ETL). O pipeline usará Apache Spark e Apache Hive clusters em execução no Azure HDInsight para consultar e manipular os dados. Você também usará tecnologias como Azure Data Lake Storage Gen2 para armazenamento de dados e Power BI para visualização.

Esse pipeline de dados combina os dados de vários repositórios, remove os dados indesejados, acrescenta novos dados e carrega tudo isso de volta para o armazenamento para visualizar informações de negócios. Leia mais sobre pipelines de ETL em [extração, transformação e carregamento (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md).

![Arquitetura de ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Baixe [Power bi desktop](https://www.microsoft.com/download/details.aspx?id=45331) para visualizar informações de negócios no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonar o repositório com scripts e dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra Azure Cloud Shell na barra de menus superior. Selecione sua assinatura para criar um compartilhamento de arquivos se Cloud Shell solicitar.

   ![Abrir o Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. No menu suspenso **selecionar ambiente** , escolha **bash**.
1. Entre em sua conta do Azure e defina a assinatura. 
1. Configure o grupo de recursos para o projeto.
   1. Escolha um nome exclusivo para o grupo de recursos.
   1. Execute o seguinte trecho de código em Cloud Shell para definir variáveis que serão usadas em etapas posteriores:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Baixe os dados e scripts deste tutorial no [repositório ETL de insights de vendas do HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) inserindo os seguintes comandos no Cloud Shell:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Insira `ls` no prompt do Shell para ver se os seguintes arquivos e diretórios foram criados:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implantar os recursos do Azure necessários para o pipeline 

1. Adicione permissões de execução para o script `chmod +x scripts/*.sh`.
1. Use o comando `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` para executar o script para implantar os seguintes recursos no Azure:

   1. Uma conta de armazenamento de BLOBs do Azure. Essa conta manterá os dados de vendas da empresa.
   2. Uma conta de Azure Data Lake Storage Gen2. Essa conta servirá como a conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre o HDInsight e o Data Lake Storage Gen2 na [integração do Azure hdinsight com o data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. Uma identidade gerenciada atribuída pelo usuário. Essa conta permite que os clusters HDInsight acessem a conta de Data Lake Storage Gen2.
   4. Um cluster Apache Spark. Esse cluster será usado para limpar e transformar os dados brutos.
   5. Um Apache Hive cluster de consulta interativa. Esse cluster permitirá consultar os dados de vendas e visualizando-os com Power BI.
   6. Uma rede virtual do Azure com suporte das regras do NSG (grupo de segurança de rede). Essa rede virtual permite que os clusters se comuniquem e protejam suas comunicações. 

A criação do cluster pode levar cerca de 20 minutos.

O script `resources.sh` contém o comando a seguir. Esse comando usa um modelo de Azure Resource Manager (`resourcestemplate.json`) para criar os recursos especificados com a configuração desejada.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

O script `resources.sh` também carrega os arquivos. csv dos dados de vendas na conta de armazenamento de BLOBs recém-criada usando este comando:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

A senha padrão para acesso SSH aos clusters é `Thisisapassword1`. Se você quiser alterar a senha, vá para o arquivo `resourcesparameters.json` e altere a senha para os parâmetros `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implantação e coletar informações de recursos

1. Se você quiser verificar o status da sua implantação, vá para o grupo de recursos no portal do Azure. Selecione **implantações** em **configurações**. Selecione o nome da sua implantação, `ResourcesDeployment`. Aqui você pode ver os recursos que foram implantados com êxito e os recursos que ainda estão em andamento.
1. Após a conclusão da implantação, vá para o portal do Azure > **grupos de recursos** > < RESOURCE_GROUP_NAME >.
1. Localize a nova conta de armazenamento do Azure que foi criada para armazenar os arquivos de vendas. O nome da conta de armazenamento começa com `blob` e, em seguida, contém uma cadeia de caracteres aleatória. Faça o seguinte:
   1. Anote o nome da conta de armazenamento para uso posterior.
   1. Selecione o nome da conta de armazenamento de BLOBs.
   1. No lado esquerdo do portal em **configurações**, selecione chaves de **acesso**.
   1. Copie a cadeia de caracteres na caixa **key1** e salve-a para uso posterior.
1. Localize a conta de Data Lake Storage Gen2 que foi criada como armazenamento para os clusters HDInsight. Essa conta está localizada no mesmo grupo de recursos que a conta de armazenamento de BLOBs, mas começa com `adlsgen2`. Faça o seguinte:
   1. Anote o nome da conta de Data Lake Storage Gen2.
   1. Selecione o nome da conta de Data Lake Storage Gen2.
   1. No lado esquerdo do portal, em **configurações**, selecione chaves de **acesso**.
   1. Copie a cadeia de caracteres na caixa **key1** e salve-a para uso posterior.

> [!Note]
> Depois de saber os nomes das contas de armazenamento, você pode obter as chaves de conta usando o seguinte comando no prompt de Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Azure Data Factory é uma ferramenta que ajuda a automatizar os pipelines do Azure. Não é a única maneira de realizar essas tarefas, mas é uma ótima maneira de automatizar os processos. Para obter mais informações sobre Azure Data Factory, consulte a [documentação do Azure data Factory](https://azure.microsoft.com/en-us/services/data-factory/). 

Esse data factory terá um pipeline com duas atividades: 

- A primeira atividade copiará os dados do armazenamento de BLOBs do Azure para a conta de armazenamento Data Lake Storage Gen 2 para imitar a ingestão de dados.
- A segunda atividade irá transformar os dados no cluster do Spark. O script transforma os dados removendo colunas indesejadas. Ele também acrescenta uma nova coluna que calcula a receita que uma única transação gera.

Para configurar o pipeline de Azure Data Factory, execute o script `adf.sh`:

1. Use `chmod +x adf.sh` para adicionar permissões de execução no arquivo.
1. Use `./adf.sh` para executar o script. 

Esse script faz o seguinte:

1. Cria uma entidade de serviço com permissões `Storage Blob Data Contributor` na conta de armazenamento Data Lake Storage Gen2.
1. Obtém um token de autenticação para autorizar solicitações POST para a [API REST do sistema de arquivos data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real de sua conta de armazenamento Data Lake Storage Gen2 nos arquivos `sparktransform.py` e `query.hql`.
1. Obtém as chaves de armazenamento para as contas de armazenamento de Data Lake Storage Gen2 e BLOB.
1. Cria outra implantação de recurso para criar um pipeline de Azure Data Factory, com seus serviços e atividades vinculados associados. Ele passa as chaves de armazenamento como parâmetros para o arquivo de modelo para que os serviços vinculados possam acessar as contas de armazenamento corretamente.

O pipeline de Data Factory é implantado por meio do seguinte comando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-data-factory-activities"></a>Disparar as atividades de Data Factory

A primeira atividade no pipeline de Data Factory que você criou move os dados do armazenamento de BLOBs para Data Lake Storage Gen2. A segunda atividade aplica as transformações do Spark nos dados e salva os arquivos. csv transformados em um novo local. O pipeline inteiro pode levar alguns minutos para ser concluído.

Para disparar os pipelines, você pode:

- Execute os seguintes comandos para disparar os pipelines de Data Factory no PowerShell: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Abra o data factory e selecione **criar & monitor**. Dispare o pipeline de cópia e, em seguida, o pipeline do Spark do Portal. Para obter informações sobre como disparar pipelines por meio do portal, consulte [Criar clusters de Apache Hadoop sob demanda no HDInsight usando Azure data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Para verificar se os pipelines foram executados, você pode executar uma das seguintes etapas:

- Vá para a seção **Monitor** em seu data Factory por meio do Portal.
- Em Gerenciador de Armazenamento do Azure, vá para sua conta de armazenamento do Data Lake Storage Gen 2. Vá para o sistema de arquivos `files` e, em seguida, vá para a pasta `transformed` e verifique seu conteúdo para ver se o pipeline foi bem-sucedido.

Para obter outras maneiras de transformar dados usando o HDInsight, consulte [Este artigo sobre como usar Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Crie uma tabela no cluster de consulta interativa para exibir dados em Power BI

1. Copie o arquivo `query.hql` para o cluster LLAP usando o SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Use o SSH para acessar o cluster LLAP usando o comando a seguir e, em seguida, insira sua senha. Se você não tiver alterado o arquivo `resourcesparameters.json`, a senha será `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Use o seguinte comando para executar o script:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Esse script criará uma tabela gerenciada no cluster de consulta interativa que você pode acessar de Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um painel de Power BI de dados de vendas

1. Abra o Power BI Desktop.
1. Selecione **Obter Dados**.
1. Pesquise **cluster de consulta interativa do HDInsight**.
1. Cole o URI do seu cluster lá. Deverá estar no formato `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

   Insira `default` para o banco de dados.
1. Insira o nome de usuário e a senha que você usa para acessar o cluster.

Depois que os dados são carregados, você pode experimentar o painel que deseja criar. Consulte os links a seguir para começar a usar os painéis de Power BI:

* [Introdução aos painéis para designers de Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Introdução ao serviço do Power BI @ no__t-0

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua todos os recursos usando o comando a seguir para que não seja cobrado por eles.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extração, transformação e carregamento (ETL) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
