---
title: Restringir o acesso usando assinaturas de acesso partilhado - Azure HDInsight
description: Saiba como usar assinaturas de acesso partilhado para restringir o acesso ao HDInsight aos dados armazenados em blobs de armazenamento Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: d68f7dc6368c2b3de7f26f2946c5fb47237a820d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313937"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Utilizar Assinaturas de Acesso Partilhado do Armazenamento do Azure para restringir o acesso aos dados no HDInsight

O HDInsight tem acesso total aos dados nas contas de Armazenamento Azure associadas ao cluster. Pode utilizar assinaturas de acesso partilhado no recipiente blob para restringir o acesso aos dados. As Assinaturas de Acesso Partilhado (SAS) são uma característica das contas de armazenamento do Azure que lhe permite limitar o acesso aos dados. Por exemplo, fornecer acesso apenas a leitura a dados.

> [!IMPORTANT]  
> Para uma solução utilizando o Apache Ranger, considere utilizar o HDInsight de domínio. Para mais informações, consulte o documento [HDInsight unido ao domínio Configure.](./domain-joined/apache-domain-joined-configure.md)

> [!WARNING]  
> O HDInsight deve ter acesso total ao armazenamento predefinido para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Um recipiente de [armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)existente.  

* Se utilizar o PowerShell, necessitará do [Módulo Az](https://docs.microsoft.com/powershell/azure/overview).

* Se pretender utilizar o Azure CLI e ainda não o tiver instalado, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Se utilizar [python](https://www.python.org/downloads/), versão 2.7 ou superior.

* Se utilizar c#, o Visual Studio deve ser a versão 2013 ou superior.

* O [esquema URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) para a sua conta de armazenamento. Este esquema `wasb://` seria para o `abfs://` Armazenamento Azure, para `adl://` o Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Armazenamento Azure, o URI seria . Ver também, [transferência segura.](../storage/common/storage-require-secure-transfer.md)

* Um cluster HDInsight existente para adicionar uma Assinatura de Acesso Partilhado. Caso contrário, pode utilizar o Azure PowerShell para criar um cluster e adicionar uma Assinatura de Acesso Partilhado durante a criação do cluster.

* Os ficheiros [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)exemplo de . Este repositório contém os seguintes itens:

  * Um projeto do Estúdio Visual que pode criar um recipiente de armazenamento, política armazenada e SAS para uso com HDInsight
  * Um script Python que pode criar um recipiente de armazenamento, política armazenada e SAS para uso com HDInsight
  * Um script PowerShell que pode criar um cluster HDInsight e configurá-lo para usar o SAS. Uma versão atualizada é usada mais abaixo.
  * Um ficheiro de amostra:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Assinaturas de Acesso Partilhado

Existem duas formas de Assinaturas de Acesso Partilhado:

* `Ad hoc`: O tempo de início, o tempo de validade e as permissões para o SAS são todos especificados no SAS URI.

* `Stored access policy`: Uma política de acesso armazenada é definida num recipiente de recursos, como um recipiente de bolhas. Uma política pode ser utilizada para gerir constrangimentos para uma ou mais assinaturas de acesso partilhado. Quando associa um SAS a uma política de acesso armazenada, o SAS herda os constrangimentos - o tempo de início, o tempo de validade e as permissões - definidos para a política de acesso armazenada.

A diferença entre as duas formas é importante para um cenário-chave: a revogação. Um SAS é um URL, para que qualquer pessoa que obtenha o SAS pode usá-lo. Não importa quem o pediu para começar. Se um SAS for publicado publicamente, pode ser usado por qualquer pessoa no mundo. Um SAS que é distribuído é válido até que uma de quatro coisas aconteça:

1. O prazo de validade especificado no SAS é atingido.

2. O prazo de validade especificado na política de acesso armazenada referenciada pelo SAS é atingido. Os seguintes cenários fazem com que o prazo de validade seja atingido:

    * O intervalo de tempo decorreu.
    * A política de acesso armazenada é modificada para ter um tempo de validade no passado. Alterar o tempo de validade é uma forma de revogar o SAS.

3. A política de acesso armazenada referenciada pelo SAS é suprimida, o que é outra forma de revogar o SAS. Se recriar a política de acesso armazenada com o mesmo nome, todas as fichas SAS para a política anterior são válidas (se o prazo de validade no SAS não tiver passado). Se pretender revogar o SAS, certifique-se de que utiliza um nome diferente se recriar a política de acesso com um prazo de validade no futuro.

4. A chave de conta que foi usada para criar o SAS é regenerada. Regenerar a chave faz com que todas as aplicações que utilizam a chave anterior falhem na autenticação. Atualize todos os componentes para a nova tecla.

> [!IMPORTANT]  
> Uma assinatura de acesso partilhado URI está associada à chave de conta utilizada para criar a assinatura, e à política de acesso armazenada associada (se houver). Se não for especificada a política de acesso armazenada, a única forma de revogar uma assinatura de acesso partilhado é alterar a chave da conta.

Recomendamos que utilize sempre políticas de acesso armazenadas. Ao utilizar as políticas armazenadas, pode revogar assinaturas ou prolongar a data de validade conforme necessário. Os passos neste documento utilizam políticas de acesso armazenadas para gerar SAS.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Criar uma política armazenada e SAS

Guarde o token SAS que é produzido no final de cada método. O símbolo será semelhante à seguinte saída:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Com o PowerShell

Substitua, `RESOURCEGROUP` `STORAGEACCOUNT` `STORAGECONTAINER` e com os valores adequados para o seu recipiente de armazenamento existente. Mude o `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` diretório para `-File` ou reveja o `Set-AzStorageblobcontent`parâmetro para conter o caminho absoluto para . Introduza o seguinte comando PowerShell:

```powershell
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

A utilização de variáveis nesta secção baseia-se num ambiente Windows. Serão necessárias ligeiras variações para a festa ou outros ambientes.

1. `STORAGEACCOUNT`Substitua, `STORAGECONTAINER` e com os valores adequados para o seu recipiente de armazenamento existente.

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

2. Desloque a chave primária recuperada para uma variável para posterior utilização. Substitua `PRIMARYKEY` pelo valor recuperado no passo anterior e, em seguida, introduza o comando abaixo:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Mude o `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` diretório para `--file` ou reveja o `az storage blob upload`parâmetro para conter o caminho absoluto para . Executar os restantes comandos:

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

Abra `SASToken.py` o ficheiro `storage_account_name` `storage_account_key`e `storage_container_name` substitua, e com os valores apropriados para o seu recipiente de armazenamento existente, e, em seguida, executar o script.

Poderá ter de `pip install --upgrade azure-storage` executar se receber `ImportError: No module named azure.storage`a mensagem de erro .

### <a name="using-c"></a>Usando C\#

1. Abra a solução no Estúdio Visual.

2. No Solution Explorer, clique à direita no projeto **SASExample** e selecione **Propriedades**.

3. Selecione **Definições** e adicione valores para as seguintes entradas:

    |Item |Descrição |
    |---|---|
    |Cadeia de conexão de armazenamento|A cadeia de ligação para a conta de armazenamento que pretende criar uma política armazenada e sas para. O formato `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` `myaccount` deve ser onde está `mykey` o nome da sua conta de armazenamento e é a chave para a conta de armazenamento.|
    |ContainerName|O recipiente na conta de armazenamento a que pretende restringir o acesso.|
    |SASPolicyName|O nome a usar para a política armazenada para criar.|
    |FileToUpload|O caminho para um ficheiro que é enviado para o recipiente.|

4. Executar o projeto. Guarde o símbolo da política SAS, o nome da conta de armazenamento e o nome do contentor. Estes valores são utilizados ao associar a conta de armazenamento ao seu cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Utilize o SAS com HDInsight

Ao criar um cluster HDInsight, deve especificar uma conta de armazenamento primária. Também pode especificar contas de armazenamento adicionais. Ambos os métodos de adição de armazenamento requerem acesso total às contas de armazenamento e aos contentores utilizados.

Utilize uma Assinatura de Acesso Partilhado para limitar o acesso ao contentor. Adicione uma entrada personalizada na configuração do **núcleo para** o cluster. Pode adicionar a entrada durante a criação de cluster utilizando powerShell ou após a criação de cluster usando Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que utilize o SAS

`RESOURCEGROUP`Substitua, `DEFAULTSTORAGEACCOUNT` `CLUSTERNAME` `STORAGECONTAINER`, `STORAGEACCOUNT`, `TOKEN` e com os valores apropriados. Introduza os comandos PowerShell:

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

$config = $config | Add-AzHDInsightConfigValue `
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
> Quando solicitado o nome e a senha de utilizador HTTP/s ou SSH, deve fornecer uma palavra-passe que satisfaça os seguintes critérios:
>
> * Deve ter pelo menos 10 caracteres de comprimento.
> * Deve conter pelo menos um dígito.
> * Deve conter pelo menos um carácter não alfanumérico.
> * Deve conter pelo menos uma letra maiúscula ou minúscula.

Demora um pouco para que este guião esteja concluído, normalmente cerca de 15 minutos. Quando o script completa sem erros, o cluster foi criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Utilize o SAS com um cluster existente

Se tiver um cluster existente, pode adicionar o SAS à configuração **do núcleo do site** utilizando os seguintes passos:

1. Abra a UI web ambari para o seu cluster. O endereço para `https://YOURCLUSTERNAME.azurehdinsight.net`esta página é . Quando solicitado, autentica-se no cluster utilizando o nome de administrador (administrador) e a palavra-passe que usou ao criar o cluster.

1. Navegue para **HDFS** > **Configs** > **Avançado** > Custom**core-site**.

1. Expanda a secção **de core-site personalizado,** percorra até ao fim e, em seguida, selecione **adicionar propriedade...**. Utilize os seguintes valores para **chave** e **valor:**

    * **Chave:**`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Valor**: O SAS devolvido por um dos métodos anteriormente executados.

    Substitua-a `CONTAINERNAME` com o nome do recipiente utilizado com a aplicação C# ou SAS. Substitua `STORAGEACCOUNTNAME` pelo nome da conta de armazenamento que usou.

    Selecione **Adicionar** para guardar esta chave e valor

1. Selecione o botão **Guardar** para evitar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionar acesso ao armazenamento SAS" por exemplo) e, em seguida, **selecione Guardar**.

    Selecione **OK** quando as alterações estiverem concluídas.

   > [!IMPORTANT]  
   > Deve reiniciar vários serviços antes que a mudança entre em vigor.

1. Aparecerá uma lista de **reinício.** Selecione **Reiniciar Todos os afetados** a partir da lista de lançamentos __e,__ em seguida, confirmar reiniciar tudo .

    Repita este processo para **MapReduce2** e **YARN**.

1. Uma vez reiniciados os serviços, selecione cada um deles e desative o modo de manutenção das Ações de **Serviço.**

## <a name="test-restricted-access"></a>Teste de acesso restrito

Utilize os seguintes passos para verificar se só pode ler e listar itens na conta de armazenamento SAS.

1. Ligue-se ao aglomerado. Substitua-o `CLUSTERNAME` pelo nome do seu cluster e introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para listar o conteúdo do recipiente, utilize o seguinte comando a partir do pedido:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua `SASCONTAINER` pelo nome do recipiente criado para a conta de armazenamento SAS. Substitua `SASACCOUNTNAME` pelo nome da conta de armazenamento utilizada para o SAS.

    A lista inclui o ficheiro carregado quando o contentor e o SAS foram criados.

3. Utilize o seguinte comando para verificar se pode ler o conteúdo do ficheiro. Substitua `SASCONTAINER` `SASACCOUNTNAME` o e como no passo anterior. Substitua `sample.log` pelo nome do ficheiro apresentado no comando anterior:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Este comando lista o conteúdo do ficheiro.

4. Utilize o seguinte comando para transferir o ficheiro para o sistema de ficheiros local:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Este comando transfere o ficheiro para um ficheiro local chamado **testfile.txt**.

5. Utilize o seguinte comando para fazer o upload do ficheiro local para um novo ficheiro chamado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Recebe uma mensagem semelhante ao seguinte texto:

        put: java.io.IOException

    Este erro ocorre porque o local de armazenamento é apenas ler+lista. Utilize o seguinte comando para colocar os dados no armazenamento predefinido para o cluster, que é repreensível:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Desta vez, a operação deve ser concluída com sucesso.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a adicionar armazenamento de acesso limitado ao seu cluster HDInsight, aprenda outras formas de trabalhar com dados no seu cluster:

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hadoop/hdinsight-use-mapreduce.md)