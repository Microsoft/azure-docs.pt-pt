---
title: 'Tutorial: Agrupamentos a pedido em Azure HDInsight com Data Factory'
description: Tutorial - Aprenda a criar clusters Apache Hadoop a pedido em HDInsight usando a Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ea4f8c33a906bff96ea93f9a7aea3e6f625556cb
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900898"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Tutorial: Criar clusters Apache Hadoop a pedido em HDInsight usando Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste tutorial, aprende-se a criar um cluster [Apache Hadoop,](../hdinsight/hdinsight-overview.md#cluster-types-in-hdinsight) a pedido, em Azure HDInsight usando a Azure Data Factory. Em seguida, utiliza-se oleodutos de dados na Azure Data Factory para executar trabalhos de Colmeia e eliminar o cluster. No final deste tutorial, aprende-se a fazer um grande trabalho de dados onde a `operationalize` criação de clusters, a corrida ao emprego e a eliminação de clusters são feitas em horário.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Compreender a atividade da Fábrica de Dados Azure
> * Criar uma fábrica de dados utilizando o portal Azure
> * Criar serviços ligados
> * Criar um pipeline
> * Acionar um pipeline
> * Monitorizar um pipeline
> * Verificar a saída

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* O Módulo PowerShell [Az](/powershell/azure/) instalado.

