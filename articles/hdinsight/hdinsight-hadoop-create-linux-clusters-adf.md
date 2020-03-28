---
title: 'Tutorial: Clusters a pedido em Azure HDInsight com Fábrica de Dados'
description: Tutorial - Aprenda a criar clusters Apache Hadoop a pedido no HDInsight utilizando a Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/18/2020
ms.openlocfilehash: b184a42c52384440445181ac44c616c3139e064f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130697"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Tutorial: Criar clusters Apache Hadoop a pedido no HDInsight utilizando a Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste tutorial, aprende-se a criar um cluster [Apache Hadoop,](./hadoop/apache-hadoop-introduction.md) a pedido, no Azure HDInsight utilizando a Azure Data Factory. Em seguida, utiliza gasodutos de dados na Azure Data Factory para executar trabalhos da Hive e eliminar o cluster. No final deste tutorial, aprende-se a operacionalizar um grande trabalho de dados onde a criação de clusters, o funcionamento do trabalho e a eliminação do cluster são realizados num horário.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure
> * Compreender a atividade da Fábrica de Dados Azure
> * Criar uma fábrica de dados usando o portal Azure
> * Criar serviços ligados
> * Criar um pipeline
> * Acionar um pipeline
> * Monitorizar um pipeline
> * Verificar a saída

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* O Módulo PowerShell [Az](https://docs.microsoft.com/powershell/azure/overview) instalado.

* Um diretor de serviço de Diretório Ativo Azure. Depois de ter criado o diretor de serviço, certifique-se de recuperar o ID da **aplicação** e a chave de **autenticação** utilizando as instruções do artigo ligado. Precisa destes valores mais tarde neste tutorial. Além disso, certifique-se de que o principal de serviço é membro do papel de *Contribuinte* da subscrição ou do grupo de recursos em que o cluster é criado. Para obter instruções para recuperar os valores necessários e atribuir as funções certas, consulte [Criar um diretor de serviço Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Criar objetos Azure preliminares

Nesta secção, cria vários objetos que serão utilizados para o cluster HDInsight que cria a pedido. A conta de armazenamento criada conterá a `partitionweblogs.hql`amostra do script [HiveQL,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) que utiliza para simular uma amostra de trabalho da Apache Hive que funciona no cluster.

Esta secção utiliza um script Azure PowerShell para criar a conta de armazenamento e copiar sobre os ficheiros necessários dentro da conta de armazenamento. O script de amostra Azure PowerShell nesta secção executa as seguintes tarefas:

1. Entra no Azure.
2. Cria um grupo de recursos Azure.
3. Cria uma Conta de armazenamento do Azure.
4. Cria um recipiente Blob na conta de armazenamento
5. Copia o script da amostra HiveQL **(partitionweblogs.hql**) o recipiente Blob. O script está [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)disponível em . O script da amostra já está disponível em outro recipiente público blob. O script PowerShell abaixo faz uma cópia destes ficheiros na conta De armazenamento Azure que cria.

### <a name="create-storage-account-and-copy-files"></a>Criar contas de armazenamento e copiar ficheiros

> [!IMPORTANT]  
> Especifique os nomes para o grupo de recursos Azure e a conta de armazenamento Azure que será criada pelo script.
> Escreva o nome do grupo de **recursos,** **o nome da conta**de armazenamento e a chave da conta de **armazenamento** saída do script. Precisa deles na próxima secção.

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

### <a name="verify-storage-account"></a>Verificar a conta de armazenamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Da esquerda, navegue para **todos os serviços grupos** > de**Recursos Gerais.****General** > 
1. Selecione o nome do grupo de recursos que criou no seu script PowerShell. Utilize o filtro se tiver demasiados grupos de recursos listados.
1. Do ponto de vista **geral,** vê-se um recurso listado a menos que partilhe o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome que especificou anteriormente. Selecione o nome da conta de armazenamento.
1. Selecione o azulejo dos **recipientes.**
1. Selecione o recipiente **adfgetstarted.** Vê-se uma pasta chamada **hivescripts.**
1. Abra a pasta e certifique-se de que contém o ficheiro de script da amostra, **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Compreender a atividade da Azure Data Factory

A [Azure Data Factory](../data-factory/introduction.md) orquestra e automatiza o movimento e transformação de dados. A Azure Data Factory pode criar um cluster Hadoop HDInsight just-in-time para processar uma fatia de dados de entrada e eliminar o cluster quando o processamento estiver concluído.

Na Azure Data Factory, uma fábrica de dados pode ter um ou mais pipelines de dados. Um pipeline de dados tem uma ou mais atividades. Existem dois tipos de atividades:

* [Atividades](../data-factory/copy-activity-overview.md)de Movimento de Dados . Utiliza atividades de movimento de dados para mover dados de uma loja de dados de origem para uma loja de dados de destino.
* [Atividades de transformação de dados.](../data-factory/transform-data.md) Utiliza atividades de transformação de dados para transformar/processar dados. A Atividade da Hive HDInsight é uma das atividades de transformação suportadas pela Data Factory. Você usa a atividade de transformação da Colmeia neste tutorial.

Neste artigo, configura a atividade da Colmeia para criar um cluster Hadoop HDInsight a pedido. Quando a atividade corre para processar dados, eis o que acontece:

1. Um cluster Hadoop HDInsight é automaticamente criado para você apenas no tempo para processar a fatia.

2. Os dados de entrada são processados executando um script HiveQL no cluster. Neste tutorial, o script HiveQL associado à atividade da colmeia executa as seguintes ações:

    * Utiliza a tabela existente *(colmeia)* para criar outra tabela **HiveSampleOut**.
    * Povoa a tabela **HiveSampleOut** com apenas colunas específicas da *colmeia*original.

3. O cluster Hadoop HDInsight é eliminado após o processamento estar concluído e o cluster está inativo durante a quantidade de tempo configurada (definição timeToLive). Se a próxima fatia de dados estiver disponível para processamento com este tempo de inatividade ToLive, o mesmo cluster é usado para processar a fatia.  

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Do menu esquerdo, navegue para **+ Criar um recurso** > **Analytics** > **Data Factory**.

    ![Fábrica de Dados Azure no portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Fábrica de Dados Azure no portal")

3. Insira ou selecione os seguintes valores para o azulejo da nova fábrica de **dados:**

    |Propriedade  |Valor  |
    |---------|---------|
    |Nome | Insira um nome para a fábrica de dados. Este nome tem de ser globalmente exclusivo.|
    |Versão | Saia na **V2.** |
    |Subscrição | Selecione a sua subscrição do Azure. |
    |Grupo de recursos | Selecione o grupo de recursos que criou utilizando o script PowerShell. |
    |Localização | A localização é automaticamente definida para o local que especificou ao criar o grupo de recursos mais cedo. Para este tutorial, a localização está definida para **os EUA Orientais.** |
    |Ativar GIT|Desmarcada esta caixa.|

    ![Criar fábrica de dados Azure utilizando portal Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Criar fábrica de dados Azure utilizando portal Azure")

4. Selecione **Criar**. A criação de uma fábrica de dados pode demorar entre 2 a 4 minutos.

5. Assim que a fábrica de dados for criada, receberá uma notificação **de Implementação bem sucedida** com um botão De recurso **Go.**  Selecione **Recorrer** para abrir a vista por defeito da Fábrica de Dados.

6. Selecione **Author & Monitor** para lançar o portal de autoria e monitorização da Azure Data Factory.

    ![Visão geral do portal Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Visão geral da Fábrica de Dados Azure")

## <a name="create-linked-services"></a>Criar serviços ligados

Nesta secção, é autor de dois serviços ligados dentro da sua fábrica de dados.

* Um **serviço ligado do Armazenamento do Azure** que liga uma conta de armazenamento do Azure à fábrica de dados. Este armazenamento é utilizado pelo cluster do HDInsight a pedido. Também contém o script da Colmeia que é executado no cluster.
* Um **serviço ligado hDInsight**a pedido. A Azure Data Factory cria automaticamente um cluster HDInsight e executa o script Hive. Em seguida, elimina o cluster do HDInsight depois de o cluster estar inativo durante um período de tempo pré-configurado.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. A partir do painel esquerdo da página **Let's start,** selecione o ícone **Autor.**

    ![Criar um serviço ligado à Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Criar um serviço ligado à Azure Data Factory")

2. Selecione **Ligações** do canto inferior esquerdo da janela e, em seguida, **selecione +New**.

    ![Criar ligações na Fábrica de Dados Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Criar ligações na Fábrica de Dados Azure")

3. Na caixa de diálogo **New Linked Service,** selecione **Armazenamento De Blob Azure** e, em seguida, selecione **Continue**.

    ![Criar serviço ligado ao armazenamento Azure para data factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Criar serviço ligado ao armazenamento Azure para data factory")

4. Fornecer os seguintes valores para o serviço ligado ao armazenamento:

    |Propriedade |Valor |
    |---|---|
    |Nome |Introduza `HDIStorageLinkedService`.|
    |Subscrição do Azure |Selecione a sua subscrição na lista de lançamentos.|
    |Nome da conta de armazenamento |Selecione a conta De armazenamento Azure que criou como parte do script PowerShell.|

    Selecione **a ligação de teste** e, se tiver sucesso, selecione **Criar**.

    ![Fornecer nome para serviço ligado ao armazenamento Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Fornecer nome para serviço ligado ao armazenamento Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Crie um serviço ligado do HDInsight a pedido

1. Selecione o botão **+ Novo** novamente para criar outro serviço ligado.

2. Na janela **New Linked Service,** selecione o separador **Compute.**

3. Selecione **Azure HDInsight**, e, em seguida, **selecione Continuar**.

    ![Criar serviço ligado ao HDInsight para a Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Criar serviço ligado ao HDInsight para a Azure Data Factory")

4. Na janela **New Linked Service,** introduza os seguintes valores e deixe o resto como padrão:

    | Propriedade | Valor |
    | --- | --- |
    | Nome | Introduza `HDInsightLinkedService`.|
    | Tipo | Selecione **HDInsight on-demand**. |
    | Serviço Ligado do Storage do Azure | Selecione `HDIStorageLinkedService`. |
    | Tipo de cluster | Selecione **hadoop** |
    | Time to live | Forneça a duração para a qual pretende que o cluster HDInsight esteja disponível antes de ser automaticamente eliminado.|
    | ID principal de serviço | Forneça o ID de aplicação do diretor de serviço de Diretório Ativo Azure que criou como parte dos pré-requisitos. |
    | Chave principal do serviço | Forneça a chave de autenticação para o diretor de serviço do Azure Ative Diretório. |
    | Prefixo de nome do cluster | Forneça um valor que será pré-fixado a todos os tipos de cluster que são criados pela fábrica de dados. |
    |Subscrição |Selecione a sua subscrição na lista de lançamentos.|
    | Selecionar grupo de recursos | Selecione o grupo de recursos que criou como parte do script PowerShell que utilizou anteriormente.|
    | Nome de utilizador do tipo OS/Cluster SSH | Introduza um nome de utilizador `sshuser`SSH, normalmente . |
    | Tipo OS/Palavra-passe SSH cluster | Fornecer uma palavra-passe para o utilizador SSH |
    | Nome de utilizador do tipo OS/Cluster | Introduza um nome de `admin`utilizador de cluster, geralmente . |
    | Tipo OS/Palavra-passe do cluster | Forneça uma senha para o utilizador do cluster. |

    Em seguida, selecione **Criar**.

    ![Fornecer valores para o serviço ligado ao HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Fornecer valores para o serviço ligado ao HDInsight")

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o **+** botão (mais) e, em seguida, selecione **Pipeline**.

    ![Criar um oleoduto na Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Criar um oleoduto na Azure Data Factory")

1. Na caixa de **ferramentas De Atividades,** expanda o **HDInsight**e arraste a atividade da **Colmeia** para a superfície do designer de gasodutos. No separador **Geral,** forneça um nome para a atividade.

    ![Adicionar atividades ao pipeline data factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Adicionar atividades ao pipeline data factory")

1. Certifique-se de que tem a atividade da Hive selecionada, selecione o separador **Cluster HDI** e a partir da lista de drop-down do **HDInsight Linked Service,** selecione o serviço linked que criou anteriormente, **HDInsightLinkedService,** para HDInsight.

    ![Fornecer detalhes do cluster HDInsight para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Fornecer detalhes do cluster HDInsight para o pipeline")

1. Selecione o separador **Script** e complete os seguintes passos:

    1. Para **o Serviço Ligado ao Script,** selecione **HDIStorageLinkedService** da lista de lançamentos. Este valor é o serviço ligado ao armazenamento que criou anteriormente.

    1. Para **o Caminho de Arquivo,** selecione **'Armazenamento de navegação'** e navegue até ao local onde está disponível o script da Colmeia. Se executou o script PowerShell mais `adfgetstarted/hivescripts/partitionweblogs.hql`cedo, este local deve ser .

        ![Forneça detalhes do script da Hive para o oleoduto](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Forneça detalhes do script da Hive para o oleoduto")

    1. Em**parâmetros** **avançados,** > selecione **preenchimento automático do script**. Esta opção procura quaisquer parâmetros no script da Colmeia que requeiram valores em tempo de execução.

    1. Na caixa de texto de **valor,** adicione `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`a pasta existente no formato . O caminho é sensível a maiúsculas e minúsculas. Este é o caminho onde a saída do script será armazenada. O `wasbs` esquema é necessário porque as contas de armazenamento têm agora uma transferência segura necessária por defeito.

        ![Fornecer parâmetros para o script da Colmeia](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Fornecer parâmetros para o script da Colmeia")

1. Selecione **Validar** para validar o gasoduto. Selecione o **>>** botão (seta direita) para fechar a janela de validação.

    ![Validar o oleoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Validar o oleoduto Azure Data Factory")

1. Por fim, selecione **Publicar Todos** para publicar os artefactos na Azure Data Factory.

    ![Publicar o oleoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Publicar o oleoduto Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Acionar um pipeline

1. A partir da barra de ferramentas na superfície do designer, selecione **Adicionar gatilho** > **agora**.

    ![Desencadear o oleoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Desencadear o oleoduto Azure Data Factory")

2. Selecione **OK** na barra lateral pop-up.

## <a name="monitor-a-pipeline"></a>Monitorizar um pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline na lista **Execuções de Pipeline**. Note o estado da execução sob a coluna **Status.**

    ![Monitorize o oleoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorize o oleoduto Azure Data Factory")

1. Selecione **Atualizar** para atualizar o estado.

1. Também pode selecionar o ícone **'Ver Atividade corre'** para ver a atividade executada associada ao pipeline. Na imagem abaixo, vê-se apenas uma atividade executada, uma vez que só há uma atividade no oleoduto que criou. Para voltar à vista anterior, selecione **Pipelines** para o topo da página.

    ![Monitorizar a atividade do gasoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorizar a atividade do gasoduto Azure Data Factory")

## <a name="verify-the-output"></a>Verificar a saída

1. Para verificar a saída, no portal Azure navegue para a conta de armazenamento que utilizou para este tutorial. Deve ver as seguintes pastas ou contentores:

    * Você vê uma **adfgerstart/pasta** de saída que contém a saída do script da Colmeia que foi executado como parte do pipeline.

    * Você vê um **adfhdidatafactory-\<de nome\<de serviço>-timestamp>** recipiente. Este recipiente é a localização padrão de armazenamento do cluster HDInsight que foi criado como parte da execução do gasoduto.

    * Você vê um recipiente de **adfjobs** que tem os registos de trabalho da Azure Data Factory.  

        ![Verifique a saída do gasoduto Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Verifique a saída do gasoduto Azure Data Factory")

## <a name="clean-up-resources"></a>Limpar recursos

Com a criação de cluster HDInsight a pedido, não precisa de eliminar explicitamente o cluster HDInsight. O cluster é eliminado com base na configuração fornecida durante a criação do gasoduto. No entanto, mesmo após a eliminação do cluster, as contas de armazenamento associadas ao cluster continuam a existir. Este comportamento é por design para que possa manter os seus dados intactos. No entanto, se não quiser persistir os dados, poderá eliminar a conta de armazenamento que criou.

Em alternativa, pode eliminar todo o grupo de recursos que criou para este tutorial. Isto elimina a conta de armazenamento e a Fábrica de Dados Azure que criou.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Grupos de Recursos** no painel esquerdo.
1. Selecione o nome do grupo de recursos que criou no seu script PowerShell. Utilize o filtro se tiver demasiados grupos de recursos listados. Abre o grupo de recursos.
1. No azulejo **Recursos,** você terá a conta de armazenamento padrão e a fábrica de dados listada, a menos que partilhe o grupo de recursos com outros projetos.
1. Selecione **Eliminar grupo de recursos**. Ao fazê-lo, elimina a conta de armazenamento e os dados armazenados na conta de armazenamento.

    ![Portal Azure apaga grupo de recursos](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Eliminar grupo de recursos")

1. Introduza o nome do grupo de recursos para confirmar a eliminação e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a Azure Data Factory para criar um cluster HDInsight a pedido e executar empregos [apache hive.](https://hive.apache.org/) Avance para o próximo artigo para aprender a criar clusters HDInsight com configuração personalizada.

> [!div class="nextstepaction"]
> [Crie clusters Azure HDInsight com configuração personalizada](hdinsight-hadoop-provision-linux-clusters.md)
