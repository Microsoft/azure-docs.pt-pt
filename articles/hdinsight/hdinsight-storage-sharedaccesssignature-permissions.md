---
title: Restringir o acesso com assinaturas de acesso partilhado - Azure HDInsight
description: Saiba como utilizar assinaturas de acesso partilhado para restringir o acesso do HDInsight para dados armazenados em blobs de armazenamento do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409549"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Utilizar assinaturas de acesso partilhado do Azure Storage para restringir o acesso aos dados no HDInsight

HDInsight tem acesso total aos dados nas contas de armazenamento do Azure associadas ao cluster. Pode utilizar assinaturas de acesso partilhado no contentor de BLOBs para restringir o acesso aos dados. Assinaturas de acesso partilhado (SAS) são uma funcionalidade de contas de armazenamento do Azure que permite-lhe limitar o acesso a dados. Por exemplo, fornecendo acesso só de leitura aos dados.

> [!IMPORTANT]  
> Para uma solução com o Apache Ranger, considere utilizar o HDInsight associado a um domínio. Para obter mais informações, consulte a [configurar o HDInsight associado a um domínio](./domain-joined/apache-domain-joined-configure.md) documento.

> [!WARNING]  
> HDInsight tem de ter acesso total para o armazenamento predefinido para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Existente [contentor de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Se utilizar o PowerShell, terá do [módulo Az](https://docs.microsoft.com/powershell/azure/overview).

* Se o que pretendem utilizar o CLI do Azure e ainda não instalou-lo, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Se utilizar [Python](https://www.python.org/downloads/), versão 2.7 ou superior.

* Se utilizar o C#, Visual Studio tem de ser a versão 2013 ou superior.

* O [esquema de URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) para a sua conta de armazenamento. Isso seria `wasb://` armazenamento do Azure, `abfs://` para a geração 2 de armazenamento do Azure Data Lake ou `adl://` para geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento do Azure ou de geração 2 de armazenamento do Data Lake, o URI seria `wasbs://` ou `abfss://`, respetivamente, também, consulte [transferência segura](../storage/common/storage-require-secure-transfer.md).

* Um cluster do HDInsight existente para adicionar uma assinatura de acesso partilhado para. Caso contrário, pode utilizar o Azure PowerShell para criar um cluster e adicione uma assinatura de acesso partilhado durante a criação do cluster.

* O exemplo ficheiros a partir [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositório contém os seguintes itens:

  * Um projeto do Visual Studio que pode criar um contentor de armazenamento, a política armazenadas e a SAS para utilização com o HDInsight
  * Um script de Python que pode criar um contentor de armazenamento, a política armazenadas e a SAS para utilização com o HDInsight
  * Um script do PowerShell que pode criar um cluster do HDInsight e configurá-lo para utilizar a SAS. Uma versão atualizada é utilizada mais abaixo.
  * Um ficheiro de exemplo: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Assinaturas de Acesso Partilhado

Existem duas formas de assinaturas de acesso partilhado:

* Ad hoc: A hora de início, hora de expiração e as permissões para a SAS são todos especificadas no URI de SAS.

* Política de acesso armazenado: É definida uma política de acesso armazenadas num contêiner de recursos, como um contentor de Blobs. Uma política pode ser utilizada para gerir restrições para um ou mais assinaturas de acesso partilhado. Quando associa um SAS com uma política de acesso armazenado, a SAS herda as restrições - a hora de início, hora de expiração e permissões - definidas para a política de acesso armazenado.

A diferença entre as duas formas é importante para um cenário de chave: revogação. Uma SAS é um URL, para que qualquer pessoa que obtém a SAS pode utilizá-lo, independentemente do que o pedido para começar. Se uma SAS é publicada publicamente, ele pode ser usado por qualquer pessoa no mundo. Uma SAS que é distribuída é válida até que uma das quatro coisas acontece:

1. For atingida a hora de expiração especificada no SAS.

2. A hora de expiração especificada na política de acesso armazenado referenciada pela SAS foi atingida. Os seguintes cenários de fazer com que a hora de expiração a ser atingido:

    * O intervalo de tempo decorrido.
    * A política de acesso armazenado é modificada para ter um tempo de expiração no passado. Alterar a hora de expiração é uma forma de revogar a SAS.

3. A política de acesso armazenado referenciada pela SAS é eliminada, que é outra forma de revogar a SAS. Caso recrie a política de acesso armazenado com o mesmo nome, todos os tokens SAS para a política anterior são válidos (se não tiver passado o tempo de expiração no SAS). Se pretende revogar o SAS, certifique-se de que utilize um nome diferente, caso recrie a política de acesso com um tempo de expiração no futuro.

4. A chave da conta que utilizou para criar a SAS é regenerada. Regenerar a chave faz com que todas as aplicações que utilizam a chave de anterior para falha na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]  
> Um URI de assinatura de acesso partilhado é associado a chave da conta utilizada para criar a assinatura e o associados armazenados política de acesso (se houver). Não se for especificada nenhuma política de acesso armazenado, é a única forma de revogar uma assinatura de acesso partilhado alterar a chave de conta.

Recomendamos que sempre usar políticas de acesso armazenadas. Quando utilizar políticas de armazenado, pode revogar assinaturas ou expandir a data de expiração, conforme necessário. Os passos neste documento utiliza armazenados políticas de acesso para gerar a SAS.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Criar uma política armazenadas e SAS

Guarde o token SAS que é produzido no final de cada método. O token terá um aspeto semelhante ao seguinte:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Com o PowerShell

Substitua `RESOURCEGROUP`, `STORAGEACCOUNT`, e `STORAGECONTAINER` com os valores adequados para o contentor de armazenamento existente. Altere o diretório para `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` ou rever os `-File` parâmetro para conter o caminho absoluto para `Set-AzStorageblobcontent`. Introduza o seguinte comando do PowerShell:

```PowerShell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

A utilização de variáveis nesta secção se baseia num ambiente do Windows. Pequenas variações serão necessários para o bash ou em outros ambientes.

1. Substitua `STORAGEACCOUNT`, e `STORAGECONTAINER` com os valores adequados para o contentor de armazenamento existente.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Defina a chave primária obtida a uma variável para utilização posterior. Substitua `PRIMARYKEY` obtida com valor no passo anterior e, em seguida, introduza o comando abaixo:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Altere o diretório para `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` ou rever os `--file` parâmetro para conter o caminho absoluto para `az storage blob upload`. Execute os comandos restantes:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Utilizar o Python

Abra o `SASToken.py` do ficheiro e substitua `storage_account_name`, `storage_account_key`, e `storage_container_name` com os valores adequados para o seu contentor de armazenamento existente e, em seguida, execute o script.

Poderá ter de executar `pip install --upgrade azure-storage` se receber a mensagem de erro `ImportError: No module named azure.storage`.

### <a name="using-c"></a>Utilizar C#

1. Abra a solução no Visual Studio.

2. No Explorador de soluções, faça duplo clique sobre o **SASExample** do projeto e selecione **propriedades**.

3. Selecione **definições** e adicione valores para as seguintes entradas:

   * StorageConnectionString: A cadeia de ligação para a conta de armazenamento que pretende criar uma política armazenadas e a SAS para. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` em que `myaccount` é o nome da conta de armazenamento e `mykey` é a chave para a conta de armazenamento.

   * ContainerName: O contentor na conta de armazenamento que pretende restringir o acesso ao.

   * SASPolicyName: O nome a utilizar para a política armazenada para criar.

   * FileToUpload: O caminho para um ficheiro que é carregado para o contentor.

4. Execute o projeto. Guarde o token de política SAS, o nome da conta de armazenamento e o nome do contentor. Estes valores são utilizados ao associar a conta de armazenamento com o seu cluster do HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Utilizar a SAS com o HDInsight

Ao criar um cluster do HDInsight, tem de especificar uma conta de armazenamento primário e, opcionalmente, pode especificar contas de armazenamento adicionais. Ambos os métodos de adicionar armazenamento requerem acesso total para as contas de armazenamento e os contentores que são utilizados.

Para utilizar uma assinatura de acesso partilhado para limitar o acesso a um contentor, adicione uma entrada personalizada para o **core-site** configuração do cluster. Pode adicionar a entrada durante a criação do cluster com o PowerShell ou após a criação de cluster com o Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que utiliza a SAS

Substitua `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, e `TOKEN` com os valores apropriados. Introduza os comandos do PowerShell:

```powershell

$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey


# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext 

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig 

$config = $config | Add-AzHDInsightConfigValues `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Quando lhe for pedido para o HTTP/s ou o nome de utilizador SSH e a palavra-passe, tem de fornecer uma palavra-passe que cumpra os seguintes critérios:
>
> * Tem de ser no mínimo 10 carateres de comprimento.
> * Tem de conter, pelo menos, um dígito.
> * Tem de conter pelo menos um caráter não alfanumérico.
> * Tem de conter pelo menos um canto superior ou uma letra minúscula.

Demora algum tempo para que este script concluir, normalmente, cerca de 15 minutos. Quando o script for concluído sem erros, o cluster ter sido criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Utilizar a SAS com um cluster existente

Se tiver um cluster existente, pode adicionar a SAS para o **core-site** configuração ao utilizar os seguintes passos:

1. Abra a IU web do Ambari para o seu cluster. O endereço para esta página é `https://YOURCLUSTERNAME.azurehdinsight.net`. Quando lhe for pedido, autenticar-se ao cluster com o nome de administrador (administrador) e a palavra-passe que utilizou quando criou o cluster.

2. Do lado esquerdo da Ambari web UI, selecione **HDFS** e, em seguida, selecione a **configurações** separador no meio da página.

3. Selecione o **avançadas** separador e, em seguida, desloque-se até encontrar o **personalizado core-site** secção.

4. Expanda a **core-site personalizado** secção, em seguida, desloque-se para o final e selecione o **Adicionar propriedade...**  ligação. Utilize os seguintes valores para o **chave** e **valor** campos:

   * **chave**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Valor**: A SAS retornada por um dos métodos executados anteriormente.

     Substitua `CONTAINERNAME` com o contentor de nome utilizado com o C# ou a aplicação de SAS. Substitua `STORAGEACCOUNTNAME` com o nome de conta de armazenamento que utilizou.

5. Clique nas **Add** botão para guardar esta chave e valor, em seguida, clique nas **guardar** botão para guardar as alterações de configuração. Quando lhe for pedido, adicione uma descrição da alteração ("adicionar acesso do armazenamento SAS" por exemplo) e, em seguida, clique em **guardar**.

    Clique em **OK** quando as alterações foram concluídas.

   > [!IMPORTANT]  
   > Tem de reiniciar vários serviços para a alteração tem efeito.

6. Na IU web do Ambari, selecione **HDFS** da lista à esquerda e, em seguida, selecione **reiniciar todos os afetados** do **ações de serviço** lista pendente lista à direita. Quando lhe for pedido, selecione __confirmar reiniciar todos os__.

    Repita este processo para MapReduce2 e YARN.

7. Depois dos serviços de ser reiniciado, selecione cada um deles e desativar o modo de manutenção do **ações de serviço** menu pendente.

## <a name="test-restricted-access"></a>Testar o acesso restrito

Utilize os seguintes passos para verificar que o utilizador pode apenas leitura e a lista de itens na conta de armazenamento SAS.

1. Ligar ao cluster. Substitua `CLUSTERNAME` com o nome do cluster e introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para listar o conteúdo do contentor, utilize o seguinte comando da linha de comandos:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua `SASCONTAINER` com o nome do contentor criado para a conta de armazenamento SAS. Substitua `SASACCOUNTNAME` com o nome da conta de armazenamento utilizado para a SAS.

    A lista inclui o ficheiro carregado quando o contentor e a SAS foram criada.

3. Utilize o seguinte comando para verificar o que pode ler o conteúdo do ficheiro. Substitua a `SASCONTAINER` e `SASACCOUNTNAME` como no passo anterior. Substitua `sample.log` com o nome do ficheiro apresentado no comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Este comando lista o conteúdo do ficheiro.

4. Utilize o seguinte comando para transferir o ficheiro para o sistema de arquivos local:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Este comando transfere o ficheiro para um ficheiro local com o nome **testfile.txt**.

5. Utilize o seguinte comando para carregar o ficheiro local para um novo ficheiro designado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Receber uma mensagem semelhante ao seguinte texto:

        put: java.io.IOException

    Este erro ocorre porque a localização de armazenamento é leitura + lista apenas. Utilize o seguinte comando para colocar os dados no armazenamento padrão para o cluster, o que é gravável:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Desta vez, a operação deverá ser concluída com êxito.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como adicionar armazenamento de acesso limitado ao seu cluster do HDInsight, saiba outras formas de trabalhar com dados no seu cluster:

* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

