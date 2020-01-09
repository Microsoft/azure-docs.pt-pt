---
title: 'Tutorial: usar runbooks de automação do Azure para criar clusters-Azure HDInsight'
description: Saiba como criar e excluir clusters do Azure HDInsight com scripts que são executados na nuvem usando runbooks de automação do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553514"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Tutorial: Criar clusters do Azure HDInsight com a automação do Azure

A automação do Azure permite criar scripts que são executados na nuvem e gerenciar recursos do Azure sob demanda ou com base em uma agenda. Este artigo descreve como criar runbooks do PowerShell para criar e excluir clusters do Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instale os módulos necessários para interagir com o HDInsight.
> * Crie e armazene as credenciais necessárias durante a criação do cluster.
> * Crie um novo runbook de automação do Azure para criar um cluster HDInsight.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de automação do Azure](../automation/automation-quickstart-create-account.md)existente.
* Uma [conta de armazenamento do Azure](../storage/common/storage-account-create.md)existente, que será usada como armazenamento de cluster.

## <a name="install-hdinsight-modules"></a>Instalar módulos do HDInsight

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione suas contas de automação do Azure.
1. Selecione **Galeria de módulos** em **recursos compartilhados**.
1. Digite **AzureRM. Profile** na caixa e pressione ENTER para pesquisar. Selecione o resultado da pesquisa disponível.
1. Na tela **AzureRM. Profile** , selecione **importar**. Marque a caixa para atualizar os módulos do Azure e selecione **OK**.

    ![importar o módulo AzureRM. Profile](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Retorne à galeria de módulos selecionando a **Galeria de módulos** em **recursos compartilhados**.
1. Digite **HDInsight**. Selecione **AzureRM. HDInsight**.

    ![procurar módulos do HDInsight](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. No painel **AzureRM. HDInsight** , selecione **importar** e **OK**.

    ![importar o módulo AzureRM. HDInsight](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Criar credenciais

1. Em **recursos compartilhados**, selecione **credenciais**.
1. Selecione **Adicionar uma credencial**.
1. Insira as informações necessárias no painel **nova credencial** . Essa credencial é para armazenar a senha do cluster, que permitirá que você faça logon no Ambari.

    | Propriedade | Valor |
    | --- | --- |
    | Nome | `cluster-password` |
    | Nome de utilizador | `admin` |
    | Palavra-passe | `SECURE_PASSWORD` |
    | Confirmar palavra-passe | `SECURE_PASSWORD` |

1. Selecione **Criar**.
1. Repita o mesmo processo para uma nova credencial `ssh-password` com o nome de usuário `sshuser` e uma senha de sua escolha. Selecione **Criar**. Essa credencial é para armazenar a senha SSH para o cluster.

    ![Criar credencial](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Criar um runbook para criar um cluster

1. Selecione **Runbooks** em **automação de processo**.
1. Selecione **criar um runbook**.
1. No painel **criar um runbook** , insira um nome para o runbook, como `hdinsight-cluster-create`. Selecione **PowerShell** na lista suspensa **tipo de runbook** .
1. Selecione **Criar**.

    ![criar runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. Insira o código a seguir na tela **Editar runbook do PowerShell** e selecione **publicar**:

    ![publicar runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Criar um runbook para excluir um cluster

1. Selecione **Runbooks** em **automação de processo**.
1. Selecione **criar um runbook**.
1. No painel **criar um runbook** , insira um nome para o runbook, como `hdinsight-cluster-delete`. Selecione **PowerShell** na lista suspensa **tipo de runbook** .
1. Selecione **Criar**.
1. Insira o código a seguir na tela **Editar runbook do PowerShell** e selecione **publicar**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Executar Runbooks

### <a name="create-a-cluster"></a>Criar um cluster

1. Exiba a lista de Runbooks para sua conta de automação, selecionando **Runbooks** em **automação de processo**.
1. Selecione `hdinsight-cluster-create`ou o nome que você usou ao criar o runbook de criação do cluster.
1. Selecione **Iniciar** para executar o runbook imediatamente. Você também pode agendar runbooks para serem executados periodicamente. Consulte [agendando um runbook na automação do Azure](../automation/shared-resources/schedules.md)
1. Insira os parâmetros necessários para o script e selecione **OK**. Isso criará um novo cluster HDInsight com o nome que você especificou no parâmetro **ClusterName** .

    ![executar criar runbook de cluster](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Eliminar um cluster

Exclua o cluster selecionando o `hdinsight-cluster-delete` runbook que você criou. Selecione **Iniciar**, insira o parâmetro **ClusterName** e sselect **OK**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua a conta de automação do Azure que foi criada para evitar encargos indesejados. Para fazer isso, navegue até a portal do Azure, selecione o grupo de recursos em que você criou a conta de automação do Azure, selecione a conta de automação e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerenciar clusters de Apache Hadoop no HDInsight usando Azure PowerShell](hdinsight-administer-use-powershell.md)