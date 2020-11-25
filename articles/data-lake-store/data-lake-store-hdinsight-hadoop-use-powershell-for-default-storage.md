---
title: PowerShell - Cluster HDInsight com Data Lake Storage Gen1 - Azure
description: Utilize a Azure PowerShell para criar e utilizar clusters Azure HDInsight com Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 675f68a36963d19f42cb7c0c5d49ae8c4f0006f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010542"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Crie clusters HDInsight com Azure Data Lake Storage Gen1 como armazenamento padrão usando PowerShell

> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilize o PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilize o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Use Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Aprenda a usar o Azure PowerShell para configurar os clusters Azure HDInsight com a Azure Data Lake Storage Gen1, como armazenamento padrão. Para obter instruções sobre a criação de um cluster HDInsight com data lake storage gen1 como armazenamento adicional, consulte [Criar um cluster HDInsight com data lake storage gen1 como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

Aqui estão algumas considerações importantes para a utilização de HDInsight com Data Lake Storage Gen1:

* A opção de criar clusters HDInsight com acesso a Data Lake Storage Gen1 como armazenamento padrão está disponível para a versão 3.5 e 3.6 do HDInsight.

* A opção de criar clusters HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento padrão não está *disponível* para clusters HDInsight Premium.

Para configurar o HDInsight para trabalhar com a Data Lake Storage Gen1 utilizando o PowerShell, siga as instruções nas próximas cinco secções.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de iniciar este tutorial, certifique-se de que cumpre os seguintes requisitos:

* **Uma subscrição do Azure**: Vá a [obter o julgamento gratuito do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**: Ver [como instalar e configurar o PowerShell](/powershell/azure/).
* **Windows Software Development Kit (SDK)**: Para instalar o Windows SDK, vá a [Downloads e ferramentas para o Windows 10](https://dev.windows.com/downloads). O SDK é usado para criar um certificado de segurança.
* **Azure Ative Directory service principal**: Este tutorial descreve como criar um diretor de serviço em Azure Ative Directory (Azure AD). No entanto, para criar um principal de serviço, você deve ser um administrador AD Azure. Se for administrador, pode ignorar este pré-requisito e proceder ao tutorial.

    >[!NOTE]
    >Só pode criar um principal de serviço se for administrador da AD Azure. O administrador AD Azure deve criar um principal de serviço antes de criar um cluster HDInsight com a Data Lake Storage Gen1. O comitado deve ser criado com um certificado, conforme descrito na [Criar um com certificado de serviço.](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Criar uma conta Azure Data Lake Storage Gen1

Para criar uma conta Desemarramento de Dados Da Gen1, faça o seguinte:

1. A partir do seu ambiente de trabalho, abra uma janela PowerShell e, em seguida, introduza os cortes abaixo. Quando lhe for solicitado que assine, inscreva-se como um dos administradores ou proprietários de subscrição. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Se registar o fornecedor de recursos da Data Lake Storage Gen1 e receber um erro semelhante a, a `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` sua subscrição pode não ser aprovada para data lake storage gen1. Para ativar a sua subscrição Azure para data lake storage gen1, siga as instruções em [Começar com Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).
    >

2. Uma conta Gen1 de armazenamento de data lake está associada a um grupo de recursos Azure. Comece por criar um grupo de recursos.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Deverá ver um resultado como este:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Crie uma conta Gen1 de armazenamento de data lake. O nome da conta que especifica deve conter apenas letras e números minúsculos.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Deve ver um resultado como o seguinte:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

4. A utilização da Data Lake Storage Gen1 como armazenamento predefinido requer que especifique um caminho de raiz para o qual os ficheiros específicos do cluster são copiados durante a criação do cluster. Para criar um caminho de raiz, que é **/clusters/hdiadlcluster** no corte, use os seguintes cmdlets:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em funções à Data Lake Storage Gen1
Todas as subscrições da Azure estão associadas a uma entidade Azure AD. Os utilizadores e serviços que acedam aos recursos de subscrição utilizando o portal Azure ou a API gestora de recursos Azure devem primeiro autenticar-se com a AZure AD. O acesso é concedido a subscrições e serviços da Azure atribuindo-lhes o papel adequado num recurso Azure. Para serviços, um diretor de serviço identifica o serviço em Azure AD.

Esta secção ilustra como conceder um serviço de aplicação, como o HDInsight, o acesso a um recurso Azure (a conta Desarma de Armazenamento de Dados Gen1 que criou anteriormente). Fá-lo criando um principal de serviço para a aplicação e atribuindo-lhe funções através do PowerShell.

Para configurar a autenticação ative directory para data lake storage gen1, execute as tarefas nas duas secções seguintes.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Certifique-se de que tem [o Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de prosseguir com os passos nesta secção. Também deve ter criado um diretório, como *C:\mycertdir,* onde cria o certificado.

1. A partir da janela PowerShell, vá ao local onde instalou o Windows SDK (normalmente, *C:\Program Files (x86)\Windows Kits\10\bin\x86)* e utilize o utilitário [MakeCert][makecert] para criar um certificado auto-assinado e uma chave privada. Utilize os seguintes comandos:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Será solicitado que introduza a senha da chave privada. Depois de o comando ser executado com sucesso, deverá ver **CertFile.cer** e **mykey.pvk** no diretório de certificados que especificou.
2. Utilize o utilitário [Pvk2Pfx][pvk2pfx] para converter os ficheiros .pvk e .cer que o MakeCert criou para um ficheiro .pfx. Execute o seguinte comando:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Quando for solicitado, insira a palavra-passe da chave privada que especificou anteriormente. O valor especificado para o parâmetro **-po** é a palavra-passe que está associada ao ficheiro .pfx. Depois de o comando ter sido concluído com sucesso, também deverá ver um **CertFile.pfx** no diretório de certificados especificado.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Criar um AD Azure e um diretor de serviço
Nesta secção, cria-se um resmanso de serviço para uma aplicação AD Azure, atribui uma função ao principal de serviço e autentica-se como diretor de serviço, fornecendo um certificado. Para criar uma aplicação em Azure AD, executar os seguintes comandos:

1. Cole os seguintes cmdlets na janela da consola PowerShell. Certifique-se de que o valor especificado para a propriedade **-DisplayName** é único. Os valores de **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Crie um principal de serviço utilizando o ID da aplicação.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Conceda ao serviço acesso principal à raiz da Gen1 de armazenamento de dados e a todas as pastas no caminho raiz que especificou anteriormente. Utilize os seguintes cmdlets:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Crie um cluster HDInsight Linux com data lake storage gen1 como o armazenamento padrão

Nesta secção, você cria um cluster HDInsight Hadoop Linux com Data Lake Storage Gen1 como o armazenamento padrão. Para esta versão, o cluster HDInsight e a Data Lake Storage Gen1 devem estar no mesmo local.

1. Recupere a identificação do inquilino de assinatura e guarde-a para usar mais tarde.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Crie o cluster HDInsight utilizando os seguintes cmdlets:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Depois de o cmdlet ter sido concluído com sucesso, deverá ver uma saída que lista os detalhes do cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar trabalhos de teste no cluster HDInsight para usar data lake storage gen1
Depois de configurar um cluster HDInsight, pode fazer testes nele para garantir que pode aceder ao Data Lake Storage Gen1. Para tal, faça uma amostra do trabalho da Hive para criar uma tabela que utilize os dados da amostra que já estão disponíveis na Data Lake Storage Gen1 em *\<cluster root> /exemplo/data/sample.log*. .

Nesta secção, você faz uma ligação Secure Shell (SSH) no cluster HDInsight Linux que criou, e depois executar uma consulta de hive amostra.

* Se estiver a utilizar um cliente Windows para escrutinar uma ligação SSH no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux para fazer uma ligação SSH no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Depois de efetuar a ligação, inicie a interface de linha de comando da Colmeia (CLI) utilizando o seguinte comando:

    ```powershell
    hive
    ```

2. Utilize o CLI para introduzir as seguintes declarações para criar uma nova tabela com **os veículos nomeados** utilizando os dados da amostra na Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Deve ver a saída de consulta na consola SSH.

>[!NOTE]
>O caminho para os dados da amostra no comando CREATE TABLE anterior `adl:///example/data/` é, onde `adl:///` está a raiz do cluster. Seguindo o exemplo da raiz de cluster que está especificada neste tutorial, o comando é `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . Pode utilizar a alternativa mais curta ou fornecer o caminho completo para a raiz do cluster.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Aceder ao Data Lake Storage Gen1 utilizando comandos HDFS
Depois de configurar o cluster HDInsight para utilizar o Data Lake Storage Gen1, pode utilizar comandos de shell do Sistema de Ficheiros Distribuídos Hadoop (HDFS) para aceder à loja.

Nesta secção, você faz uma ligação SSH no cluster HDInsight Linux que criou e, em seguida, executar os comandos HDFS.

* Se estiver a utilizar um cliente Windows para escrutinar uma ligação SSH no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux para fazer uma ligação SSH no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de ter feito a ligação, liste os ficheiros na Data Lake Storage Gen1 utilizando o seguinte comando do sistema de ficheiros HDFS.

```azurepowershell
hdfs dfs -ls adl:///
```

Também pode usar o `hdfs dfs -put` comando para enviar alguns ficheiros para data lake storage gen1 e, em seguida, usar `hdfs dfs -ls` para verificar se os ficheiros foram carregados com sucesso.

## <a name="see-also"></a>Ver também
* [Use data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portal Azure: Criar um cluster HDInsight para utilizar a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx