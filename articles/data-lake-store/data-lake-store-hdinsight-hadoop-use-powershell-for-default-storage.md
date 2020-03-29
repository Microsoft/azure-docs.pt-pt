---
title: Crie clusters HDInsight com Azure Data Lake Storage Gen1 como armazenamento padrão usando powerShell [ Microsoft Docs'
description: Utilize o Azure PowerShell para criar e utilizar clusters HDInsight com o Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161421"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Crie clusters HDInsight com Azure Data Lake Storage Gen1 como armazenamento padrão usando powerShell

> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilizar o PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilizar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Saiba como utilizar o Azure PowerShell para configurar os clusters Azure HDInsight com o Azure Data Lake Storage Gen1, como armazenamento padrão. Para obter instruções sobre a criação de um cluster HDInsight com data Lake Storage Gen1 como armazenamento adicional, consulte [Criar um cluster HDInsight com Data Lake Storage Gen1 como armazenamento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

Aqui estão algumas considerações importantes para usar o HDInsight com data lake storage Gen1:

* A opção de criar clusters HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento predefinido está disponível para os versões 3.5 e 3.6 da HDInsight.

* A opção de criar clusters HDInsight com acesso a Data Lake Storage Gen1 uma vez que o armazenamento padrão não está *disponível* para clusters HDInsight Premium.

Para configurar o HDInsight para trabalhar com data Lake Storage Gen1 utilizando o PowerShell, siga as instruções nas próximas cinco secções.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de iniciar este tutorial, certifique-se de que cumpre os seguintes requisitos:

* **Uma subscrição Azure**: Ir para [obter o teste gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/)
* **Azure PowerShell 1.0 ou superior**: Veja [como instalar e configurar powerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Para instalar o Windows SDK, vá a [Downloads e ferramentas para o Windows 10](https://dev.windows.com/downloads). O SDK é usado para criar um certificado de segurança.
* **Azure Ative Directory principal**: Este tutorial descreve como criar um diretor de serviço no Azure Ative Directory (Azure AD). No entanto, para criar um diretor de serviço, deve ser administrador da AD Azure. Se for administrador, pode ignorar este pré-requisito e prosseguir com o tutorial.

    >[!NOTE]
    >Só pode criar um diretor de serviço se for administrador da Azure AD. O administrador da AD Azure deve criar um diretor de serviço antes de poder criar um cluster HDInsight com data Lake Storage Gen1. O diretor de serviço deve ser criado com um certificado, tal como descrito na [Create a service principal com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1

Para criar uma conta Gen1 de Armazenamento de Data Lake, faça o seguinte:

1. A partir do seu ambiente de trabalho, abra uma janela PowerShell e, em seguida, introduza os cortes abaixo. Quando lhe for solicitado que faça o seu insessão, inscreva-se como um dos administradores de subscrição ou proprietários. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Se registar o fornecedor de recursos Data Lake Storage `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`Gen1 e receber um erro semelhante ao, a sua subscrição poderá não estar na lista branca para data Lake Storage Gen1. Para permitir a sua subscrição Azure para Data Lake Storage Gen1, siga as instruções em [Get started with Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).
    >

2. Uma conta de Data Lake Storage Gen1 está associada a um grupo de recursos Azure. Comece por criar um grupo de recursos.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Deverá ver um resultado como este:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta Gen1 de Armazenamento de Data Lake. O nome da conta que especifica deve conter apenas letras minúsculas e números.

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

4. A utilização do Data Lake Storage Gen1 como armazenamento predefinido requer que especifique um caminho raiz para o qual os ficheiros específicos do cluster são copiados durante a criação do cluster. Para criar um caminho radicular, que é **/clusters/hdiadlcluster** no corte, utilize os seguintes cmdlets:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em papéis ao Data Lake Storage Gen1
Todas as assinaturas azure estão associadas a uma entidade Azure AD. Os utilizadores e serviços que acedam aos recursos de subscrição utilizando o portal Azure ou a API do Gestor de Recursos Azure devem primeiro autenticar com a AD Azure. O acesso é concedido às subscrições e serviços do Azure, atribuindo-lhes o papel adequado num recurso Azure. Para serviços, um diretor de serviço identifica o serviço em Azure AD.

Esta secção ilustra como conceder um serviço de aplicação, como o HDInsight, acesso a um recurso Azure (a conta Data Lake Storage Gen1 que criou anteriormente). Fá-lo criando um principal de serviço para a aplicação e atribuindo-lhe funções através do PowerShell.

Para configurar a autenticação do Diretório Ativo para data lake storage Gen1, execute as tarefas nas duas secções seguintes.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Certifique-se de que tem o [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de avançar com as etapas desta secção. Você também deve ter criado um diretório, como *C:\mycertdir,* onde você cria o certificado.

1. A partir da janela PowerShell, vá ao local onde instalou o Windows SDK (normalmente, *C:\Program Files (x86)\Windows Kits\10\bin\x86*) e use o utilitário [MakeCert][makecert] para criar um certificado auto-assinado e uma chave privada. Utilize os seguintes comandos:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Será solicitado a introduzir a senha de chave privada. Depois de o comando ser executado com sucesso, deve ver **CertFile.cer** e **mykey.pvk** no diretório de certificados que especificou.
2. Utilize o utilitário [Pvk2Pfx][pvk2pfx] para converter os ficheiros .pvk e .cer que a MakeCert criou para um ficheiro .pfx. Execute o seguinte comando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando for solicitado, introduza a senha de chave privada que especificou anteriormente. O valor que especifica para o parâmetro **-po** é a palavra-passe associada ao ficheiro .pfx. Depois de o comando ter sido concluído com sucesso, deve também consultar um **CertFile.pfx** no diretório de certificados que especificou.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Criar um Anúncio Azure e um diretor de serviço
Nesta secção, cria um diretor de serviço para uma aplicação Azure AD, atribui uma função ao diretor de serviço, e autentica-se como diretor de serviço, fornecendo um certificado. Para criar uma aplicação em Azure AD, executar os seguintes comandos:

1. Colar os seguintes cmdlets na janela da consola PowerShell. Certifique-se de que o valor que especifica para a propriedade **-DisplayName** é único. Os valores para **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

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
2. Crie um diretor de serviço utilizando o ID da aplicação.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda o principal acesso ao serviço principal à raiz de Data Lake Storage Gen1 e a todas as pastas no caminho raiz que especificou anteriormente. Utilize os seguintes cmdlets:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Crie um cluster HDInsight Linux com Data Lake Storage Gen1 como o armazenamento padrão

Nesta secção, cria-se um cluster HDInsight Hadoop Linux com data lake storage Gen1 como armazenamento padrão. Para esta versão, o cluster HDInsight e data lake storage Gen1 devem estar no mesmo local.

1. Recupere o ID do inquilino de subscrição e guarde-o para usar mais tarde.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Crie o cluster HDInsight utilizando os seguintes cmdlets:

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

    Depois de o cmdlet ter sido concluído com sucesso, deve ver uma saída que enumere os detalhes do cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar trabalhos de teste no cluster HDInsight para usar Data Lake Storage Gen1
Depois de configurar um cluster HDInsight, pode executar trabalhos de teste nele para garantir que pode aceder ao Data Lake Storage Gen1. Para tal, faça uma amostra de trabalho da Hive para criar uma tabela que utilize os dados da amostra que já estão disponíveis no Data Lake Storage Gen1 em * \<raiz de cluster>/exemplo/data/sample.log*.

Nesta secção, você faz uma ligação Secure Shell (SSH) ao cluster HDInsight Linux que criou, e depois executa uma consulta de hive de amostra.

* Se estiver a utilizar um cliente Windows para fazer uma ligação SSH ao cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a usar um cliente Linux para fazer uma ligação SSH ao cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir de Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Depois de ter feito a ligação, inicie a interface da linha de comando da Hive (CLI) utilizando o seguinte comando:

        hive
2. Utilize o CLI para introduzir as seguintes declarações para criar uma nova tabela de **veículos nomeados** utilizando os dados da amostra no Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Deve ver a saída de consulta na consola SSH.

    >[!NOTE]
    >O caminho para os dados da amostra `adl:///example/data/`no `adl:///` comando CRITABELA anterior é, onde está a raiz do cluster. Seguindo o exemplo da raiz do cluster especificada neste `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`tutorial, o comando é . Pode utilizar a alternativa mais curta ou fornecer o caminho completo para a raiz do cluster.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Aceder ao Data Lake Storage Gen1 utilizando comandos HDFS
Depois de configurar o cluster HDInsight para utilizar data Lake Storage Gen1, pode utilizar comandos de ficheiros distribuídos Hadoop (HDFS) para aceder à loja.

Nesta secção, você faz uma ligação SSH no cluster HDInsight Linux que criou, e depois executa os comandos HDFS.

* Se estiver a utilizar um cliente Windows para fazer uma ligação SSH ao cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a usar um cliente Linux para fazer uma ligação SSH ao cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir de Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de ter feito a ligação, lista os ficheiros no Data Lake Storage Gen1 utilizando o seguinte comando do sistema de ficheiros HDFS.

    hdfs dfs -ls adl:///

Também pode usar `hdfs dfs -put` o comando para enviar alguns ficheiros `hdfs dfs -ls` para data Lake Storage Gen1, e depois usar para verificar se os ficheiros foram carregados com sucesso.

## <a name="see-also"></a>Consulte também
* [Utilize data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal Azure: Criar um cluster HDInsight para usar data lake storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
