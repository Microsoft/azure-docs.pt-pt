---
title: Use data lake storage Gen1 com Hadoop em Azure HDInsight
description: Saiba como consultar os dados da Azure Data Lake Storage Gen1 e armazenar os resultados da sua análise.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 35941f585a0ae5c0d3915c769db5b18737b299f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945407"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Use data lake storage Gen1 com clusters Azure HDInsight

> [!Note]
> Implemente novos clusters HDInsight utilizando [a Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) para melhorar o desempenho e novas funcionalidades.

Para analisar dados no cluster HDInsight, pode armazenar os dados em [`Azure Blob storage`](../storage/common/storage-introduction.md) , [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md), ou [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Todas as opções de armazenamento permitem eliminar com segurança os clusters HDInsight que são utilizados para a computação sem perder os dados do utilizador.

Neste artigo, você aprende como data lake storage gen1 funciona com clusters HDInsight. Para saber como funciona o armazenamento Azure Blob com clusters HDInsight, consulte [o armazenamento Use Azure Blob com clusters Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre a criação de um cluster HDInsight, consulte [crie clusters Apache Hadoop em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 é sempre acedido através de um canal seguro, por isso não há `adls` nenhum nome de esquema de sistema de ficheiros. Utiliza sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para clusters HDInsight

Apache Hadoop suporta uma noção do sistema de ficheiros predefinido. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, especifique um recipiente blob no Azure Storage como o sistema de ficheiros predefinido. Ou com as versões HDInsight 3.5 e mais recentes, pode selecionar o armazenamento Azure Blob ou o Azure Data Lake Storage Gen1 como o sistema de ficheiros predefinidos com algumas exceções. O cluster e a conta do Storage têm de estar alojados na mesma região.

Os clusters HDInsight podem usar data lake storage gen1 de duas maneiras:

* Como armazenamento predefinido
* Como armazenamento adicional, com o armazenamento Azure Blob como armazenamento predefinido.

Atualmente, apenas alguns dos tipos/versões de cluster HDInsight suportam usando a Data Lake Storage Gen1 como armazenamento predefinido e contas de armazenamento adicionais:

| Tipo de cluster do HDInsight | Data Lake Storage Gen1 como armazenamento padrão | Data Lake Storage Gen1 como armazenamento adicional| Notas |
|------------------------|------------------------------------|---------------------------------------|------|
| Versão HDInsight 4.0 | No | No |A ADLS Gen1 não é suportado com HDInsight 4.0 |
| HDInsight versão 3.6 | Yes | Yes | Exceto HBase|
| HDInsight versão 3.5 | Yes | Yes | Exceto HBase|
| HDInsight versão 3.4 | No | Yes | |
| HDInsight versão 3.3 | No | No | |
| HDInsight versão 3.2 | No | Yes | |
| Storm | | |Você pode usar Data Lake Storage Gen1 para escrever dados de uma topologia storm. Também pode usar data lake storage gen1 para dados de referência que podem ser lidos por uma topologia storm.|

> [!WARNING]  
> HDInsight HBase não é suportado com Azure Data Lake Storage Gen1

Usar a Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho. Ou a capacidade de ler ou escrever para o armazenamento de bolhas Azure do cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Use data lake storage Gen1 como armazenamento padrão

Quando o HDInsight é implementado com o Data Lake Storage Gen1 como armazenamento predefinido, os ficheiros relacionados com o cluster são armazenados em `adl://mydatalakestore/<cluster_root_path>/` , onde é o nome de uma pasta que cria no Armazenamento do Lago de `<cluster_root_path>` Dados. Ao especificar um caminho de raiz para cada cluster, você pode usar a mesma conta de Armazenamento do Lago de Dados para mais de um cluster. Por isso, pode ter uma configuração em que:

* Cluster1 pode utilizar o caminho `adl://mydatalakestore/cluster1storage`
* Cluster2 pode utilizar o caminho `adl://mydatalakestore/cluster2storage`

Note que ambos os clusters usam a mesma conta de armazenamento de dados Da Gen1 **mydatalakestore**. Cada cluster tem acesso ao seu próprio sistema de ficheiros raiz no Armazenamento do Lago de Dados. A experiência de implantação do portal Azure leva-o a utilizar um nome de pasta como **/clusters/ \<clustername>** para o caminho da raiz.

Para utilizar a Data Lake Storage Gen1 como armazenamento predefinido, deve conceder ao serviço acesso principal aos seguintes caminhos:

* A raiz da conta de armazenamento de dados da Gen1.  Por exemplo: adl://mydatalakestore/.
* A pasta com todas as pastas de clusters.  Por exemplo: adl://mydatalakestore/clusters.
* A pasta do cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para obter mais informações para criar o principal do serviço e conceder acesso, consulte o acesso ao Armazenamento do Lago de Dados Configure.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extração de certificado da Azure Keyvault para utilização na criação de clusters

Se o certificado para o seu principal de serviço estiver armazenado no Cofre da Chave Azure, deve converter o certificado no formato correto. Os seguintes fragmentos de código mostram como fazer a conversão.

Primeiro, descarregue o certificado do Key Vault e extraia o `SecretValueText` .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Em seguida, converta o `SecretValueText` certificado.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Em seguida, pode usar `$identityCertificate` o para implantar um novo cluster como no seguinte corte:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Use data lake storage Gen1 como armazenamento adicional

Você pode usar data lake storage Gen1 como armazenamento adicional para o cluster também. Nesses casos, o armazenamento por defeito do cluster pode ser um armazenamento Azure Blob ou uma conta Azure Data Lake Storage Gen1. Ao executar trabalhos HDInsight contra os dados armazenados na Azure Data Lake Storage Gen1 como armazenamento adicional, utilize o caminho totalmente qualificado. Por exemplo:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Não há **cluster_root_path** na URL agora. Isso é porque o Armazenamento do Lago de Dados não é um armazenamento padrão neste caso. Então, tudo o que precisa fazer é fornecer o caminho para os ficheiros.

Para utilizar uma Gen1 de Armazenamento de Data Lake como armazenamento adicional, conceda ao serviço acesso principal aos caminhos onde os seus ficheiros estão armazenados.  Por exemplo:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Para obter mais informações para criar o principal do serviço e conceder acesso, consulte o acesso ao Armazenamento do Lago de Dados Configure.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Use mais de uma conta de Data Lake Storage Gen1

Adicionar uma conta de Armazenamento do Lago de Dados como adicional e adicionar mais de uma conta de Armazenamento do Lago de Dados pode ser feito. Dê ao cluster HDInsight permissão sobre dados em uma ou mais contas de Armazenamento de Data Lake. Consulte o acesso à Gen1 de armazenamento de dados configurar dados abaixo.

## <a name="configure-data-lake-storage-gen1-access"></a>Configure data lake storage gen1 acesso

Para configurar o acesso da Azure Data Lake Storage Gen1 a partir do seu cluster HDInsight, tem de ter um diretório Azure Ative (Azure AD). Apenas um administrador do Azure AD pode criar um principal de serviço. O principal de serviço tem de ser criado com um certificado. Para obter mais informações, veja [Início Rápido: Configurar clusters no HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) e [Criar um principal de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se você vai usar Azure Data Lake Storage Gen1 como armazenamento adicional para cluster HDInsight, recomendamos vivamente que você faça isso enquanto cria o cluster como descrito neste artigo. Adicionar Azure Data Lake Storage Gen1 como armazenamento adicional a um cluster HDInsight existente não é um cenário suportado.

Para obter mais informações sobre o modelo de controlo de acesso, consulte o [controlo de acesso na Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros no Armazenamento do Lago de Dados a partir de um cluster HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Utilizando o formato de caminho abreviado**. Com esta abordagem, você substitui o caminho até a raiz do cluster com:

    ```
    adl:///<file path>
    ```

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos baseiam-se numa [ligação ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó da cabeça do cluster. Os exemplos utilizam os três esquemas URI. Substitua `DATALAKEACCOUNT` e `CLUSTERNAME` pelos valores relevantes.

#### <a name="a-few-hdfs-commands"></a>Alguns comandos hdfs

1. Crie um arquivo no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de clusters.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie os dados do armazenamento local para o armazenamento de clusters.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Listar conteúdos de diretório no armazenamento de clusters.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criação de uma mesa de colmeia

Três localizações de ficheiros são mostradas para fins ilustrativos. Para execução real, utilize apenas uma das `LOCATION` entradas.

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

Para identificar o caminho completo para a loja padrão configurada, navegue até **HDFS**  >  **Configs** e introduza `fs.defaultFS` na caixa de entrada do filtro.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Criar clusters HDInsight com acesso a Data Lake Storage Gen1

Utilize os seguintes links para instruções detalhadas sobre como criar clusters HDInsight com acesso à Data Lake Storage Gen1.

* [Utilizar o portal](./hdinsight-hadoop-provision-linux-clusters.md)
* [Utilizando o PowerShell (com data lake storage Gen1 como armazenamento padrão)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Utilizando o PowerShell (com data lake storage gen1 como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Utilizar modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Atualizar o certificado do HDInsight para acesso ao Data Lake Storage Gen1

O seguinte exemplo O código PowerShell lê um certificado de um ficheiro local ou Azure Key Vault, e atualiza o seu cluster HDInsight com o novo certificado para aceder ao Azure Data Lake Storage Gen1. Forneça o seu próprio nome de cluster HDInsight, nome de grupo de recursos, ID de `app ID` assinatura, caminho local para o certificado. Digite a palavra-passe quando solicitado.

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

Neste artigo, você aprendeu a usar Azure Data Lake Storage Gen1 compatível com HDFS com HDInsight. Este armazenamento permite-lhe construir soluções de aquisição de dados adaptáveis, a longo prazo, arquivando soluções de aquisição de dados. E use o HDInsight para desbloquear a informação dentro dos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Início rápido: Configurar clusters no HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
* [Crie um cluster HDInsight para utilizar a Data Lake Storage Gen1 utilizando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Upload data to HDInsight (Carregar dados para o HDInsight)](hdinsight-upload-data.md)
* [Use Azure Blob armazenamento Assinaturas de acesso compartilhados para restringir o acesso a dados com HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)