---
title: 'Tutorial: Clusters de Apache Hadoop sob demanda no Azure HDInsight-Data Factory'
description: Tutorial-saiba como criar clusters de Apache Hadoop sob demanda no HDInsight usando Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 04/18/2019
ms.openlocfilehash: 0b3062e14873ec971163c125fccd6852d8662663
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098730"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Tutorial: Criar clusters de Apache Hadoop sob demanda no HDInsight usando Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste tutorial, você aprenderá a criar um cluster [Apache Hadoop](https://hadoop.apache.org/) , sob demanda, no Azure HDInsight usando Azure data Factory. Em seguida, você usa pipelines de dados no Azure Data Factory para executar trabalhos do hive e excluir o cluster. Ao final deste tutorial, você aprenderá a colocar em operação uma Big Data execução de trabalho em que a criação do cluster, a execução do trabalho e a exclusão do cluster são executadas em um agendamento.

Este tutorial abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Criar uma conta do Storage do Azure
> * Entender Azure Data Factory atividade
> * Criar um data factory usando portal do Azure
> * Criar serviços ligados
> * Criar um pipeline
> * Disparar um pipeline
> * Monitorizar um pipeline
> * Verificar a saída

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* O [módulo AZ](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

* Uma entidade de serviço Azure Active Directory. Depois de criar a entidade de serviço, certifique-se de recuperar a **ID do aplicativo** e a chave de **autenticação** usando as instruções no artigo vinculado. Você precisará desses valores posteriormente neste tutorial. Além disso, verifique se a entidade de serviço é membro da função *colaborador* da assinatura ou do grupo de recursos no qual o cluster é criado. Para obter instruções para recuperar os valores necessários e atribuir as funções corretas, consulte [criar uma Azure Active Directory entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Criar objetos do Azure preliminares

Nesta seção, você cria vários objetos que serão usados para o cluster HDInsight criado sob demanda. A conta de armazenamento criada conterá o script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) de`partitionweblogs.hql`exemplo () que você usa para simular um trabalho de [Apache Hive](https://hive.apache.org/) de exemplo que é executado no cluster.

Esta seção usa um script Azure PowerShell para criar a conta de armazenamento e copiar os arquivos necessários dentro da conta de armazenamento. O script de exemplo Azure PowerShell nesta seção executa as seguintes tarefas:

1. Entra no Azure.
2. Cria um grupo de recursos do Azure.
3. Cria uma Conta de armazenamento do Azure.
4. Cria um contêiner de blob na conta de armazenamento
5. Copia o script HiveQL de exemplo (**partitionweblogs. HQL**) do contêiner de BLOB. O script está disponível em [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). O script de exemplo já está disponível em outro contêiner de blob público. O script do PowerShell abaixo faz uma cópia desses arquivos para a conta de armazenamento do Azure que ele cria.

> [!WARNING]  
> O tipo `BlobStorage` de conta de armazenamento não pode ser usado para clusters HDInsight.

**Para criar uma conta de armazenamento e copiar os arquivos usando Azure PowerShell:**

> [!IMPORTANT]  
> Especifique nomes para o grupo de recursos do Azure e a conta de armazenamento do Azure que será criada pelo script.
> Anote o **nome do grupo de recursos**, o **nome da conta de armazenamento**e a chave da conta de **armazenamento** emitida pelo script. Você precisará deles na próxima seção.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**Para verificar a criação da conta de armazenamento**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos** no painel esquerdo.
3. Selecione o nome do grupo de recursos que você criou no script do PowerShell. Use o filtro se você tiver muitos grupos de recursos listados.
4. No bloco **recursos** , você verá um recurso listado, a menos que compartilhe o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome especificado anteriormente. Selecione o nome de conta de armazenamento.
5. Selecione os blocos de **BLOBs** .
6. Selecione o contêiner **adfgetstarted** . Você verá uma pasta chamada **hivescripts**.
7. Abra a pasta e verifique se ela contém o arquivo de script de exemplo, **partitionweblogs. HQL**.

## <a name="understand-the-azure-data-factory-activity"></a>Entender a atividade de Azure Data Factory

[Azure data Factory](../data-factory/introduction.md) orquestra e automatiza a movimentação e a transformação de dados. Azure Data Factory pode criar um cluster Hadoop do HDInsight just-in-time para processar uma fatia de dados de entrada e excluir o cluster quando o processamento for concluído. 

No Azure Data Factory, um data factory pode ter um ou mais pipelines de dados. Um pipeline de dados tem uma ou mais atividades. Há dois tipos de atividades:

- [Atividades de movimentação de dados](../data-factory/copy-activity-overview.md) – você usa atividades de movimentação de dados para mover dados de um armazenamento de dados de origem para um armazenamento de dados de destino.
- [Atividades de transformação de dados](../data-factory/transform-data.md). Você usa atividades de transformação de dados para transformar/processar dados. A atividade do hive do HDInsight é uma das atividades de transformação com suporte pelo Data Factory. Você usa a atividade de transformação do hive neste tutorial.

Neste artigo, você configura a atividade do hive para criar um cluster Hadoop do HDInsight sob demanda. Quando a atividade é executada para processar dados, aqui está o que acontece:

1. HDInsight An cluster Hadoop é criado automaticamente para você just-in-time para processar a fatia. 

2. Os dados de entrada são processados por meio da execução de um script HiveQL no cluster. Neste tutorial, o script HiveQL associado à atividade Hive executa as seguintes ações:

    - Usa a tabela existente (*hivesampletable*) para criar outra tabela **HiveSampleOut**.
    - Popula a tabela **HiveSampleOut** com apenas colunas específicas do *hivesampletable*original.
    
3. O cluster Hadoop do HDInsight é excluído após a conclusão do processamento e o cluster está ocioso durante o período de tempo configurado (configuração timeToLive). Se a próxima fatia de dados estiver disponível para processamento com nesse horário de ociosidade de timeToLive, o mesmo cluster será usado para processar a fatia.  

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda, navegue até **+ criar uma** > **Data Factory**de**análise** > de recursos.

    ![Azure data Factory no portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure data Factory no portal")

3. Insira ou selecione os seguintes valores para o **novo** bloco de data Factory:

    |Propriedade  |Value  |
    |---------|---------|
    |Name | Insira um nome para o data factory. Este nome tem de ser globalmente exclusivo.|
    |Subscription | Selecione a sua subscrição do Azure. |
    |Resource group | Selecione **usar existente** e, em seguida, selecione o grupo de recursos que você criou usando o script do PowerShell. |
    |Version | Deixe em **v2**. |
    |Location | O local é definido automaticamente para o local especificado ao criar o grupo de recursos anteriormente. Para este tutorial, o local é definido como **leste dos EUA**. |

    ![Criar Azure data Factory usando portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Criar Azure data Factory usando portal do Azure")

4. Selecione **Criar**. A criação de um data factory pode levar de 2 a 4 minutos.

5. Depois que o data factory for criado, você receberá uma notificação de **implantação bem-sucedida** com um botão **ir para recurso** .  Selecione **ir para o recurso** para abrir o data Factory exibição padrão.

6. Selecione **criar & monitor** para iniciar o portal de criação e monitoramento de Azure data Factory.

    ![Visão geral do portal do Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Visão geral de Azure data Factory")

## <a name="create-linked-services"></a>Criar serviços ligados

Nesta seção, você criará dois serviços vinculados dentro de seu data factory.

- Um **serviço ligado do Armazenamento do Azure** que liga uma conta de armazenamento do Azure à fábrica de dados. Este armazenamento é utilizado pelo cluster do HDInsight a pedido. Ele também contém o script do hive que é executado no cluster.
- Um **serviço ligado do HDInsight a pedido**. Azure Data Factory cria automaticamente um cluster HDInsight e executa o script do hive. Em seguida, elimina o cluster do HDInsight depois de o cluster estar inativo durante um período de tempo pré-configurado.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. No painel esquerdo da página **vamos começar** , selecione o ícone **criar** .

    ![Criar um serviço vinculado Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Criar um serviço vinculado Azure data Factory")

2. Selecione **conexões** no canto inferior esquerdo da janela e, em seguida, selecione **+ novo**.

    ![Criar conexões no Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Criar conexões no Azure data Factory")

3. Na caixa de diálogo **novo serviço vinculado** , selecione **armazenamento de BLOBs do Azure** e, em seguida, selecione **continuar**.

    ![Criar serviço vinculado do armazenamento do Azure para data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Criar serviço vinculado do armazenamento do Azure para data Factory")

4. Forneça os seguintes valores para o serviço vinculado de armazenamento:

    |Propriedade |Value |
    |---|---|
    |Name |Introduza `HDIStorageLinkedService`.|
    |Subscrição do Azure |Selecione sua assinatura na lista suspensa.|
    |Nome da conta de armazenamento |Selecione a conta de armazenamento do Azure que você criou como parte do script do PowerShell.|

    Em seguida, selecione **Concluir**.

    ![Forneça o nome do serviço vinculado do armazenamento do Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Forneça o nome do serviço vinculado do armazenamento do Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Crie um serviço ligado do HDInsight a pedido

1. Selecione o botão **+ Novo** novamente para criar outro serviço ligado.

2. Na janela **novo serviço vinculado** , selecione a guia **computação** .

3. Selecione **Azure HDInsight**e, em seguida, selecione **continuar**.

    ![Criar serviço vinculado do HDInsight para Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Criar serviço vinculado do HDInsight para Azure data Factory")

4. Na janela **novo serviço vinculado** , insira os seguintes valores e deixe o restante como padrão:

    | Propriedade | Value |
    | --- | --- |
    | Name | Introduza `HDInsightLinkedService`.|
    | Type | Selecione **HDInsight sob demanda**. |
    | Serviço Ligado do Storage do Azure | Selecione `HDIStorageLinkedService`. |
    | Tipo de cluster | Selecionar **Hadoop** |
    | Time to live | Forneça a duração pela qual você deseja que o cluster HDInsight esteja disponível antes de ser excluído automaticamente.|
    | ID do principal de serviço | Forneça a ID do aplicativo da entidade de serviço Azure Active Directory que você criou como parte dos pré-requisitos. |
    | Chave da entidade de serviço | Forneça a chave de autenticação para a entidade de serviço Azure Active Directory. |
    | Prefixo do nome do cluster | Forneça um valor que será prefixado para todos os tipos de cluster criados pelo data factory. |
    |Subscription |Selecione sua assinatura na lista suspensa.|
    | Selecionar grupo de recursos | Selecione o grupo de recursos que você criou como parte do script do PowerShell que você usou anteriormente.|
    |Selecionar região | Selecione uma região na lista suspensa.|
    | Tipo de so/nome de usuário do SSH do cluster | Insira um nome de usuário SSH, `sshuser`normalmente. |
    | Tipo de so/senha do cluster SSH | Forneça uma senha para o usuário SSH |
    | Tipo de sistema operacional/nome de usuário do cluster | Insira um nome de usuário de cluster `admin`, normalmente. |
    | Tipo de so/senha de usuário do cluster | Forneça uma senha para o usuário do cluster. |

    Em seguida, selecione **Concluir**.

    ![Forneça valores para o serviço vinculado do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Forneça valores para o serviço vinculado do HDInsight")

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o botão **+** (mais) e, em seguida, selecione **Pipeline**.

    ![Criar um pipeline no Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Criar um pipeline no Azure data Factory")

2. Na caixa de ferramentas **atividades** , expanda **HDInsight**e arraste a atividade do **Hive** para a superfície do designer de pipeline. Na guia **geral** , forneça um nome para a atividade.

    ![Adicionar atividades ao pipeline de data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Adicionar atividades ao pipeline de data Factory")

3. Verifique se a atividade do hive está selecionada, selecione a guia **cluster HDI** e, na lista suspensa **serviço vinculado do HDInsight** , selecione o serviço vinculado criado anteriormente, **HDinightLinkedService**, para HDInsight.

    ![Fornecer detalhes do cluster HDInsight para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Fornecer detalhes do cluster HDInsight para o pipeline")

4. Selecione a guia **script** e conclua as seguintes etapas:

    1. Para **serviço vinculado de script**, selecione **HDIStorageLinkedService** na lista suspensa. Esse valor é o serviço vinculado de armazenamento que você criou anteriormente.

    1. Para **caminho do arquivo**, selecione **procurar armazenamento** e navegue até o local onde o script do hive de exemplo está disponível. Se você executou o script do PowerShell anteriormente, esse local `adfgetstarted/hivescripts/partitionweblogs.hql`deve ser.

        ![Fornecer detalhes do script do hive para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Fornecer detalhes do script do hive para o pipeline")

    1. Em**parâmetros** **avançados** > , selecione **preenchimento automático do script**. Essa opção procura quaisquer parâmetros no script do hive que exigem valores em tempo de execução. O script que você usa (**partitionweblogs. HQL**) tem um parâmetro de **saída** . Forneça o **valor** no formato `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontar para uma pasta existente no armazenamento do Azure. O caminho é sensível a maiúsculas e minúsculas. Esse é o caminho em que a saída do script será armazenada. O `wasbs` esquema é necessário porque as contas de armazenamento agora têm a transferência segura necessária habilitada por padrão.
    
        ![Fornecer parâmetros para o script do hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Fornecer parâmetros para o script do hive")

1. Selecione **validar** para validar o pipeline. Selecione o botão **>>** (seta para a direita) para fechar a janela de validação.

    ![Validar o pipeline de Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Validar o pipeline de Azure data Factory")

1. Por fim, selecione **publicar tudo** para publicar os artefatos em Azure data Factory.

    ![Publicar o pipeline de Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publicar o pipeline de Azure data Factory")

## <a name="trigger-a-pipeline"></a>Disparar um pipeline

1. Na barra de ferramentas na superfície do designer, selecione Adicionar gatilho do **gatilho** > **agora**.

    ![Disparar o pipeline de Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Disparar o pipeline de Azure data Factory")

2. Selecione **concluir** na barra lateral pop-up.

## <a name="monitor-a-pipeline"></a>Monitorizar um pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline na lista **Execuções de Pipeline**. Observe o status da execução na coluna **status** .

    ![Monitorar o pipeline de Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorar o pipeline de Azure data Factory")

1. Selecione **Atualizar** para atualizar o estado.

1. Você também pode selecionar o ícone **Exibir execuções de atividade** para ver a execução da atividade associada ao pipeline. Na captura de tela abaixo, você verá apenas uma execução de atividade, pois há apenas uma atividade no pipeline que você criou. Para voltar para o modo de exibição anterior, selecione **pipelines** em direção à parte superior da página.

    ![Monitorar a atividade de pipeline de Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorar a atividade de pipeline de Azure data Factory")

## <a name="verify-the-output"></a>Verificar a saída

1. Para verificar a saída, no portal do Azure navegue até a conta de armazenamento que você usou para este tutorial. Você deve ver as seguintes pastas ou contêineres:

    - Você verá um **adfgerstarted/outputfolder** que contém a saída do script do hive que foi executado como parte do pipeline.

    - Você verá um **adfhdidatafactory\<-link-Service-Name >-\<timestamp >** contêiner. Esse contêiner é o local de armazenamento padrão do cluster HDInsight que foi criado como parte da execução do pipeline.

    - Você verá um contêiner **adfjobs** que tem os logs de trabalho do Azure data Factory.  

        ![Verificar a saída do pipeline de Azure data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Verificar a saída do pipeline de Azure data Factory")

## <a name="clean-up-resources"></a>Limpar recursos

Com a criação de cluster HDInsight sob demanda, você não precisa excluir explicitamente o cluster HDInsight. O cluster é excluído com base na configuração que você forneceu ao criar o pipeline. No entanto, mesmo depois que o cluster é excluído, as contas de armazenamento associadas ao cluster continuam a existir. Esse comportamento é por design, para que você possa manter seus dados intactos. No entanto, se você não quiser manter os dados, poderá excluir a conta de armazenamento que você criou.

Como alternativa, você pode excluir o grupo de recursos inteiro que você criou para este tutorial. Isso exclui a conta de armazenamento e o Azure Data Factory que você criou.

### <a name="delete-the-resource-group"></a>Excluir o grupo de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos de recursos** no painel esquerdo.
1. Selecione o nome do grupo de recursos que você criou no script do PowerShell. Use o filtro se você tiver muitos grupos de recursos listados. Ele abre o grupo de recursos.
1. No bloco **recursos** , você deve ter a conta de armazenamento padrão e a data Factory listada, a menos que você compartilhe o grupo de recursos com outros projetos.
1. Selecione **Eliminar grupo de recursos**. Isso exclui a conta de armazenamento e os dados armazenados na conta de armazenamento.

    ![Portal do Azure excluir grupo de recursos](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Excluir grupo de recursos")

1. Insira o nome do grupo de recursos para confirmar a exclusão e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, você aprendeu a usar Azure Data Factory para criar um cluster HDInsight sob demanda e executar trabalhos de [Apache Hive](https://hive.apache.org/) . Avance para o próximo artigo para saber como criar clusters HDInsight com configuração personalizada.

> [!div class="nextstepaction"]
>[Criar clusters do Azure HDInsight com configuração personalizada](hdinsight-hadoop-provision-linux-clusters.md)
