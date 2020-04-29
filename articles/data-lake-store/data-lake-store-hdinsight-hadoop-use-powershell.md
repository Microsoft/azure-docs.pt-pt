---
title: 'PowerShell: Cluster Azure HDInsight com Azure Data Lake Storage Gen1 como armazenamento adicionais [ armazenamento adicionais ] Microsoft Docs'
description: Aprenda a usar o Azure PowerShell para configurar um cluster HDInsight com o Azure Data Lake Storage Gen1 como armazenamento adicional.
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
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78970129"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Utilize o Azure PowerShell para criar um cluster HDInsight com o Azure Data Lake Storage Gen1 (como armazenamento adicional)

> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilização do PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilização do PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usando o Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o Azure PowerShell para configurar um cluster HDInsight com o Azure Data Lake Storage Gen1, **como armazenamento adicional**. Para obter instruções sobre como criar um cluster HDInsight com data Lake Storage Gen1 como armazenamento padrão, consulte [Criar um cluster HDInsight com data Lake Storage Gen1 como armazenamento padrão](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Se vai utilizar o Data Lake Storage Gen1 como armazenamento adicional para o cluster HDInsight, recomendamos vivamente que o faça enquanto cria o cluster como descrito neste artigo. Adicionar data lake storage Gen1 como armazenamento adicional a um cluster HDInsight existente é um processo complicado e propenso a erros.
>

Para tipos de cluster suportados, o Data Lake Storage Gen1 pode ser usado como uma conta de armazenamento padrão ou de armazenamento adicional. Quando o Data Lake Storage Gen1 é usado como armazenamento adicional, a conta de armazenamento padrão para os clusters continuará a ser Blobs de Armazenamento Azure (WASB) e os ficheiros relacionados com o cluster (como registos, etc.) ainda são escritos para o armazenamento predefinido, enquanto os dados que pretende processar podem ser armazenados numa conta data Lake Storage Gen1. A utilização do Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de ler/escrever para o armazenamento do cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Utilização de Data Lake Storage Gen1 para armazenamento de cluster HDInsight

Aqui estão algumas considerações importantes para usar o HDInsight com data lake storage Gen1:

* Opção de criar clusters HDInsight com acesso a Data Lake Storage Gen1 como armazenamento adicional está disponível para as versões HDInsight 3.2, 3.4, 3.5 e 3.6.

Configurar o HDInsight para trabalhar com data Lake Storage Gen1 utilizando powerShell envolve os seguintes passos:

* Criar uma conta do Data Lake Storage Gen1
* Configurar a autenticação para acesso baseado em papéis ao Data Lake Storage Gen1
* Criar cluster HDInsight com autenticação para Data Lake Storage Gen1
* Executar um trabalho de teste no cluster

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Pode instalá-lo a partir [daqui](https://dev.windows.com/en-us/downloads). Usa isto para criar um certificado de segurança.
* **Azure Ative Diretor de Serviço**de Diretório Ativo. Os passos neste tutorial fornecem instruções sobre como criar um diretor de serviço em Azure AD. No entanto, deve ser administrador da AD Azure para poder criar um diretor de serviço. Se for administrador da AD Azure, pode ignorar este pré-requisito e prosseguir com o tutorial.

    Se não for administrador da **AD Azure,** não poderá executar os passos necessários para criar um diretor de serviço. Neste caso, o seu administrador Azure AD deve primeiro criar um diretor de serviço antes de poder criar um cluster HDInsight com data Lake Storage Gen1. Além disso, o diretor de serviço deve ser criado com um certificado, conforme descrito na [Create a service principal com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
Siga estes passos para criar uma conta Gen1 de Armazenamento de Data Lake.

1. A partir do seu ambiente de trabalho, abra uma nova janela Azure PowerShell e introduza o seguinte corte. Quando for solicitado para iniciar sessão, certifique-se de que faz login como um dos administradores/proprietários de subscrição:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Se receber um erro `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` semelhante ao registar o fornecedor de recursos Data Lake Storage Gen1, é possível que a sua subscrição não esteja listada de forma branca para data Lake Storage Gen1. Certifique-se de que ativa a sua subscrição Azure para Data Lake Storage Gen1 seguindo estas [instruções](data-lake-store-get-started-portal.md).
   >
   >
2. Uma conta de Data Lake Storage Gen1 está associada a um Grupo de Recursos Azure. Comece por criar um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Deverá ver um resultado como este:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta Gen1 de Armazenamento de Data Lake. O nome da conta que especifica deve conter apenas letras minúsculas e números.

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

5. Faça upload de alguns dados da amostra para Data Lake Storage Gen1. Usaremos isto mais tarde neste artigo para verificar se os dados estão acessíveis a partir de um cluster HDInsight. Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em papéis ao Data Lake Storage Gen1

Cada subscrição do Azure está associada a um Diretório Ativo Azure. Os utilizadores e serviços que acedam aos recursos da subscrição utilizando o portal Azure ou o Azure Resource Manager API devem primeiro autenticar com esse Diretório Ativo Azure. O acesso é concedido às subscrições e serviços do Azure, atribuindo-lhes o papel adequado num recurso Azure.  Para serviços, um diretor de serviço identifica o serviço no Diretório Ativo Azure (AAD). Esta secção ilustra como conceder um serviço de aplicação, como o HDInsight, acesso a um recurso Azure (a conta Data Lake Storage Gen1 que criou anteriormente) criando um principal de serviço para a aplicação e atribuindo funções a esse através do Azure PowerShell.

Para configurar a autenticação do Diretório Ativo para data lake storage Gen1, você deve executar as seguintes tarefas.

* Criar um certificado autoassinado
* Criar uma aplicação no Azure Ative Directory e um Diretor de Serviço

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Certifique-se de que tem o [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de avançar com as etapas desta secção. Você também deve ter criado um diretório, como **C:\mycertdir,** onde o certificado será criado.

1. A partir da janela PowerShell, navegue até ao local onde `C:\Program Files (x86)\Windows Kits\10\bin\x86` instalou o Windows SDK (normalmente, e utilize o utilitário [MakeCert][makecert] para criar um certificado auto-assinado e uma chave privada. Utilize os seguintes comandos.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Será solicitado a introduzir a senha de chave privada. Após a execução do comando com sucesso, deverá ver um **CertFile.cer** e **mykey.pvk** no diretório de certificados que especificou.
2. Utilize o utilitário [Pvk2Pfx][pvk2pfx] para converter os ficheiros .pvk e .cer que a MakeCert criou para um ficheiro .pfx. Execute o seguinte comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando solicitado introduza a senha de chave privada especificada anteriormente. O valor que especifica para o parâmetro **-po** é a palavra-passe que está associada ao ficheiro .pfx. Depois de o comando ter terminado com sucesso, deve também ver um CertFile.pfx no diretório de certificados especificado.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Diretório Ativo Azure e um diretor de serviço

Nesta secção, executa os passos para criar um diretor de serviço para uma aplicação azure Ative Directy, atribuir uma função ao diretor de serviço, e autenticar como diretor de serviço, fornecendo um certificado. Executar os seguintes comandos para criar uma aplicação no Diretório Ativo Azure.

1. Colar os seguintes cmdlets na janela da consola PowerShell. Certifique-se de que o valor que especifica para a propriedade **-DisplayName** é único. Além disso, os valores para **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

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
3. Conceda o principal acesso ao serviço principal à pasta Data Lake Storage Gen1 e ao ficheiro a que acederá a partir do cluster HDInsight. O corte abaixo fornece acesso à raiz da conta Data Lake Storage Gen1 (onde copiou o ficheiro de dados da amostra) e ao próprio ficheiro.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Crie um cluster HDInsight Linux com Data Lake Storage Gen1 como armazenamento adicional

Nesta secção, criamos um cluster HDInsight Hadoop Linux com Data Lake Storage Gen1 como armazenamento adicional. Para esta versão, o cluster HDInsight e a conta Data Lake Storage Gen1 devem estar no mesmo local.

1. Comece com a recuperação da identificação do inquilino de subscrição. Vai precisar disso mais tarde.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Para esta libertação, para um cluster Hadoop, data Lake Storage Gen1 só pode ser usado como um armazenamento adicional para o cluster. O armazenamento predefinido continuará a ser as bolhas de armazenamento Azure (WASB). Então, primeiro criaremos a conta de armazenamento e os recipientes de armazenamento necessários para o cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Crie o cluster HDInsight. Utilize os seguintes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Depois de o cmdlet ter terminado com sucesso, deve ver uma saída listando os detalhes do cluster.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Executar trabalhos de teste no cluster HDInsight para usar a conta Data Lake Storage Gen1
Depois de configurar um cluster HDInsight, pode executar trabalhos de teste no cluster para testar que o cluster HDInsight pode aceder ao Data Lake Storage Gen1. Para tal, vamos executar uma amostra de trabalho da Hive que cria uma tabela usando os dados da amostra que você carregou anteriormente para a sua conta Data Lake Storage Gen1.

Nesta secção, você irá SSH no cluster HDInsight Linux que criou e executar á amostra da consulta da Hive.

* Se estiver a utilizar um cliente Windows para ssh no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a usar um cliente Linux para ssh no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Uma vez ligado, inicie o CLI da Colmeia utilizando o seguinte comando:

        hive
2. Utilizando o CLI, introduza as seguintes declarações para criar uma nova tabela de **veículos nomeados** utilizando os dados da amostra no Data Lake Storage Gen1:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Aceder ao Data Lake Storage Gen1 utilizando comandos HDFS
Depois de configurar o cluster HDInsight para utilizar data lake storage Gen1, pode utilizar os comandos de concha HDFS para aceder à loja.

Nesta secção, irá sSH no cluster HDInsight Linux que criou e executará os comandos HDFS.

* Se estiver a utilizar um cliente Windows para ssh no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se estiver a usar um cliente Linux para ssh no cluster, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Uma vez ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros na conta Data Lake Storage Gen1.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Isto deve listar o ficheiro que fez o upload anteriormente para data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Também pode usar `hdfs dfs -put` o comando para enviar alguns ficheiros `hdfs dfs -ls` para data Lake Storage Gen1, e depois usar para verificar se os ficheiros foram carregados com sucesso.

## <a name="see-also"></a>Veja também
* [Utilize data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Criar um cluster HDInsight para usar data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
