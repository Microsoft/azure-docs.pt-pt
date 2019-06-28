---
title: 'Tutorial: Criar clusters do Apache Hadoop a pedido no HDInsight do Azure com o Data Factory '
description: Tutorial – Saiba como criar clusters do Apache Hadoop a pedido no HDInsight com o Azure Data Factory.
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 04/18/2019
ms.openlocfilehash: e9773c2e8f6f8de3a44e45989aa577a5d8c2dcee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433832"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Tutorial: Criar clusters do Apache Hadoop a pedido no HDInsight com o Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste tutorial, irá aprender a criar uma [Apache Hadoop](https://hadoop.apache.org/) cluster, a pedido, no HDInsight do Azure com o Azure Data Factory. Utilize, em seguida, pipelines de dados no Azure Data Factory para executar tarefas do Hive e eliminar o cluster. No final deste tutorial, irá aprender a operacionalizar um trabalho de grandes volumes de dados, executar em que a criação do cluster, a execução de tarefa e a exclusão de cluster são executadas com base numa agenda.

Este tutorial abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Criar uma conta do Storage do Azure
> * Compreender a atividade do Azure Data Factory
> * Criar uma fábrica de dados com o portal do Azure
> * Criar serviços ligados
> * Criar um pipeline
> * Acionar um pipeline
> * Monitorizar um pipeline
> * Verificar a saída

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* O PowerShell [módulo Az](https://docs.microsoft.com/powershell/azure/overview) instalado.

* Um principal de serviço do Azure Active Directory. Depois de criar o principal de serviço, certifique-se obter o **ID da aplicação** e **chave de autenticação** com as instruções no artigo ligado. Vai precisar destes valores mais tarde neste tutorial. Além disso, certifique-se de que o principal de serviço é um membro do *contribuinte* função da subscrição ou o grupo de recursos no qual o cluster é criado. Para obter instruções obter os valores necessários e atribua as funções certas, consulte [criar um Azure Active Directory, principal de serviço](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Criar objetos do Azure preliminares

Nesta secção, vai criar vários objetos que serão utilizados para o cluster do HDInsight que cria a pedido. A conta de armazenamento criada irá conter o exemplo [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) script (`partitionweblogs.hql`) que usar para simular uma amostra [Apache Hive](https://hive.apache.org/) tarefa que é executada no cluster.

Esta secção utiliza um script do Azure PowerShell para criar a conta de armazenamento e copie os ficheiros necessários na conta de armazenamento. O script de exemplo do Azure PowerShell nesta secção, executa as seguintes tarefas:

1. Iniciar a sessão no Azure.
2. Cria um grupo de recursos do Azure.
3. Cria uma Conta de armazenamento do Azure.
4. Cria um contentor de BLOBs na conta de armazenamento
5. Copia o exemplo de script de HiveQL (**partitionweblogs. hql**) o contentor de Blobs. O script está disponível em [ https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). O script de exemplo já está disponível em outro contentor de BLOBs público. O script do PowerShell abaixo efetua uma cópia desses arquivos para a conta de armazenamento do Azure cria.

> [!WARNING]  
> Tipo de conta de armazenamento `BlobStorage` não pode ser utilizado para clusters do HDInsight.

**Para criar uma conta de armazenamento e copie os ficheiros com o Azure PowerShell:**

> [!IMPORTANT]  
> Especifique nomes para o grupo de recursos do Azure e a conta de armazenamento do Azure que será criada pelo script.
> Anote **nome do grupo de recursos**, **nome da conta de armazenamento**, e **chave da conta de armazenamento** produzido pelo script. Precisa na próxima seção.

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

**Para verificar a criação de conta de armazenamento**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos** no painel da esquerda.
3. Selecione o nome do grupo de recursos que criou no script do PowerShell. Se tiver muitos grupos de recursos listados, utilize o filtro.
4. Sobre o **recursos** mosaico, verá um recurso listado, a menos que partilha o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome que especificou anteriormente. Selecione o nome de conta de armazenamento.
5. Selecione o **Blobs** mosaicos.
6. Selecione o **adfgetstarted** contentor. Verá uma pasta denominada **hivescripts**.
7. Abra a pasta e certifique-se de que contém o ficheiro de script de exemplo **partitionweblogs. hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Compreender a atividade do Azure Data Factory

[O Azure Data Factory](../data-factory/introduction.md) orquestra e automatiza o movimento e transformação de dados. O Azure Data Factory, pode criar um Hadoop do HDInsight cluster just-in-time para processar um setor de dados de entrada e eliminar o cluster quando o processamento é concluído. 

No Azure Data Factory, uma fábrica de dados pode ter um ou mais pipelines de dados. Um pipeline de dados tem uma ou mais atividades. Existem dois tipos de atividades:

- [Atividades de movimento de dados](../data-factory/copy-activity-overview.md) -utilizar atividades de movimento de dados para mover dados de um arquivo de dados de origem para um arquivo de dados de destino.
- [Atividades de transformação de dados](../data-factory/transform-data.md). Utilize atividades de transformação de dados para transformar/processar dados. Atividade do Hive do HDInsight é uma das atividades de transformação Data Factory suportadas. Utilize a atividade de transformação do ramo de registo neste tutorial.

Neste artigo, vai configurar a atividade de ramo de registo para criar um cluster de Hadoop do HDInsight a pedido. Quando a atividade é executada para processar dados, eis o que acontece:

1. Um cluster de Hadoop do HDInsight é criado automaticamente para just-in-time processar o setor. 

2. Os dados de entrada são processados ao executar um script de HiveQL no cluster. Neste tutorial, o script de HiveQL associado com a atividade do hive executa as seguintes ações:

    - Utiliza a tabela existente (*hivesampletable*) para criar outra tabela **HiveSampleOut**.
    - Preenche o **HiveSampleOut** tabela com apenas as colunas específicas em relação ao original *hivesampletable*.
    
3. O cluster de Hadoop do HDInsight é eliminado após o processamento é concluído e o cluster estar inativo durante o período de tempo (definição de timeToLive) configurado. Se o setor de dados seguinte está disponível para processamento com no tempo de inatividade timeToLive, do mesmo cluster é utilizado para processar o setor.  

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda, navegue até **+ criar um recurso** > **análise** > **Data Factory**.

    ![O Azure Data Factory no portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "no portal do Azure Data Factory")

3. Introduza ou selecione os seguintes valores para o **nova fábrica de dados** mosaico:

    |Propriedade  |Value  |
    |---------|---------|
    |Name | Introduza um nome para a fábrica de dados. Este nome tem de ser globalmente exclusivo.|
    |Subscrição | Selecione a sua subscrição do Azure. |
    |Grupo de recursos | Selecione **utilizar existente** e, em seguida, selecione o grupo de recursos que criou com o script do PowerShell. |
    |Version | Deixe **V2**. |
    |Location | A localização é automaticamente definida para a localização que especificou ao criar o grupo de recursos anteriormente. Para este tutorial, a localização está definida como **E.U.A. Leste**. |

    ![Criar fábrica de dados do Azure através do portal do Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "criar do Azure Data Factory com o portal do Azure")

4. Selecione **Criar**. Criar uma fábrica de dados pode demorar entre 2 a 4 minutos.

5. Depois de criar a fábrica de dados, receberá um **implementação concluída com êxito** notificação com uma **Ir para recurso** botão.  Selecione **Ir para recurso** para abrir a vista predefinida do Data Factory.

6. Selecione **criar e monitorizar** para iniciar a fábrica de dados do Azure, criação e monitorização do portal.

    ![Descrição geral do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "descrição geral da fábrica de dados do Azure")

## <a name="create-linked-services"></a>Criar serviços ligados

Nesta secção, vai criar dois serviços ligados na sua fábrica de dados.

- Um **serviço ligado do Armazenamento do Azure** que liga uma conta de armazenamento do Azure à fábrica de dados. Este armazenamento é utilizado pelo cluster do HDInsight a pedido. Também contém o script de ramo de registo que é executado no cluster.
- Um **serviço ligado do HDInsight a pedido**. O Azure Data Factory cria um cluster do HDInsight e executa o script de ramo de registo automaticamente. Em seguida, elimina o cluster do HDInsight depois de o cluster estar inativo durante um período de tempo pré-configurado.

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure

1. No painel à esquerda do **Vamos começar** página, selecione a **autor** ícone.

    ![Criar um serviço ligado do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "criar um serviço ligado do Azure Data Factory")

2. Selecione **conexões** no canto inferior esquerdo da janela e em seguida, selecione **+ novo**.

    ![Criar ligações no Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "criar ligações no Azure Data Factory")

3. Na **novo serviço ligado** caixa de diálogo, selecione **armazenamento de Blobs do Azure** e, em seguida, selecione **continuar**.

    ![Serviço do Data Factory ligado do armazenamento do Azure de criar](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "serviço fábrica de dados ligado do armazenamento do Azure de criar")

4. Forneça os valores seguintes para o serviço ligado do armazenamento:

    |Propriedade |Value |
    |---|---|
    |Name |Introduza `HDIStorageLinkedService`.|
    |Subscrição do Azure |Selecione a sua subscrição na lista pendente.|
    |Nome da conta de armazenamento |Selecione a conta de armazenamento do Azure que criou como parte do script do PowerShell.|

    Em seguida, selecione **Concluir**.

    ![Serviço ligado de fornecer o nome do armazenamento do Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "serviço ligado de fornecer o nome do armazenamento do Azure")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Crie um serviço ligado do HDInsight a pedido

1. Selecione o botão **+ Novo** novamente para criar outro serviço ligado.

2. Na **novo serviço ligado** janela, selecione a **computação** separador.

3. Selecione **do Azure HDInsight**e, em seguida, selecione **continuar**.

    ![HDInsight criar serviço ligado para Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight criar serviço ligado para Azure Data Factory")

4. Na **novo serviço ligado** janela, introduza os seguintes valores e deixe o resto como predefinição:

    | Propriedade | Value |
    | --- | --- |
    | Name | Introduza `HDInsightLinkedService`.|
    | Tipo | Selecione **HDInsight a pedido**. |
    | Serviço Ligado do Storage do Azure | Selecione `HDIStorageLinkedService`. |
    | Tipo de cluster | Selecione **hadoop** |
    | Time to live | Forneça a duração para o qual pretende que o cluster do HDInsight para estar disponível antes de ser eliminado automaticamente.|
    | ID de principal de serviço | Forneça o ID de aplicação do principal de serviço do Azure Active Directory que criou como parte dos pré-requisitos. |
    | Chave de principal de serviço | Forneça a chave de autenticação para o principal de serviço do Azure Active Directory. |
    | Prefixo do nome do cluster | Forneça um valor que terá o prefixo para todos os tipos de cluster que são criados pela fábrica de dados. |
    |Subscrição |Selecione a sua subscrição na lista pendente.|
    | Selecionar grupo de recursos | Selecione o grupo de recursos que criou como parte do script do PowerShell que utilizou anteriormente.|
    |Selecione a região | Selecione uma região na lista pendente.|
    | Nome de utilizador SSH do SO/tipo de Cluster | Introduza um nome de utilizador do SSH, normalmente `sshuser`. |
    | Palavra-passe SSH do SO/tipo de Cluster | Forneça uma palavra-passe do utilizador SSH |
    | Nome de utilizador do Cluster do tipo de SO | Introduza um nome de utilizador do cluster, normalmente `admin`. |
    | Palavra-passe de utilizador do SO/tipo de Cluster | Forneça uma palavra-passe para o utilizador do cluster. |

    Em seguida, selecione **Concluir**.

    ![Serviço ligado de fornecer valores para o HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "serviço ligado de fornecer valores para o HDInsight")

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione o botão **+** (mais) e, em seguida, selecione **Pipeline**.

    ![Criar um pipeline no Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "criar um pipeline na fábrica de dados do Azure")

2. Na **atividades** caixa de ferramentas, expanda **HDInsight**e arraste o **Hive** atividade para a superfície de desenho do pipeline. Na **gerais** separador, indique um nome para a atividade.

    ![Adicionar atividades ao pipeline do Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "adicionar atividades ao pipeline do Data Factory")

3. Certifique-se de que tem a atividade de ramo de registo selecionada, selecione o **Cluster do HDI** separador e para o **serviço ligado do HDInsight** na lista pendente, selecione associada de serviço que criou anteriormente,  **HDinightLinkedService**, para o HDInsight.

    ![Forneça os detalhes de cluster do HDInsight para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "HDInsight fornecem detalhes do cluster para o pipeline")

4. Selecione o **Script** separador e conclua os seguintes passos:

    1. Para **serviço ligado de Script**, selecione **HDIStorageLinkedService** na lista pendente. Este valor é o serviço ligado de armazenamento que criou anteriormente.

    1. Para **caminho do ficheiro**, selecione **procurar no armazenamento** e navegue para a localização onde o script do Hive de exemplo está disponível. Se tiver executado o script do PowerShell anteriormente, nesta localização deve ser `adfgetstarted/hivescripts/partitionweblogs.hql`.

        ![Fornecer detalhes de script de ramo de registo para o pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Hive fornecer detalhes de script para o pipeline")

    1. Sob **avançadas** > **parâmetros**, selecione **preencher automaticamente a partir do script**. Esta opção procura todos os parâmetros do script de ramo de registo que requerem valores em tempo de execução. O script que utilizar (**partitionweblogs. hql**) tem um **saída** parâmetro. Forneça o **valor** no formato `wasb://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontar para uma pasta existente no seu armazenamento do Azure. O caminho é sensível a maiúsculas e minúsculas. Este é o caminho onde a saída do script será armazenada.
    
        ![Fornecer os parâmetros para o script do Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "fornecer os parâmetros para o script do Hive")

1. Selecione **Validate** para validar o pipeline. Selecione o botão **>>** (seta para a direita) para fechar a janela de validação.

    ![Validar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "validar o pipeline da fábrica de dados do Azure")

1. Por fim, selecione **publicar tudo** para publicar os artefactos no Azure Data Factory.

    ![Publicar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "publicar o pipeline da fábrica de dados do Azure")

## <a name="trigger-a-pipeline"></a>Acionar um pipeline

1. Na barra de ferramentas na superfície de design, selecione **adicionar acionador** > **acionar agora**.

    ![Acionar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "acionar o pipeline da fábrica de dados do Azure")

2. Selecione **concluir** na barra do lado de pop-up.

## <a name="monitor-a-pipeline"></a>Monitorizar um pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline na lista **Execuções de Pipeline**. Tenha em atenção o estado da execução sob o **estado** coluna.

    ![Monitorizar o pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "monitorizar o pipeline da fábrica de dados do Azure")

1. Selecione **Atualizar** para atualizar o estado.

1. Também pode selecionar o **ver execuções de atividades** ícone para ver a execução de atividade associadas do pipeline. A captura de ecrã abaixo, verá apenas uma atividade executar uma vez que existe apenas uma atividade no pipeline que criou. Para regressar à vista anterior, selecione **Pipelines** na parte superior da página.

    ![Monitorizar a atividade do pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "monitorizar a atividade do pipeline de fábrica de dados do Azure")

## <a name="verify-the-output"></a>Verificar a saída

1. Para verificar a saída, no portal do Azure navegue para a conta de armazenamento que utilizou para este tutorial. Deverá ver as pastas ou os contentores seguintes:

    - Verá uma **adfgerstarted/outputfolder** que contém a saída do script do Hive que foi executado como parte do pipeline.

    - Verá uma **adfhdidatafactory -\<ligado-service-name >-\<timestamp >** contentor. Este contentor é a localização de armazenamento predefinida do cluster HDInsight que foi criada como parte da execução de pipeline.

    - Verá uma **adfjobs** registos de contentor que tem a tarefa do Azure Data Factory.  

        ![Verificar a saída do pipeline do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "verificar a saída do pipeline de fábrica de dados do Azure")

## <a name="clean-up-resources"></a>Limpar recursos

Com a criação de cluster do HDInsight a pedido, não é necessário eliminar explicitamente o cluster de HDInsight. O cluster é eliminado com base na configuração que forneceu ao criar o pipeline. No entanto, mesmo depois do cluster é eliminado, as contas de armazenamento associadas ao cluster continuam a existir. Este comportamento é predefinido para que possa manter seus dados intactos. No entanto, se não desejar persistir os dados, pode eliminar a conta de armazenamento que criou.

Em alternativa, pode eliminar o grupo de recursos inteiro que criou para este tutorial. Esta ação elimina a conta de armazenamento e a fábrica de dados do Azure que criou.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos de recursos** no painel da esquerda.
1. Selecione o nome do grupo de recursos que criou no script do PowerShell. Se tiver muitos grupos de recursos listados, utilize o filtro. Abre o grupo de recursos.
1. Sobre o **recursos** mosaico, deverá ter a conta de armazenamento predefinida e a fábrica de dados listado, a menos que partilha o grupo de recursos com outros projetos.
1. Selecione **Eliminar grupo de recursos**. Se o fizer, elimina a conta de armazenamento e os dados armazenados na conta de armazenamento.

    ![Eliminar grupo de recursos](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "eliminar grupo de recursos")

1. Introduza o nome do grupo de recursos para confirmar a eliminação e, em seguida, selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar o Azure Data Factory para criar o cluster do HDInsight e executar a pedido [Apache Hive](https://hive.apache.org/) tarefas. Avance para o artigo seguinte para saber como criar clusters do HDInsight com configuração personalizada.

> [!div class="nextstepaction"]
>[Criar clusters do HDInsight do Azure com configuração personalizada](hdinsight-hadoop-provision-linux-clusters.md)