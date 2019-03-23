---
title: Utilizar o Data Lake Storage Gen1 com o Hadoop no HDInsight do Azure
description: Saiba como consultar dados da geração 1 de armazenamento do Azure Data Lake e armazenar os resultados da sua análise.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: b567f5e74737c6020a3dd08484354383d45ecb7d
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361894"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Utilizar a geração 1 do Data Lake armazenamento com clusters do HDInsight do Azure

> [!Note] 
> Implementar novos clusters do HDInsight com o [geração 2 de armazenamento do Azure Data Lake](hdinsight-hadoop-use-data-lake-storage-gen2.md) para um desempenho melhorado e novos recursos.

Para analisar dados num cluster do HDInsight, pode armazenar os dados no [armazenamento do Azure](../storage/common/storage-introduction.md), [do Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) ou [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Todas as opções de armazenamento permitem eliminar em segurança os clusters do HDInsight que são utilizados para o cálculo sem perda de dados de utilizador.

Neste artigo, ficará a saber como a geração 1 de armazenamento do Data Lake funciona com clusters do HDInsight. Para saber como o Armazenamento do Azure funciona com clusters do HDInsight, veja [Utilizar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre como criar um cluster do HDInsight, consulte [Apache Hadoop criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Geração 1 de armazenamento do Data Lake é sempre acedido através de um canal seguro, portanto, não há nenhum `adls` nome de esquema de sistema de ficheiros. Utiliza sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para os clusters do HDInsight

Apache Hadoop suporta uma noção do sistema de ficheiros predefinido. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação de cluster do HDInsight, pode especificar um contentor de BLOBs no armazenamento do Azure como o sistema de ficheiros predefinido ou com o HDInsight 3.5 e versões mais recentes, pode selecionar o armazenamento do Azure ou Gen1 de armazenamento do Azure Data Lake como o sistema de ficheiros predefinido com um algumas exceções. 

Clusters do HDInsight podem utilizar o Data Lake Storage Gen1 de duas formas:

* Como armazenamento predefinido
* Como armazenamento adicional, com o Azure Storage Blob como armazenamento predefinido.

A partir de agora, apenas alguns do HDInsight cluster usando Gen1 de armazenamento do Data Lake como armazenamento predefinido e contas de armazenamento adicionais de suporte de tipos/versões:

| Tipo de cluster do HDInsight | Geração 1 de armazenamento do Data Lake como armazenamento predefinido | Geração 1 de armazenamento do Data Lake como armazenamento adicional| Notas |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 3.6 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.5 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não | Não | |
| HDInsight versão 3.2 | Não | Sim | |
| Storm | | |Pode usar a geração 1 de armazenamento do Data Lake para escrever dados de uma topologia do Storm. Também pode utilizar o armazenamento do Data Lake para dados de referência que, em seguida, podem ser lidos por uma topologia Storm.|

> [!WARNING]  
> HDInsight HBase não é suportado com a geração 1 de armazenamento do Azure Data Lake

Com a geração 1 de armazenamento do Data Lake como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de ler ou escrever para o armazenamento do Azure do cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Utilizar o Data Lake Storage Gen1 como armazenamento predefinido

Quando o HDInsight é implementado com o Data Lake Storage Gen1 como armazenamento predefinido, os ficheiros relacionados com o cluster são armazenados no `adl://mydatalakestore/<cluster_root_path>/`, onde `<cluster_root_path>` é o nome de uma pasta que criar no armazenamento do Data Lake. Ao especificar um caminho de raiz para cada cluster, pode utilizar a mesma conta de armazenamento do Data Lake para mais de um cluster. Por isso, pode ter uma configuração em que:

* Cluster1 pode utilizar o caminho `adl://mydatalakestore/cluster1storage`
* Cluster2 pode utilizar o caminho `adl://mydatalakestore/cluster2storage`

Tenha em atenção que ambos os clusters utilizam a mesma conta de geração 1 de armazenamento do Data Lake **mydatalakestore**. Cada cluster tem acesso ao seu próprio sistema de ficheiros de raiz no armazenamento do Data Lake. A experiência de implementação do portal do Azure em particular pede-lhe que utilize um nome de pasta como **/clusters/\<clustername>** para o caminho da raiz.

Para poder utilizar o Data Lake Storage Gen1 como armazenamento predefinido, tem de conceder ao principal de serviço acesso aos seguintes caminhos:

- A raiz da conta de geração 1 de armazenamento do Data Lake.  Por exemplo: adl://mydatalakestore/.
- A pasta com todas as pastas de clusters.  Por exemplo: adl://mydatalakestore/clusters.
- A pasta do cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para obter mais informações para a criação de serviço principal e conceder acesso, consulte Configurar o armazenamento do Data Lake acesso.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>A extrair um certificado do Cofre de chaves do Azure para utilização na criação do cluster

Se pretende configurar a geração 1 de armazenamento do Azure Data Lake como o armazenamento predefinido para um novo cluster e o certificado para o seu principal de serviço é armazenado no Azure Key Vault, existem algumas etapas adicionais necessárias para converter o certificado para o formato correto. Os fragmentos de código seguinte mostram como efetuar a conversão.

Em primeiro lugar, transfira o certificado do Key Vault e extrair o `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Em seguida, converta o `SecretValueText` a um certificado.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Pode utilizar o `$identityCertificate` para implementar um cluster novo como no seguinte fragmento:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Utilizar o Data Lake Storage Gen1 como armazenamento adicional

Pode utilizar Gen1 de armazenamento do Data Lake como armazenamento adicional para o cluster também. Nesses casos, o armazenamento predefinido do cluster pode ser um Blob de armazenamento do Azure ou uma conta de armazenamento do Data Lake. Se estiver a executar tarefas do HDInsight contra aos dados armazenados no armazenamento do Data Lake como armazenamento adicional, tem de utilizar o caminho totalmente qualificado para os ficheiros. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Tenha em atenção que agora não existe nenhum **cluster_root_path** no URL. Isso ocorre porque o armazenamento do Data Lake não é um armazenamento predefinido em vez disso, para que tudo o que precisa fazer é fornecer o caminho para os ficheiros.

Para poder utilizar uma geração de 1 de armazenamento do Data Lake como armazenamento adicional, apenas tem de conceder ao principal de serviço acesso aos caminhos onde os ficheiros estão armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para obter mais informações para a criação de serviço principal e conceder acesso, consulte Configurar o armazenamento do Data Lake acesso.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Utilizar mais do que um contas de armazenamento do Data Lake

Adicionar uma conta de armazenamento do Data Lake como adicional e adicionar mais do que um armazenamento do Data Lake são efetuadas ao dar a permissão de cluster do HDInsight nos dados de contas de armazenamento do Data Lake de uma ou mais. Acesso de armazenamento Consulte Configurar o Data Lake.

## <a name="configure-data-lake-storage-access"></a>Configurar o acesso de armazenamento do Data Lake

Para configurar o acesso de armazenamento do Data Lake do seu cluster do HDInsight, tem de ter um principal de serviço de (Azure AD) do Active Directory do Azure. Apenas um administrador do Azure AD pode criar um principal de serviço. O principal de serviço tem de ser criado com um certificado. Para obter mais informações, consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), e [criar principal de serviço com o certificado do autoassinado self](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se pretender utilizar Gen1 de armazenamento do Azure Data Lake como armazenamento adicional para o cluster do HDInsight, recomendamos vivamente que o faça ao criar o cluster, conforme descrito neste artigo. Adicionar o Azure Data Lake armazenamento Gen1 como armazenamento adicional a um cluster do HDInsight existente não é um cenário suportado.
>

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas podem a aceder aos ficheiros no armazenamento do Data Lake a partir de um cluster do HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Utilizando o formato de caminho abreviado**. Com esta abordagem, substitui o caminho até à raiz do cluster por adl:///. Por isso, no exemplo acima, pode substituir `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` por `adl:///`.

        adl:///<file path>

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder. Por exemplo, se o caminho completo para o ficheiro for:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Pode aceder ao mesmo ficheiro sample.log utilizando, em alternativa, este caminho relativo.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Criar clusters do HDInsight com acesso à geração 1 de armazenamento do Data Lake

Utilize as seguintes ligações para obter instruções detalhadas sobre como criar clusters do HDInsight com acesso à geração 1 de armazenamento do Data Lake.

* [Utilizar o Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Utilizar o PowerShell (com o Data Lake Gen1 de armazenamento como armazenamento predefinido)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Utilizar o PowerShell (com o Data Lake Gen1 de armazenamento como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Utilizar modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Atualizar o certificado do HDInsight para o acesso de geração 1 de armazenamento do Data Lake

O código do PowerShell de exemplo seguinte lê um certificado a partir de um ficheiro local ou o Azure Key Vault e atualiza o seu cluster do HDInsight com o novo certificado para acesso Gen1 de armazenamento do Azure Data Lake. Fornece seu próprio nome de cluster do HDInsight, o nome do grupo de recursos, o ID de subscrição, ID de aplicação, o caminho local para o certificado. Escreva a palavra-passe quando lhe for pedido.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar o Data Lake do Azure compatível com HDFS Gen1 de armazenamento com o HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]
* [Quickstart: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Criar um cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake com o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Utilizar o Apache Hive com o HDInsight][hdinsight-use-hive]
* [Utilizar o Apache Pig com o HDInsight][hdinsight-use-pig]
* [Utilizar Assinaturas de Acesso Partilhado do Storage do Azure para restringir o acesso aos dados com o HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
