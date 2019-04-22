---
title: 'PowerShell: Cluster HDInsight do Azure com o Azure Data Lake Storage Gen1 como armazenamento adicional | Documentos da Microsoft'
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f78ad8d58bb1bc760a31b792b44a4a39ed25e1f3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880105"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Utilizar o Azure PowerShell para criar um cluster do HDInsight com o Azure Data Lake Storage Gen1 (como armazenamento adicional)

> [!div class="op_single_selector"]
> * [Utilizar o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Com o PowerShell (para o armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Com o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Com o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como utilizar o Azure PowerShell para configurar um cluster do HDInsight com o Azure Data Lake Storage Gen1, **como armazenamento adicional**. Para obter instruções sobre como criar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento predefinido, consulte [criar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento predefinido](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Se pretender utilizar a geração 1 de armazenamento do Data Lake como armazenamento adicional para o cluster do HDInsight, recomendamos vivamente que o faça ao criar o cluster, conforme descrito neste artigo. Adicionar Gen1 de armazenamento do Data Lake como armazenamento adicional a um HDInsight existente o cluster é um complicado processo e propenso a erros.
>

Para tipos de cluster suportadas, a geração 1 de armazenamento do Data Lake pode ser utilizado como um armazenamento predefinido ou uma conta de armazenamento adicional. Quando Gen1 de armazenamento do Data Lake é utilizado como armazenamento adicional, a conta de armazenamento predefinida para os clusters continuarão a estar Blobs de armazenamento do Azure (WASB) e os ficheiros relacionados com o cluster (por exemplo, registos, etc.) ainda são escritos para o armazenamento predefinido, enquanto os dados que pretende processo pode ser armazenado numa conta de geração 1 de armazenamento do Data Lake. Com a geração 1 de armazenamento do Data Lake como uma conta de armazenamento adicional não afeta desempenho ou a capacidade de leitura/escrita para o armazenamento do cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Utilizar a geração 1 de armazenamento do Data Lake para o armazenamento de cluster do HDInsight

Seguem-se algumas considerações importantes para o HDInsight com geração 1 de armazenamento do Data Lake:

* Opção para criar clusters do HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento adicional está disponível para versões do HDInsight 3.2, 3.4, 3.5 e 3.6.

Configurar o HDInsight para trabalhar com a geração 1 de armazenamento do Data Lake com o PowerShell envolve os seguintes passos:

* Criar uma conta de geração 1 de armazenamento do Data Lake
* Configurar a autenticação para acesso baseado em funções para a geração 1 de armazenamento do Data Lake
* Criar cluster do HDInsight com a autenticação para a geração 1 de armazenamento do Data Lake
* Executar uma tarefa de teste no cluster

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Pode instalá-lo a partir [daqui](https://dev.windows.com/en-us/downloads). Utilize isto para criar um certificado de segurança.
* **Principal de serviço de diretório do Azure Active Directory**. Os passos neste tutorial fornecem instruções sobre como criar um principal de serviço no Azure AD. No entanto, tem de ser um administrador do Azure AD para poder criar um principal de serviço. Se for um administrador do Azure AD, pode ignorar este pré-requisito e continuar o tutorial.

    **Se não for um administrador do Azure AD**, não será capaz de executar os passos necessários para criar um principal de serviço. Nesse caso, o administrador do Azure AD tem de criar um principal de serviço antes de poder criar um cluster do HDInsight com a geração 1 de armazenamento do Data Lake. Além disso, o principal de serviço tem de ser criado usando um certificado, conforme descrito em [criar um principal de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Data Lake
Siga estes passos para criar uma conta de geração 1 de armazenamento do Data Lake.

1. Na área de trabalho, abra uma nova janela do PowerShell do Azure e introduza o fragmento seguinte. Quando lhe for pedido para iniciar sessão, certifique-se de que inicia sessão como um do proprietário/administrador da subscrição:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Se receber um erro semelhante a `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ao registar o fornecedor de recursos de geração 1 de armazenamento do Data Lake, é possível que a sua subscrição não está na lista de permissões para a geração 1 de armazenamento do Data Lake. Certifique-se de ativar a sua subscrição do Azure para a geração 1 de armazenamento do Data Lake através destas [instruções](data-lake-store-get-started-portal.md).
   >
   >
2. Uma conta de geração 1 de armazenamento do Data Lake está associada um grupo de recursos do Azure. Comece por criar um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Deverá ver uma saída como esta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta de geração 1 de armazenamento do Data Lake. O nome da conta que especificar tem de conter apenas letras minúsculas e números.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
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

5. Carregar alguns dados de exemplo para a geração 1 de armazenamento do Data Lake. Usaremos isso neste artigo para verificar se os dados estão acessíveis a partir de um cluster do HDInsight. Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em funções para a geração 1 de armazenamento do Data Lake

Cada subscrição do Azure está associada um Azure Active Directory. Utilizadores e serviços que acedam aos recursos da subscrição com o portal do Azure ou a API do Azure Resource Manager devem primeiro autenticar com esse Azure Active Directory. Conceder o acesso às subscrições do Azure e serviços ao atribuir-lhes a função adequada num recurso do Azure.  Para os serviços, um principal de serviço identifica o serviço no Azure Active Directory (AAD). Esta secção ilustra como conceder um serviço de aplicações, como o HDInsight, acesso a um recurso do Azure (a conta de geração 1 do Data Lake armazenamento que criou anteriormente) ao criar um principal de serviço para a aplicação e atribuir funções para que, através do Azure PowerShell.

Para configurar a autenticação do Active Directory para a geração 1 de armazenamento do Data Lake, tem de efetuar as seguintes tarefas.

* Criar um certificado autoassinado
* Criar uma aplicação no Azure Active Directory e um Principal de serviço

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Certifique-se de que tem [SDK do Windows](https://dev.windows.com/en-us/downloads) instalado antes de continuar com os passos nesta secção. Tem de ter também criado um diretório, como **C:\mycertdir**, onde será criado o certificado.

1. A partir da janela do PowerShell, navegue para a localização onde instalou o SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e utilizar o [MakeCert] [ makecert] utilitário para criar um certificado autoassinado e uma chave privada. Utilize os seguintes comandos.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Será solicitado que introduza a palavra-passe da chave privada. Depois do comando executado com êxito, deverá ver um **CertFile.cer** e **mykey.pvk** no diretório de certificado que especificou.
2. Utilize o [Pvk2Pfx] [ pvk2pfx] utilitário para converter os ficheiros de .pvk e. cer que criou o MakeCert para um ficheiro. pfx. Execute o seguinte comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando lhe for pedido introduza a senha da chave privada que especificou anteriormente. O valor que especificar para o **-oC** parâmetro é a palavra-passe que estão associada com o ficheiro. pfx. Depois do comando for concluído com êxito, também deverá ver um CertFile.pfx no diretório de certificado que especificou.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Azure Active Directory e um principal de serviço

Nesta secção, execute os passos para criar um serviço principal para uma aplicação do Azure Active Directory, atribuir uma função ao principal de serviço e autenticar-se como o principal de serviço ao fornecer um certificado. Execute os seguintes comandos para criar uma aplicação no Azure Active Directory.

1. Cole os seguintes cmdlets na janela de consola do PowerShell. Certificar-se de que o valor que especificar para o **- DisplayName** propriedade é exclusiva. Além disso, os valores para **- home page** e **- IdentiferUris** são valores de marcador de posição e não são verificadas.

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
3. Conceda o acesso de principal de serviço para a pasta de geração 1 do Data Lake Storage e o ficheiro que irá aceder a partir do cluster do HDInsight. O trecho a seguir fornece acesso à raiz da conta do Data Lake Storage Gen1 (onde copiou o ficheiro de dados de exemplo) e o próprio ficheiro.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster Linux de HDInsight com Data Lake Storage Gen1 como armazenamento adicional

Nesta secção, vamos criar um cluster do HDInsight Hadoop Linux com a geração 1 de armazenamento do Data Lake como armazenamento adicional. Nesta versão, o cluster do HDInsight e a conta de geração 1 de armazenamento do Data Lake tem de ser na mesma localização.

1. Começar a obter o ID de inquilino da subscrição. Irá precisar dele mais tarde.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Nesta versão, para um cluster de Hadoop, Gen1 de armazenamento do Data Lake pode apenas ser utilizado como armazenamento adicional para o cluster. O armazenamento predefinido continuará a ser os blobs de armazenamento do Azure (WASB). Por isso, primeiro, vamos criar a conta de armazenamento e os contentores de armazenamento necessários para o cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Crie o cluster do HDInsight. Utilize os seguintes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Depois do cmdlet for concluído com êxito, deverá ver uma saída lista os detalhes do cluster.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Executar tarefas de teste no cluster do HDInsight para utilizar a conta de geração 1 de armazenamento do Data Lake
Depois de configurar um cluster do HDInsight, pode executar tarefas de teste no cluster para testar que o cluster do HDInsight pode aceder a geração 1 de armazenamento do Data Lake. Para tal, executamos uma tarefa do Hive de exemplo que cria uma tabela com os dados de exemplo carregado anteriormente para a sua conta de geração 1 de armazenamento do Data Lake.

Nesta secção, irá SSH no cluster do Linux de HDInsight que criou e execute a uma consulta do Hive de exemplo.

* Se estiver a utilizar um cliente do Windows para encaminhar o SSH para o cluster, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente de Linux para encaminhar o SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Assim que estiver ligado, inicie a CLI do Hive com o seguinte comando:

        hive
2. Com a CLI, introduza as seguintes instruções para criar uma nova tabela com o nome **veículos** com os dados de exemplo no Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Deverá ver um resultado semelhante ao seguinte:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Aceder a geração 1 de armazenamento do Data Lake com comandos do HDFS
Depois de configurar o cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake, pode utilizar os comandos da shell HDFS para aceder à loja.

Nesta seção irá SSH no cluster do Linux de HDInsight que criou e execute os comandos HDFS.

* Se estiver a utilizar um cliente do Windows para encaminhar o SSH para o cluster, consulte [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a utilizar um cliente de Linux para encaminhar o SSH para o cluster, consulte o artigo [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Assim que estiver ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros na conta de geração 1 de armazenamento do Data Lake.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Isso deve listar o ficheiro que carregou anteriormente para a geração 1 de armazenamento do Data Lake.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para geração 1 de armazenamento do Data Lake e, em seguida, utilizar `hdfs dfs -ls` para verificar se os ficheiros foram carregados com êxito.

## <a name="see-also"></a>Consultar Também
* [Utilizar a geração 1 do Data Lake armazenamento com clusters do HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Criar um cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