* Um diretor de serviço do Azure Ative Directory. Uma vez criado o principal de serviço, certifique-se de recuperar o **ID** da aplicação e **a chave de autenticação** usando as instruções no artigo ligado. Precisa destes valores mais tarde neste tutorial. Além disso, certifique-se de que o principal de serviço é membro do papel *contribuinte* da subscrição ou do grupo de recursos em que o cluster é criado. Para obter instruções para recuperar os valores necessários e atribuir as funções certas, consulte [Criar um diretor de serviço do Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Criar objetos Azure preliminares

Nesta secção, cria vários objetos que serão utilizados para o cluster HDInsight que cria a pedido. A conta de armazenamento criada conterá a amostra do script HiveQL, `partitionweblogs.hql` que você usa para simular uma amostra de Apache Hive que funciona no cluster.

Esta secção utiliza um script Azure PowerShell para criar a conta de armazenamento e copiar sobre os ficheiros necessários dentro da conta de armazenamento. O script da amostra Azure PowerShell nesta secção faz as seguintes tarefas:

1. Assina para Azure.
2. Cria um grupo de recursos Azure.
3. Cria uma Conta de armazenamento do Azure.
4. Cria um recipiente Blob na conta de armazenamento
5. Copia a amostra Do script HiveQL **(partitionweblogs.hql** ) o recipiente Blob. O guião está disponível em [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) . O guião da amostra já está disponível em outro recipiente público blob. O script PowerShell abaixo faz uma cópia destes ficheiros na conta de Armazenamento Azure que cria.

### <a name="create-storage-account-and-copy-files"></a>Criar conta de armazenamento e copiar ficheiros

> [!IMPORTANT]  
> Especifique os nomes do grupo de recursos Azure e da conta de armazenamento Azure que será criada pelo script.
> Anote **o nome do grupo de recursos,** o nome da conta de **armazenamento** e a chave da conta **de armazenamento** outputed by the script. Vai precisar deles na próxima secção.

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

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

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
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

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

### <a name="verify-storage-account"></a>Verificar conta de armazenamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Da esquerda, navegue para **todos os**  >  **General**  >  **serviços Grupos de Recursos Gerais.**
1. Selecione o nome do grupo de recursos que criou no seu script PowerShell. Utilize o filtro se tiver demasiados grupos de recursos listados.
1. A partir da **vista geral,** você vê um recurso listado a menos que você partilhe o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome especificado anteriormente. Selecione o nome da conta de armazenamento.
1. Selecione o **azulejo dos recipientes.**
1. Selecione o recipiente **adfgetstarted.** Vê-se uma pasta chamada **`hivescripts`** .
1. Abra a pasta e certifique-se de que contém o ficheiro de script de amostra, **partitionweblogs.hql** .

## <a name="understand-the-azure-data-factory-activity"></a>Compreender a atividade da Fábrica de Dados Azure

[A Azure Data Factory](../data-factory/introduction.md) orquestra e automatiza o movimento e transformação de dados. A Azure Data Factory pode criar um cluster HdInsight Hadoop just-in-time para processar uma fatia de dados de entrada e eliminar o cluster quando o processamento estiver concluído.

Na Azure Data Factory, uma fábrica de dados pode ter um ou mais oleodutos de dados. Um pipeline de dados tem uma ou mais atividades. Existem dois tipos de atividades:

* [Atividades de Movimento de Dados](../data-factory/copy-activity-overview.md). Utiliza atividades de movimento de dados para mover dados de uma loja de dados de origem para uma loja de dados de destino.
* [Atividades de Transformação de Dados](../data-factory/transform-data.md). Utiliza atividades de transformação de dados para transformar/processar dados. A HDInsight Hive Activity é uma das atividades de transformação apoiadas pela Data Factory. Você usa a atividade de transformação da Colmeia neste tutorial.

Neste artigo, você configura a atividade da Colmeia para criar um cluster hdinsight hadoop a pedido. Quando a atividade corre para processar dados, eis o que acontece:

1. Um cluster HDInsight Hadoop é automaticamente criado para você apenas a tempo de processar a fatia.

2. Os dados de entrada são processados executando um script HiveQL no cluster. Neste tutorial, o script HiveQL associado à atividade da colmeia faz as seguintes ações:

    * Utiliza a tabela existente *(hivesampletable)* para criar outra mesa **HiveSampleOut** .
    * Povoa a tabela **HiveSampleOut** apenas com colunas específicas do *hivesampletable* original.

3. O cluster HDInsight Hadoop é eliminado após o processamento estar completo e o cluster está inativo durante a quantidade de tempo configurada (definição timeToLive). Se a próxima fatia de dados estiver disponível para processamento com neste tempo ParaLive tempo de marcha lenta, o mesmo cluster é usado para processar a fatia.  

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Do menu esquerdo, navegue até à **`+ Create a resource`**  >  **Analytics**  >  **Data Factory.**

    ![Fábrica de Dados Azure no portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Fábrica de Dados Azure no portal")

3. Introduza ou selecione os seguintes valores para o novo azulejo da fábrica de **dados:**

    |Propriedade  |Valor  |
    |---------|---------|
    |Nome | Insira um nome para a fábrica de dados. Este nome tem de ser globalmente exclusivo.|
    |Versão | Deixe na **V2.** |
    |Subscrição | Selecione a sua subscrição do Azure. |
    |Grupo de recursos | Selecione o grupo de recursos criado utilizando o script PowerShell. |
    |Localização | A localização é automaticamente definida para o local especificado enquanto cria o grupo de recursos mais cedo. Para este tutorial, a localização está definida para **o Leste dos EUA.** |
    |Ativar GIT|Desmarque esta caixa.|

    ![Criar fábrica de dados Azure utilizando o portal Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Criar fábrica de dados Azure utilizando o portal Azure")

4. Selecione **Criar** . Criar uma fábrica de dados pode demorar entre 2 a 4 minutos.

5. Assim que a fábrica de dados for criada, receberá uma notificação **de implementação com** um botão **de recurso Go.**  Selecione **Vá para o recurso** para abrir a vista predefinitiva da Data Factory.

6. Selecione **Author & Monitor** para lançar o portal de autoria e monitorização da Azure Data Factory.

    ![Visão geral do portal Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Visão geral da Fábrica de Dados Azure")

## <a name="create-linked-services"></a>Criar serviços ligados

Nesta secção, é autor de dois serviços ligados dentro da sua fábrica de dados.

* Um **serviço ligado do Armazenamento do Azure** que liga uma conta de armazenamento do Azure à fábrica de dados. Este armazenamento é utilizado pelo cluster do HDInsight a pedido. Também contém o script da Colmeia que é executado no cluster.
* Um **serviço ligado a pedido hdinsight** . A Azure Data Factory cria automaticamente um cluster HDInsight e executa o script Hive. Em seguida, elimina o cluster do HDInsight depois de o cluster estar inativo durante um período de tempo pré-configurado.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. A partir do painel esquerdo **do Let's get start** page, selecione o ícone **Autor.**

    ![Criar um serviço ligado à Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Criar um serviço ligado à Azure Data Factory")

2. Selecione **Ligações** do canto inferior esquerdo da janela e, em seguida, selecione **+New** .

    ![Criar ligações na Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Criar ligações na Azure Data Factory")

3. Na caixa de diálogo **new linked service,** selecione **Azure Blob Storage** e, em seguida, selecione **Continue** .

    ![Criar serviço ligado ao armazenamento Azure para a Fábrica de Dados](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Criar serviço ligado ao armazenamento Azure para a Fábrica de Dados")

4. Fornecer os seguintes valores para o serviço ligado ao armazenamento:

    |Propriedade |Valor |
    |---|---|
    |Nome |Introduza `HDIStorageLinkedService`.|
    |Subscrição do Azure |Selecione a sua subscrição a partir da lista de drop-down.|
    |Nome da conta de armazenamento |Selecione a conta de Armazenamento Azure que criou como parte do script PowerShell.|

    Selecione **a ligação de teste** e, se for bem sucedido, selecione **Criar** .

    ![Fornecer nome para serviço ligado ao Armazenamento Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Fornecer nome para serviço ligado ao Armazenamento Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Crie um serviço ligado do HDInsight a pedido

1. Selecione o botão **+ Novo** novamente para criar outro serviço ligado.

2. Na janela **New Linked Service,** selecione o **separador Compute.**

3. Selecione **Azure HDInsight** e, em seguida, **selecione Continue** .

    ![Criar serviço ligado a HDInsight para Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Criar serviço ligado a HDInsight para Azure Data Factory")

4. Na janela **New Linked Service,** insira os seguintes valores e deixe os restantes como padrão:

    | Propriedade | Valor |
    | --- | --- |
    | Nome | Introduza `HDInsightLinkedService`.|
    | Tipo | Selecione **HDInsight a pedido** . |
    | Serviço Ligado do Storage do Azure | Selecione `HDIStorageLinkedService`. |
    | Tipo de cluster | **Selecione hadoop** |
    | Time to live | Forneça a duração durante a qual deseja que o cluster HDInsight esteja disponível antes de ser eliminado automaticamente.|
    | ID principal do serviço | Forneça o ID de aplicação do diretor de serviço azure ative que criou como parte dos pré-requisitos. |
    | Chave principal do serviço | Forneça a chave de autenticação para o diretor de serviço Azure Ative. |
    | Prefixo de nome de cluster | Fornecer um valor que será pré-fixado a todos os tipos de cluster criados pela fábrica de dados. |
    |Subscrição |Selecione a sua subscrição a partir da lista de drop-down.|
    | Selecionar o grupo de recursos | Selecione o grupo de recursos que criou como parte do script PowerShell que usou anteriormente.|
    | Nome de utilizador do tipo DE/Cluster SSH | Introduza um nome de utilizador SSH, `sshuser` comumente. |
    | Palavra-passe tipo DE TIPO DE/Cluster SSH | Forneça uma senha para o utilizador SSH |
    | Nome de utilizador do tipo de SO/Cluster | Introduza um nome de utilizador do cluster, comumente `admin` . |
    | Palavra-passe tipo DE/Cluster | Forneça uma palavra-passe para o utilizador do cluster. |

    Em seguida, selecione **Criar** .

    ![Fornecer valores para o serviço ligado ao HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Fornecer valores para o serviço ligado ao HDInsight")

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o **+** botão (mais) e, em seguida, selecione **Pipeline** .

    ![Criar um oleoduto na Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Criar um oleoduto na Azure Data Factory")

1. Na caixa de **ferramentas Atividades,** expanda a **HDInsight** e arraste a atividade da **Colmeia** para a superfície do designer de gasodutos. No separador **Geral,** forneça um nome para a atividade.

    ![Adicionar atividades ao pipeline da Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Adicionar atividades ao pipeline da Data Factory")

1. Certifique-se de que tem a atividade da Colmeia selecionada, selecione o **separador Cluster HDI.** E a partir da lista de drop-down **do HDInsight Linked Service,** selecione o serviço ligado que criou anteriormente, **HDInsightLinkedService,** para HDInsight.

    ![Fornecer detalhes do cluster HDInsight para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Fornecer detalhes do cluster HDInsight para o pipeline")

1. Selecione o **separador Script** e complete os seguintes passos:

    1. Para **o Serviço ligado ao script** , selecione **HDIStorageLinkedService** da lista de suspensos. Este valor é o serviço de armazenamento ligado que criou anteriormente.

    1. Para **o Caminho do Ficheiro** , **selecione Procurar armazenamento** e navegue até ao local onde está disponível o script da Colmeia. Se executou o script PowerShell mais cedo, esta localização deve ser `adfgetstarted/hivescripts/partitionweblogs.hql` .

        ![Forneça detalhes do script da Hive para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Forneça detalhes do script da Hive para o pipeline")

    1. Nos **Advanced**  >  **parâmetros** avançados, selecione **`Auto-fill from script`** . Esta opção procura quaisquer parâmetros no script da Colmeia que exijam valores em tempo de execução.

    1. Na caixa de texto **de valor,** adicione a pasta existente no formato `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` . O caminho é sensível a maiúsculas e minúsculas. Este caminho é onde a saída do script será armazenada. O `wasbs` esquema é necessário porque as contas de armazenamento têm agora uma transferência segura necessária ativada por padrão.

        ![Fornecer parâmetros para o script da Colmeia](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Fornecer parâmetros para o script da Colmeia")

1. **Selecione Validate** para validar o pipeline. Selecione o **>>** botão (seta direita) para fechar a janela de validação.

    ![Validar o gasoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Validar o gasoduto Azure Data Factory")

1. Por fim, **selecione Publicar Tudo** para publicar os artefactos na Azure Data Factory.

    ![Publicar o pipeline da Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publicar o pipeline da Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Acionar um pipeline

1. A partir da barra de ferramentas na superfície do designer, **selecione Adicione o gatilho**  >  **Agora** .

    ![Desencadear o oleoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Desencadear o oleoduto Azure Data Factory")

2. Selecione **OK** na barra lateral pop-up.

## <a name="monitor-a-pipeline"></a>Monitorizar um pipeline

1. Mude para o separador **Monitorizar** , no lado esquerdo. Verá uma execução de pipeline na lista **Execuções de Pipeline** . Note o estado da execução sob a coluna **Status.**

    ![Monitorize o oleoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorize o oleoduto Azure Data Factory")

1. Selecione **Atualizar** para atualizar o estado.

1. Também pode selecionar o ícone **'Ver Atividade Corre'** para ver a atividade executada associada ao pipeline. Na imagem abaixo, vê-se apenas uma atividade a decorrer, uma vez que só há uma atividade no oleoduto que criou. Para voltar à vista anterior, selecione **Pipelines** para o topo da página.

    ![Monitorize a atividade do gasoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorize a atividade do gasoduto Azure Data Factory")

## <a name="verify-the-output"></a>Verificar a saída

1. Para verificar a saída, no portal Azure navegue para a conta de armazenamento que utilizou para este tutorial. Deve ver as seguintes pastas ou recipientes:

    * Você vê **um adfgerstared/outputfolder** que contém a saída do script hive que foi executado como parte do pipeline.

    * Vê-se um contentor **\<linked-service-name> - \<timestamp> adfhdidatafactory.** Este recipiente é o local de armazenamento padrão do cluster HDInsight que foi criado como parte da execução do gasoduto.

    * Você vê um contentor **adfjobs** que tem os registos de trabalho da Azure Data Factory.  

        ![Verifique a saída do gasoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Verifique a saída do gasoduto Azure Data Factory")

## <a name="clean-up-resources"></a>Limpar os recursos

Com a criação de cluster HDInsight a pedido, não é necessário eliminar explicitamente o cluster HDInsight. O cluster é eliminado com base na configuração fornecida durante a criação do pipeline. Mesmo depois de o cluster ser eliminado, as contas de armazenamento associadas ao cluster continuam a existir. Este comportamento é por design para que possa manter os seus dados intactos. No entanto, se não quiser persistir os dados, poderá apagar a conta de armazenamento que criou.

Ou pode eliminar todo o grupo de recursos que criou para este tutorial. Este processo elimina a conta de armazenamento e a Fábrica de Dados Azure que criou.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos de recursos** no painel esquerdo.
1. Selecione o nome do grupo de recursos que criou no seu script PowerShell. Utilize o filtro se tiver demasiados grupos de recursos listados. Abre o grupo de recursos.
1. No azulejo **recursos,** terá a conta de armazenamento por defeito e a fábrica de dados listada, a menos que partilhe o grupo de recursos com outros projetos.
1. Selecione **Eliminar grupo de recursos** . Ao fazê-lo, elimina a conta de armazenamento e os dados armazenados na conta de armazenamento.

    !['Portal Azure elimina grupo de recursos'](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Eliminar grupo de recursos")

1. Introduza o nome do grupo de recursos para confirmar a eliminação e, em seguida, **selecione Delete** .

## <a name="next-steps"></a>Próximas etapas

Neste artigo, aprendeu a usar a Azure Data Factory para criar um cluster HDInsight a pedido e executar empregos apache Hive. Avance para o próximo artigo para aprender a criar clusters HDInsight com configuração personalizada.

> [!div class="nextstepaction"]
> [Criar clusters Azure HDInsight com configuração personalizada](hdinsight-hadoop-provision-linux-clusters.md)