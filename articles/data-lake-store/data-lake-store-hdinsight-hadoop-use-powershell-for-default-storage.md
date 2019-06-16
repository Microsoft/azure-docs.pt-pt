---
title: Criar clusters do HDInsight com Gen1 de armazenamento do Azure Data Lake como armazenamento predefinido com o PowerShell | Documentos da Microsoft
description: Utilizar o Azure PowerShell para criar e utilizar clusters do HDInsight com Gen1 de armazenamento do Azure Data Lake
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c57a45145d9abc43d0ca79839ea297dfc025db9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161421"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Criar clusters do HDInsight com Gen1 de armazenamento do Azure Data Lake como armazenamento predefinido com o PowerShell

> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilize o PowerShell (para o armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilize o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilize o Gestor de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Saiba como utilizar o Azure PowerShell para configurar clusters do HDInsight do Azure com o Azure Data Lake Storage Gen1, como armazenamento predefinido. Para obter instruções sobre como criar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento adicional, consulte [criar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

Seguem-se algumas considerações importantes para o HDInsight com geração 1 de armazenamento do Data Lake:

* A opção para criar clusters do HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento predefinido está disponível para o HDInsight versão 3.5 e 3.6.

* A opção para criar o HDInsight clusters com acesso para o Data Lake Storage Gen1 como armazenamento predefinido é *não está disponível* para clusters do HDInsight Premium.

Para configurar o HDInsight para trabalhar com geração 1 de armazenamento do Data Lake com o PowerShell, siga as instruções nas seções a seguir cinco.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:

* **Uma subscrição do Azure**: Aceda a [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
* **O Azure PowerShell 1.0 ou superior**: Ver [como instalar e configurar o PowerShell](/powershell/azure/overview).
* **Software Development Kit (SDK) do Windows**: Para instalar o SDK do Windows, aceda a [Downloads e ferramentas para o Windows 10](https://dev.windows.com/downloads). O SDK é usado para criar um certificado de segurança.
* **Principal de serviço do Azure Active Directory**: Este tutorial descreve como criar um serviço principal no Azure Active Directory (Azure AD). No entanto, para criar um principal de serviço, tem de ser um administrador do Azure AD. Se for um administrador, pode ignorar este pré-requisito e continuar o tutorial.

    >[!NOTE]
    >Pode criar um principal de serviço somente se for administrador do Azure AD. Administrador do Azure AD tem de criar um serviço principal antes de poder criar um cluster do HDInsight com a geração 1 de armazenamento do Data Lake. O principal de serviço tem de ser criado com um certificado, conforme descrito em [criar um principal de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Data Lake

Para criar uma conta do Data Lake Storage Gen1, faça o seguinte:

1. Na área de trabalho, abra uma janela do PowerShell e, em seguida, introduza os fragmentos abaixo. Quando lhe for pedido para iniciar sessão, inicie sessão como um dos administradores de subscrição ou proprietários. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Se registar o fornecedor de recursos de geração 1 de armazenamento do Data Lake e receber um erro semelhante a `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, a sua subscrição poderá não estar na lista de permissões para a geração 1 de armazenamento do Data Lake. Para ativar a sua subscrição do Azure para a geração 1 de armazenamento do Data Lake, siga as instruções em [introdução ao Gen1 de armazenamento do Azure Data Lake com o portal do Azure](data-lake-store-get-started-portal.md).
    >

2. Uma conta de geração 1 de armazenamento do Data Lake está associada um grupo de recursos do Azure. Comece por criar um grupo de recursos.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Deverá ver uma saída como esta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta de geração 1 de armazenamento do Data Lake. O nome da conta que especificar tem de conter apenas letras minúsculas e números.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    Deve ver um resultado como o seguinte:

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

4. Usando a geração 1 de armazenamento do Data Lake como armazenamento predefinido requer que especifique um caminho de raiz para a qual os arquivos específicos do cluster são copiados durante a criação do cluster. Para criar um caminho de raiz, que é **/clusters/hdiadlcluster** no fragmento, utilize os seguintes cmdlets:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em funções para a geração 1 de armazenamento do Data Lake
Cada subscrição do Azure está associada uma entidade do Azure AD. Utilizadores e serviços que acedam aos recursos de subscrição com o portal do Azure ou a API do Azure Resource Manager devem primeiro autenticar com o Azure AD. Conceder o acesso às subscrições do Azure e serviços ao atribuir-lhes a função adequada num recurso do Azure. Para os serviços, um principal de serviço identifica o serviço no Azure AD.

Esta secção ilustra como conceder um serviço de aplicações, como o HDInsight, acesso a um recurso do Azure (a conta de geração 1 do Data Lake armazenamento que criou anteriormente). Pode fazê-lo através da criação de um serviço principal da aplicação e atribuir funções à mesma através do PowerShell.

Para configurar a autenticação do Active Directory para a geração 1 de armazenamento do Data Lake, execute as tarefas nas duas secções seguintes.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Certifique-se de que tem [SDK do Windows](https://dev.windows.com/en-us/downloads) instalado antes de continuar com os passos nesta secção. Tem de ter também criado um diretório, como *C:\mycertdir*, onde criar o certificado.

1. A partir da janela do PowerShell, aceda à localização onde instalou o SDK do Windows (normalmente, *C:\Program Files (x86) \Windows Kits\10\bin\x86*) e utilizar os [MakeCert] [ makecert] utilitário para criar um certificado autoassinado e uma chave privada. Utilize os seguintes comandos:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Será solicitado que introduza a palavra-passe da chave privada. Depois do comando for executado com êxito, deverá ver **CertFile.cer** e **mykey.pvk** no diretório de certificado que especificou.
2. Utilize o [Pvk2Pfx] [ pvk2pfx] utilitário para converter os ficheiros de .pvk e. cer que criou o MakeCert para um ficheiro. pfx. Execute o seguinte comando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando lhe for pedido, introduza a senha da chave privada que especificou anteriormente. O valor que especificar para o **-oC** parâmetro é a palavra-passe associada ao ficheiro. pfx. Depois do comando foi concluído com êxito, também deverá ver um **CertFile.pfx** no diretório de certificado que especificou.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Criar um Azure AD e um principal de serviço
Nesta secção, criar um principal de serviço para uma aplicação do Azure AD, atribuir uma função ao principal de serviço e efetuar a autenticação como o principal de serviço ao fornecer um certificado. Para criar uma aplicação no Azure AD, execute os seguintes comandos:

1. Cole os seguintes cmdlets na janela de consola do PowerShell. Certifique-se de que o valor especificado para o **- DisplayName** propriedade é exclusiva. Os valores para **- home page** e **- IdentiferUris** são valores de marcador de posição e não são verificadas.

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
2. Criar um principal de serviço com o ID da aplicação.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda o acesso de principal de serviço para a raiz de geração 1 de armazenamento do Data Lake e todas as pastas no caminho de raiz que especificou anteriormente. Utilize os seguintes cmdlets:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Criar um cluster Linux de HDInsight com Data Lake Storage Gen1 como armazenamento predefinido

Nesta secção, vai criar um cluster do HDInsight Hadoop Linux com a geração 1 de armazenamento do Data Lake como armazenamento predefinido. Nesta versão, o cluster do HDInsight e a geração 1 de armazenamento do Data Lake tem de ser na mesma localização.

1. Obter o ID de inquilino da subscrição e armazená-lo para utilizar mais tarde.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Crie o cluster do HDInsight com os seguintes cmdlets:

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

    Depois do cmdlet foi concluído com êxito, deverá ver uma saída que lista os detalhes do cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar tarefas de teste no cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake
Depois de configurar um cluster do HDInsight, pode executar tarefas de teste no mesmo para se certificar de que ele pode acessar Gen1 de armazenamento do Data Lake. Para tal, execute uma tarefa do Hive de exemplo para criar uma tabela que usa os dados de exemplo que já estão disponíveis no Data Lake Storage Gen1 em  *\<raiz do cluster > /example/data/sample.log*.

Nesta secção, estabelecer uma ligação de Secure Shell (SSH) no cluster do Linux de HDInsight que criou e, em seguida, executar uma consulta do Hive de exemplo.

* Se estiver a utilizar um cliente do Windows para fazer uma ligação SSH ao cluster, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux para fazer uma ligação SSH ao cluster, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Depois de efetuar a ligação, inicie a interface de linha de comandos (CLI) de Hive com o seguinte comando:

        hive
2. Utilizar a CLI para introduzir as seguintes instruções para criar uma nova tabela com o nome **veículos** com os dados de exemplo no Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Deverá ver o resultado da consulta na consola do SSH.

    >[!NOTE]
    >É o caminho para os dados de exemplo no comando anterior CREATE TABLE `adl:///example/data/`, onde `adl:///` é a raiz do cluster. Seguindo o exemplo de raiz do cluster especificado neste tutorial, o comando é `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Pode utilizar a alternativa mais curta ou forneça o caminho completo para a raiz do cluster.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Acesso Data Lake Storage Gen1 através dos comandos HDFS
Depois de configurar o cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake, pode utilizar comandos da shell Hadoop Distributed File System (HDFS) para aceder à loja.

Nesta secção, estabelecer uma ligação de SSH no cluster do Linux de HDInsight que criou e, em seguida, executar os comandos HDFS.

* Se estiver a utilizar um cliente do Windows para fazer uma ligação SSH ao cluster, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente Linux para fazer uma ligação SSH ao cluster, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Após fazer a ligação, liste os ficheiros na geração 1 de armazenamento do Data Lake ao utilizar o seguinte comando do sistema de ficheiros do HDFS.

    hdfs dfs -ls adl:///

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para geração 1 de armazenamento do Data Lake e, em seguida, utilizar `hdfs dfs -ls` para verificar se os ficheiros foram carregados com êxito.

## <a name="see-also"></a>Consulte também
* [Utilizar a geração 1 do Data Lake armazenamento com clusters do HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal do Azure: Criar um cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
