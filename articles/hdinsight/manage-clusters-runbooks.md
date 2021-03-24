---
title: 'Tutorial: Use os runbooks da Azure Automation para criar clusters - Azure HDInsight'
description: Aprenda a criar e eliminar clusters Azure HDInsight com scripts que funcionam na nuvem utilizando os runbooks da Azure Automation.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 5eb0f353579233041bb5ccba46de2549ada7e9b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864793"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Tutorial: Criar clusters Azure HDInsight com Azure Automation

A Azure Automation permite-lhe criar scripts que funcionam na nuvem e gerem os recursos Azure a pedido ou com base num horário. Este artigo descreve como criar livros de execução PowerShell para criar e eliminar clusters Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instale os módulos necessários para interagir com o HDInsight.
> * Criar e armazenar credenciais necessárias durante a criação de clusters.
> * Crie um novo runbook Azure Automation para criar um cluster HDInsight.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta Azure Automation](../automation/automation-quickstart-create-account.md)existente.
* Uma conta de [armazenamento Azure](../storage/common/storage-account-create.md)existente, que será usada como armazenamento de cluster.

## <a name="install-hdinsight-modules"></a>Instalar módulos HDInsight

1. Inscreva-se no [portal Azure](https://portal.azure.com).
1. Selecione as suas Contas de Automação Azure.
1. Selecione **a galeria de módulos** em **Recursos Partilhados.**
1. Digite **AzureRM.Profile** na caixa e acerte entrar para procurar. Selecione o resultado da pesquisa disponível.
1. No ecrã **AzureRM.profile,** **selecione Import**. Verifique a caixa para atualizar os módulos Azure e, em seguida, selecione **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="módulo de import AzureRM.profile" border="false":::

1. Regressar à galeria de módulos selecionando a **galeria de módulos** em **Recursos Partilhados.**
1. Tipo **HDInsight**. Selecione **AzurerM.HDInsight**.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="navegue em módulos HDInsight" border="true":::

1. No painel **AzureRM.HDInsight,** **selecione Import** and **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="módulo AzureRM.HDInsight" border="true":::

## <a name="create-credentials"></a>Criar credenciais

1. Em **Recursos Partilhados**, selecione **Credenciais.**
1. **Selecione Adicione uma credencial**.
1. Introduza as informações necessárias no painel **New Credential.** Esta credencial é para armazenar a senha do cluster, que lhe permitirá iniciar sessão em Ambari.

    | Propriedade | Valor |
    | --- | --- |
    | Nome | `cluster-password` |
    | Nome de utilizador | `admin` |
    | Palavra-passe | `SECURE_PASSWORD` |
    | Confirmar palavra-passe | `SECURE_PASSWORD` |

1. Selecione **Criar**.
1. Repita o mesmo processo para uma nova credencial `ssh-password` com nome de utilizador e uma `sshuser` palavra-passe à sua escolha. Selecione **Criar**. Esta credencial é para armazenar a palavra-passe SSH para o seu cluster.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="Criar credencial" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Criar um livro de corridas para criar um cluster

1. Selecione **Runbooks** em **Automação de Processos.**
1. Selecione **Criar um livro de bordo.**
1. No **painel 'Criar' um painel de recortes,** insira um nome para o livro de recortes, tal como `hdinsight-cluster-create` . Selecione **Powershell** a partir do **dropdown do tipo Runbook.**
1. Selecione **Criar**.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="criar runbook" border="true":::

1. Introduza o seguinte código no ecrã **Editar PowerShell Runbook** e selecione **Publicar:**

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="publicar runbook" border="true":::

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

## <a name="create-a-runbook-to-delete-a-cluster"></a>Criar um livro de execução para eliminar um cluster

1. Selecione **Runbooks** em **Automação de Processos.**
1. Selecione **Criar um livro de bordo.**
1. No **painel 'Criar' um painel de recortes,** insira um nome para o livro de recortes, tal como `hdinsight-cluster-delete` . Selecione **Powershell** a partir do **dropdown do tipo Runbook.**
1. Selecione **Criar**.
1. Introduza o seguinte código no ecrã **Editar PowerShell Runbook** e selecione **Publicar:**

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

## <a name="execute-runbooks"></a>Executar runbooks

### <a name="create-a-cluster"></a>Criar um cluster

1. Consulte a lista de Runbooks para a sua conta Demôm automação, selecionando **Runbooks** em **Automação de Processos.**
1. Selecione `hdinsight-cluster-create` , ou o nome que usou ao criar o seu livro de criação de cluster.
1. Selecione **Comece** a executar o livro de recortes imediatamente. Também pode agendar livros para executar periodicamente. Ver [Agendar um livro de bordo na Azure Automation](../automation/shared-resources/schedules.md)
1. Introduza os parâmetros necessários para o script e selecione **OK**. Isto criará um novo cluster HDInsight com o nome especificado no parâmetro **CLUSTERNAME.**

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="executar criar runbook cluster" border="true":::

### <a name="delete-a-cluster"></a>Eliminar um cluster

Elimine o cluster selecionando o `hdinsight-cluster-delete` livro de recortes que criou. Selecione **Iniciar,** insira o parâmetro **CLUSTERNAME** e sselect **OK**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine a Conta de Automação Azure que foi criada para evitar encargos não intencionais. Para tal, navegue no portal Azure, selecione o grupo de recursos onde criou a Conta de Automação Azure, selecione a Conta De Automação e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir os clusters Apache Hadoop em HDInsight utilizando a Azure PowerShell](hdinsight-administer-use-powershell.md)