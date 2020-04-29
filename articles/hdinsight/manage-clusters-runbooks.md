---
title: 'Tutorial: Use livros de execução da Automação Azure para criar clusters - Azure HDInsight'
description: Aprenda a criar e eliminar clusters Azure HDInsight com scripts que funcionam na nuvem utilizando livros de execução Da Automação Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75553514"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Tutorial: Criar clusters Azure HDInsight com Automação Azure

A Azure Automation permite-lhe criar scripts que funcionam na nuvem e gerir os recursos azure a pedido ou com base num horário. Este artigo descreve como criar livros de execução PowerShell para criar e eliminar clusters Azure HDInsight.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instale módulos necessários para interagir com o HDInsight.
> * Criar e armazenar credenciais necessárias durante a criação de clusters.
> * Crie um novo livro de execução da Automação Azure para criar um cluster HDInsight.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de [Automação Azure](../automation/automation-quickstart-create-account.md)existente.
* Uma conta de [Armazenamento Azure](../storage/common/storage-account-create.md)existente, que será usada como armazenamento de cluster.

## <a name="install-hdinsight-modules"></a>Instale módulos HDInsight

1. Inscreva-se no [portal Azure.](https://portal.azure.com)
1. Selecione as suas Contas de Automação Azure.
1. Selecione **galeria de Módulos** em **Recursos Partilhados.**
1. Digite **AzureRM.Perfil** na caixa e bata para procurar. Selecione o resultado de pesquisa disponível.
1. No ecrã **de perfil AzureRM.,** selecione **Import**. Verifique a caixa para atualizar os módulos Azure e, em seguida, selecione **OK**.

    ![importar módulo de perfil AzureRM.](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Volte à galeria de módulos selecionando a **galeria Módulos** em **Recursos Partilhados.**
1. Tipo **HDInsight**. Selecione **AzureRM.HDInsight**.

    ![navegar em módulos HDInsight](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. No painel **AzureRM.HDInsight,** selecione **Import** e **OK**.

    ![importar módulo AzureRM.HDInsight](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Criar credenciais

1. Em **Recursos Partilhados,** selecione **Credenciais.**
1. Selecione **Adicionar uma credencial**.
1. Introduza as informações necessárias no painel **New Credential.** Esta credencial é armazenar a palavra-passe do cluster, o que lhe permitirá iniciar sessão em Ambari.

    | Propriedade | Valor |
    | --- | --- |
    | Nome | `cluster-password` |
    | Nome de utilizador | `admin` |
    | Palavra-passe | `SECURE_PASSWORD` |
    | Confirmar palavra-passe | `SECURE_PASSWORD` |

1. Selecione **Criar**.
1. Repita o mesmo processo para `ssh-password` uma nova `sshuser` credencial com nome de utilizador e uma palavra-passe à sua escolha. Selecione **Criar**. Esta credencial é armazenar a senha SSH para o seu cluster.

    ![Criar credencial](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Criar um livro de corridas para criar um cluster

1. Selecione **Runbooks** em **Automação de Processos**.
1. Selecione **Criar um livro de execução**.
1. No painel Criar um livro de **reprodução,** introduza `hdinsight-cluster-create`um nome para o livro de execução, como . Selecione **Powershell** a partir do dropdown do **tipo Runbook.**
1. Selecione **Criar**.

    ![criar livro de corridas](./media/manage-clusters-runbooks/create-runbook.png)

1. Introduza o seguinte código no ecrã **editPowerShell Runbook** e **selecione Publicar:**

    ![publicar livro de execução](./media/manage-clusters-runbooks/publish-runbook.png)

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

## <a name="create-a-runbook-to-delete-a-cluster"></a>Criar um livro de corridas para apagar um cluster

1. Selecione **Runbooks** em **Automação de Processos**.
1. Selecione **Criar um livro de execução**.
1. No painel Criar um livro de **reprodução,** introduza `hdinsight-cluster-delete`um nome para o livro de execução, como . Selecione **Powershell** a partir do dropdown do **tipo Runbook.**
1. Selecione **Criar**.
1. Introduza o seguinte código no ecrã **editPowerShell Runbook** e **selecione Publicar:**

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

## <a name="execute-runbooks"></a>Executar Livros de Execução

### <a name="create-a-cluster"></a>Criar um cluster

1. Consulte a lista de Livros de Execução para a sua conta Automation, selecionando **Runbooks** em Automação de **Processos**.
1. Selecione, `hdinsight-cluster-create`ou o nome que usou ao criar o seu livro de execução de criação de clusters.
1. Selecione **Iniciar** a execução do livro de execução imediatamente. Também pode agendar livros de execução para funcionar periodicamente. Ver [Agendar um livro de corridas na Automação Azure](../automation/shared-resources/schedules.md)
1. Introduza os parâmetros necessários para o script e selecione **OK**. Isto criará um novo cluster HDInsight com o nome que especificou no parâmetro **CLUSTERNAME.**

    ![executar criar livro de execução cluster](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Eliminar um cluster

Elimine o cluster `hdinsight-cluster-delete` selecionando o livro de execução que criou. Selecione **Iniciar,** introduza o parâmetro **CLUSTERNAME** e selecione **OK**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague a Conta de Automação Azure que foi criada para evitar encargos não intencionais. Para tal, navegue para o portal Azure, selecione o grupo de recursos onde criou a Conta de Automação Azure, selecione a Conta de Automação e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir os clusters Apache Hadoop em HDInsight utilizando o Azure PowerShell](hdinsight-administer-use-powershell.md)