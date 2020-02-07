---
title: Use Data Lake Storage Gen1 com Hadoop em Azure HDInsight
description: Saiba como consultar dados do Azure Data Lake Storage Gen1 e armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 23c292a950deea262ee063b4141b07a4f64f9f84
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061333"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Utilize data lake storage Gen1 com clusters Azure HDInsight

> [!Note]
> Implemente novos clusters HDInsight utilizando [o Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) para melhorar o desempenho e novas funcionalidades.

Para analisar dados no cluster HDInsight, pode armazenar os dados no [Azure Storage,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md), ou [azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Todas as opções de armazenamento permitem eliminar com segurança os clusters HDInsight que são utilizados para a computação sem perder dados do utilizador.

Neste artigo, você aprende como Data Lake Storage Gen1 funciona com clusters HDInsight. Para saber como o Armazenamento do Azure funciona com clusters do HDInsight, veja [Utilizar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre a criação de um cluster HDInsight, consulte [Create Apache Hadoop clusters em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 é sempre acedido através de um canal seguro, por isso não existe `adls` nome do sistema de ficheiros. Utiliza sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para clusters HDInsight

Apache Hadoop suporta uma noção do sistema de ficheiros padrão. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação de cluster HDInsight, pode especificar um recipiente blob no Armazenamento Azure como sistema de ficheiros padrão, ou com versões HDInsight 3.5 e versões mais recentes, pode selecionar o Armazenamento Azure ou o Azure Data Lake Storage Gen1 como o sistema de ficheiros predefinido com um poucas exceções.

Os clusters HDInsight podem usar data Lake Storage Gen1 de duas maneiras:

* Como armazenamento predefinido
* Como armazenamento adicional, com o Azure Storage Blob como armazenamento predefinido.

A partir de agora, apenas alguns dos tipos/versões de cluster HDInsight suportam usando data Lake Storage Gen1 como armazenamento padrão e contas de armazenamento adicionais:

| Tipo de cluster do HDInsight | Data Lake Storage Gen1 como armazenamento padrão | Data Lake Storage Gen1 como armazenamento adicional| Notas |
|------------------------|------------------------------------|---------------------------------------|------|
| Versão HDInsight 4.0 | Não | Não |ADLS Gen1 não é suportada com HDInsight 4.0 |
| HDInsight versão 3.6 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.5 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não | Não | |
| HDInsight versão 3.2 | Não | Sim | |
| Storm | | |Você pode usar Data Lake Storage Gen1 para escrever dados de uma topologia storm. Também pode utilizar o Data Lake Storage para dados de referência que podem ser lidos por uma topologia storm.|

> [!WARNING]  
> HDInsight HBase não é suportado com Azure Data Lake Storage Gen1

A utilização do Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de ler ou escrever para o armazenamento azure a partir do cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Use data Lake Storage Gen1 como armazenamento padrão

Quando o HDInsight é implantado com data Lake Storage Gen1 como armazenamento padrão, os ficheiros relacionados com o cluster são armazenados em `adl://mydatalakestore/<cluster_root_path>/`, onde `<cluster_root_path>` é o nome de uma pasta que cria no Armazenamento de Data Lake. Especificando um caminho de raiz para cada cluster, pode utilizar a mesma conta de Armazenamento de Data Lake para mais de um cluster. Por isso, pode ter uma configuração em que:

* Cluster1 pode utilizar o caminho `adl://mydatalakestore/cluster1storage`
* Cluster2 pode utilizar o caminho `adl://mydatalakestore/cluster2storage`

Note que ambos os clusters usam a mesma conta data Lake Storage Gen1 **mydatalakestore**. Cada cluster tem acesso ao seu próprio sistema de ficheiros raiz no Armazenamento de Data Lake. A experiência de implementação do portal do Azure em particular pede-lhe que utilize um nome de pasta como **/clusters/\<clustername>** para o caminho da raiz.

Para poder utilizar o Data Lake Storage Gen1 como armazenamento predefinido, deve conceder ao serviço o acesso principal aos seguintes caminhos:

* A raiz da conta data Lake Storage Gen1.  Por exemplo: adl://mydatalakestore/.
* A pasta com todas as pastas de clusters.  Por exemplo: adl://mydatalakestore/clusters.
* A pasta do cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para obter mais informações sobre a criação de serviço principal e conceder acesso, consulte o acesso ao Armazenamento do Lago de Dados configurado.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrair um certificado do Azure Keyvault para utilização na criação de clusters

Se pretender configurar o Azure Data Lake Storage Gen1 como o seu armazenamento padrão para um novo cluster e o certificado para o seu diretor de serviço está armazenado no Cofre de Chaves Azure, existem alguns passos adicionais necessários para converter o certificado no formato correto. Os seguintes fragmentos de código mostram como realizar a conversão.

Primeiro, descarregue o certificado do Key Vault e extraia o `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Em seguida, converter o `SecretValueText` para um certificado.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Em seguida, pode usar o `$identityCertificate` para implantar um novo cluster como no seguinte corte:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Use data Lake Storage Gen1 como armazenamento adicional

Também pode utilizar o Data Lake Storage Gen1 como armazenamento adicional para o cluster. Nesses casos, o armazenamento por padrão do cluster pode ser uma Blob de Armazenamento Azure ou uma conta de Armazenamento de Data Lake. Se estiver a executar trabalhos HDInsight contra os dados armazenados no Armazenamento de Data Lake como armazenamento adicional, deve utilizar o caminho totalmente qualificado para os ficheiros. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Tenha em atenção que agora não existe nenhum **cluster_root_path** no URL. Isso porque o Armazenamento do Lago de Dados não é um armazenamento padrão neste caso, por isso tudo o que precisa fazer é fornecer o caminho para os ficheiros.

Para poder utilizar um Data Lake Storage Gen1 como armazenamento adicional, basta conceder ao serviço o acesso principal aos caminhos onde os seus ficheiros estão armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para obter mais informações sobre a criação de serviço principal e conceder acesso, consulte o acesso ao Armazenamento do Lago de Dados configurado.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Use mais de uma conta de armazenamento de data lake

Adicionando uma conta de Armazenamento de Data Lake como adicional e adicionando mais de uma contas de Armazenamento de Data Lake são realizadas dando ao cluster HDInsight permissão em dados em mais contas de armazenamento de data lake. Consulte o acesso ao Armazenamento do Lago de Dados de Configure.

## <a name="configure-data-lake-storage-access"></a>Configure data acesso ao armazenamento do lago de dados

Para configurar o acesso ao Armazenamento de Data Lake do seu cluster HDInsight, deve ter um diretor de serviço de diretório Azure Ative (Azure AD). Apenas um administrador do Azure AD pode criar um principal de serviço. O principal de serviço tem de ser criado com um certificado. Para obter mais informações, veja [Início Rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) e [Criar um principal de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se vai utilizar o Azure Data Lake Storage Gen1 como armazenamento adicional para o cluster HDInsight, recomendamos vivamente que o faça enquanto cria o cluster como descrito neste artigo. Adicionar o Azure Data Lake Storage Gen1 como armazenamento adicional a um cluster HDInsight existente não é um cenário suportado.

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros no Armazenamento de Data Lake a partir de um cluster HDInsight.

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

Exemplos são baseados numa [ligação ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó da cabeça do cluster. Os exemplos usam todos os três esquemas de URI. Substitua `DATALAKEACCOUNT` e `CLUSTERNAME` os valores relevantes.

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

Três locais de arquivo são mostrados para fins ilustrativos. Para a execução real, utilize apenas uma das entradas `LOCATION`.

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

## <a name="identify-storage-path-from-ambari"></a>Identifique o caminho de armazenamento de Ambari

Para identificar o caminho completo para a loja predefinida configurada, navegue para **o HDFS** > **Configs** e introduza `fs.defaultFS` na caixa de entrada do filtro.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Criar clusters HDInsight com acesso a Data Lake Storage Gen1

Utilize os seguintes links para instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Storage Gen1.

* [Utilizar o Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Utilização da PowerShell (com data Lake Storage Gen1 como armazenamento predefinido)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Utilização do PowerShell (com Data Lake Storage Gen1 como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Utilizar modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Atualizar o certificado HDInsight para acesso a Data Lake Storage Gen1

O seguinte exemplo O código PowerShell lê um certificado de um ficheiro local ou cofre de chaves Azure, e atualiza o seu cluster HDInsight com o novo certificado para aceder ao Azure Data Lake Storage Gen1. Forneça o seu próprio nome de cluster HDInsight, nome de grupo de recursos, ID de subscrição, ID de aplicação, caminho local para o certificado. Digite a palavra-passe quando solicitado.

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

Neste artigo, aprendeu a usar o Azure Data Lake Storage Gen1 compatível com hDFS com hDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Começar com Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Crie um cluster HDInsight para usar data Lake Storage Gen1 usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Enviar dados para HDInsight](hdinsight-upload-data.md)
* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilize assinaturas de acesso partilhado de armazenamento Azure para restringir o acesso a dados com hDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Tutorial: Extrair, transformar e carregar dados usando consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
