---
title: Usar Data Lake Storage Gen1 com Hadoop no Azure HDInsight
description: Saiba como consultar dados de Azure Data Lake Storage Gen1 e armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 309af904f0dbfc0664c4341803cb6a4dc8a2c8a4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839289"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Usar Data Lake Storage Gen1 com clusters do Azure HDInsight

> [!Note]
> Implante novos clusters HDInsight usando [Azure data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) para melhorar o desempenho e novos recursos.

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [armazenamento do Azure](../storage/common/storage-introduction.md), [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)ou [Azure data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Todas as opções de armazenamento permitem que você exclua com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário.

Neste artigo, você aprende como Data Lake Storage Gen1 funciona com clusters HDInsight. Para saber como o Armazenamento do Azure funciona com clusters do HDInsight, veja [Utilizar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre como criar um cluster HDInsight, consulte [criar Apache Hadoop clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 é sempre acessado por meio de um canal seguro, portanto, não há nenhum nome de esquema de sistema de arquivos `adls`. Utiliza sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para clusters HDInsight

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blob no armazenamento do Azure como o sistema de arquivos padrão ou com o HDInsight 3,5 e versões mais recentes, você pode selecionar o armazenamento do Azure ou Azure Data Lake Storage Gen1 como o sistema de arquivos padrão com um poucas exceções.

Os clusters HDInsight podem usar Data Lake Storage Gen1 de duas maneiras:

* Como armazenamento predefinido
* Como armazenamento adicional, com o Azure Storage Blob como armazenamento predefinido.

A partir de agora, apenas alguns dos tipos/versões de cluster HDInsight dão suporte ao uso de Data Lake Storage Gen1 como armazenamento padrão e contas de armazenamento adicionais:

| Tipo de cluster do HDInsight | Data Lake Storage Gen1 como armazenamento padrão | Data Lake Storage Gen1 como armazenamento adicional| Notas |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 4,0 | Não | Não |ADLS Gen1 não tem suporte com o HDInsight 4,0 |
| HDInsight versão 3.6 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.5 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não | Não | |
| HDInsight versão 3.2 | Não | Sim | |
| Storm | | |Você pode usar Data Lake Storage Gen1 para gravar dados de uma topologia do Storm. Você também pode usar Data Lake Storage para dados de referência que podem ser lidos por uma topologia Storm.|

> [!WARNING]  
> O HBase do HDInsight não tem suporte com o Azure Data Lake Storage Gen1

O uso de Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de ler ou gravar no armazenamento do Azure do cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Usar Data Lake Storage Gen1 como armazenamento padrão

Quando o HDInsight é implantado com Data Lake Storage Gen1 como armazenamento padrão, os arquivos relacionados ao cluster são armazenados em `adl://mydatalakestore/<cluster_root_path>/`, em que `<cluster_root_path>` é o nome de uma pasta que você cria no Data Lake Storage. Ao especificar um caminho raiz para cada cluster, você pode usar a mesma conta de Data Lake Storage para mais de um cluster. Por isso, pode ter uma configuração em que:

* Cluster1 pode utilizar o caminho `adl://mydatalakestore/cluster1storage`
* Cluster2 pode utilizar o caminho `adl://mydatalakestore/cluster2storage`

Observe que ambos os clusters usam a mesma conta de Data Lake Storage Gen1 **mydatalakestore**. Cada cluster tem acesso a seu próprio sistema de arquivos raiz no Data Lake Storage. A experiência de implementação do portal do Azure em particular pede-lhe que utilize um nome de pasta como **/clusters/\<clustername>** para o caminho da raiz.

Para poder usar Data Lake Storage Gen1 como armazenamento padrão, você deve conceder acesso à entidade de serviço aos seguintes caminhos:

* A raiz da conta de Data Lake Storage Gen1.  Por exemplo: adl://mydatalakestore/.
* A pasta com todas as pastas de clusters.  Por exemplo: adl://mydatalakestore/clusters.
* A pasta do cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para obter mais informações sobre como criar a entidade de serviço e conceder acesso, consulte Configurar o acesso de Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extraindo um certificado do Azure keyvault para uso na criação do cluster

Se você quiser configurar Azure Data Lake Storage Gen1 como o armazenamento padrão para um novo cluster e o certificado para a entidade de serviço estiver armazenado no Azure Key Vault, há algumas etapas adicionais necessárias para converter o certificado para o formato correto. Os trechos de código a seguir mostram como executar a conversão.

Primeiro, baixe o certificado de Key Vault e extraia o `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Em seguida, converta o `SecretValueText` em um certificado.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Em seguida, você pode usar o `$identityCertificate` para implantar um novo cluster como no trecho a seguir:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Usar Data Lake Storage Gen1 como armazenamento adicional

Você também pode usar Data Lake Storage Gen1 como armazenamento adicional para o cluster. Nesses casos, o armazenamento padrão do cluster pode ser uma Azure Storage Blob ou uma conta de Data Lake Storage. Se você estiver executando trabalhos do HDInsight em relação aos dados armazenados em Data Lake Storage como armazenamento adicional, deverá usar o caminho totalmente qualificado para os arquivos. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Tenha em atenção que agora não existe nenhum **cluster_root_path** no URL. Isso porque Data Lake Storage não é um armazenamento padrão nesse caso, portanto, tudo o que você precisa fazer é fornecer o caminho para os arquivos.

Para poder usar um Data Lake Storage Gen1 como armazenamento adicional, você só precisa conceder acesso à entidade de serviço aos caminhos em que os arquivos são armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para obter mais informações sobre como criar a entidade de serviço e conceder acesso, consulte Configurar o acesso de Data Lake Storage.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Usar mais de uma Data Lake Storage contas

A adição de uma conta de Data Lake Storage como adicional e a adição de mais de uma Data Lake Storage contas são realizadas fornecendo a permissão de cluster do HDInsight em dados em uma ou mais contas de Data Lake Storage. Consulte Configurar o acesso ao Data Lake Storage.

## <a name="configure-data-lake-storage-access"></a>Configurar o acesso ao Data Lake Storage

Para configurar o acesso Data Lake Storage de seu cluster HDInsight, você deve ter uma entidade de serviço do Azure Active Directory (Azure AD). Apenas um administrador do Azure AD pode criar um principal de serviço. O principal de serviço tem de ser criado com um certificado. Para obter mais informações, veja [Início Rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) e [Criar um principal de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se você pretende usar Azure Data Lake Storage Gen1 como armazenamento adicional para o cluster HDInsight, é altamente recomendável que você faça isso enquanto cria o cluster, conforme descrito neste artigo. Adicionar Azure Data Lake Storage Gen1 como armazenamento adicional a um cluster HDInsight existente não é um cenário com suporte.

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Há várias maneiras de acessar os arquivos em Data Lake Storage de um cluster HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Utilizando o formato de caminho abreviado**. Com essa abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    adl:///<file path>
    ```

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos são baseados em uma [conexão SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) com o nó principal do cluster. Os exemplos usam todos os três esquemas de URI. Substitua `DATALAKEACCOUNT` e `CLUSTERNAME` pelos valores relevantes.

#### <a name="a-few-hdfs-commands"></a>Alguns comandos do HDFS

1. Crie um arquivo simples no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de cluster.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie dados do armazenamento local para o armazenamento de cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Liste o conteúdo do diretório no armazenamento de cluster.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criando uma tabela Hive

Três locais de arquivo são mostrados para fins ilustrativos. Para a execução real, use apenas uma das entradas de `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-abmari"></a>Identificar o caminho de armazenamento do Abmari

Para identificar o caminho completo para o repositório padrão configurado, navegue até **HDFS** > **configurações** e insira `fs.defaultFS` na caixa de entrada de filtro.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Criar clusters HDInsight com acesso ao Data Lake Storage Gen1

Use os links a seguir para obter instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Storage Gen1.

* [Utilizar o Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Usando o PowerShell (com Data Lake Storage Gen1 como armazenamento padrão)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Usando o PowerShell (com Data Lake Storage Gen1 como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Utilizar modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Atualizar o certificado do HDInsight para acesso Data Lake Storage Gen1

O código do PowerShell de exemplo a seguir lê um certificado de um arquivo local ou Azure Key Vault e atualiza o cluster HDInsight com o novo certificado para acessar Azure Data Lake Storage Gen1. Forneça seu próprio nome de cluster HDInsight, nome do grupo de recursos, ID da assinatura, ID do aplicativo, caminho local para o certificado. Digite a senha quando solicitado.

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

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

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

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar o Azure Data Lake Storage Gen1 compatível com o HDFS com o HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Criar um cluster HDInsight para usar Data Lake Storage Gen1 usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar assinaturas de acesso compartilhado do armazenamento do Azure para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Tutorial: extrair, transformar e carregar dados usando a consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
