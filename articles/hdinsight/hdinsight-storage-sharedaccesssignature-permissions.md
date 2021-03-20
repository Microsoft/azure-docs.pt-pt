---
title: Restringir o acesso usando assinaturas de acesso compartilhado - Azure HDInsight
description: Saiba como utilizar assinaturas de acesso partilhado para restringir o acesso hdInsight aos dados armazenados no armazenamento de Azure Blob.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 647ac363dd81604f0a5a1d750d98a3349beb19c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931724"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Use Azure Blob armazenamento Assinaturas de acesso compartilhados para restringir o acesso a dados em HDInsight

O HDInsight tem acesso total aos dados nas contas de armazenamento Azure Blob associadas ao cluster. Pode utilizar assinaturas de acesso partilhado no recipiente blob para restringir o acesso aos dados. As Assinaturas de Acesso Partilhado (SAS) são uma funcionalidade das contas de armazenamento Azure Blob que permite limitar o acesso aos dados. Por exemplo, fornecer acesso apenas de leitura aos dados.

> [!IMPORTANT]  
> Para obter uma solução utilizando o Apache Ranger, considere utilizar o HDInsight de domínio. Para obter mais informações, consulte o documento [HDInsight de domínio de Configuração.](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!WARNING]  
> O HDInsight deve ter acesso total ao armazenamento predefinido para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Um recipiente de [armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)existente.  

* Se utilizar o PowerShell, necessitará do [Módulo Az](/powershell/azure/).

* Se quiser utilizar o Azure CLI e ainda não o instalou, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

* Se utilizar [Python](https://www.python.org/downloads/), versão 2.7 ou superior.

* Se utilizar C#, o Visual Studio deve ser versão 2013 ou superior.

* O esquema URI para a sua conta de armazenamento. Este esquema seria `wasb://` para o armazenamento de Azure Blob, para a `abfs://` Azure Data Lake Storage Gen2 ou `adl://` para a Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o armazenamento do Azure Blob, o URI será `wasbs://` .

* Um cluster HDInsight existente para adicionar uma Assinatura de Acesso Partilhado. Caso contrário, pode utilizar o Azure PowerShell para criar um cluster e adicionar uma Assinatura de Acesso Partilhado durante a criação do cluster.

* Os ficheiros de exemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . Este repositório contém os seguintes itens:

  * Um projeto visual studio que pode criar um recipiente de armazenamento, política armazenada e SAS para uso com HDInsight
  * Um script Python que pode criar um recipiente de armazenamento, política armazenada e SAS para uso com HDInsight
  * Um script PowerShell que pode criar um cluster HDInsight e configurá-lo para usar o SAS. Uma versão atualizada é usada mais abaixo.
  * Um ficheiro de amostra: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Assinaturas de Acesso Partilhado

Existem duas formas de Assinaturas de Acesso Partilhado:

* `Ad hoc`: A hora de início, o prazo de validade e as permissões para o SAS estão todas especificadas no SAS URI.

* `Stored access policy`: Uma política de acesso armazenada é definida num recipiente de recursos, como um recipiente de bolhas. Uma política pode ser usada para gerir constrangimentos para uma ou mais assinaturas de acesso partilhadas. Quando associa um SAS a uma política de acesso armazenada, o SAS herda os constrangimentos - a hora de início, o tempo de validade e permissões - definidos para a política de acesso armazenada.

A diferença entre as duas formas é importante para um cenário-chave: a revogação. Um SAS é um URL, para que qualquer pessoa que obtenha o SAS pode usá-lo. Não importa quem pediu para começar. Se um SAS for publicado publicamente, pode ser usado por qualquer pessoa no mundo. Um SAS que é distribuído é válido até que uma das quatro coisas aconteça:

1. O prazo de validade especificado no SAS é atingido.

2. O prazo de validade especificado na política de acesso armazenada referenciada pelo SAS é atingido. Os seguintes cenários fazem com que o prazo de validade seja atingido:

    * O intervalo de tempo passou.
    * A política de acesso armazenada é modificada para ter um prazo de validade no passado. Alterar o prazo de validade é uma forma de revogar o SAS.

3. A política de acesso armazenada referenciada pelo SAS é eliminada, o que é outra forma de revogar o SAS. Se recriar a política de acesso armazenada com o mesmo nome, todos os tokens SAS para a apólice anterior são válidos (se o prazo de validade do SAS não tiver passado). Se pretende renunciar ao SAS, não se esqueça de usar um nome diferente se recriar a política de acesso com um prazo de validade no futuro.

4. A chave de conta que foi usada para criar o SAS é regenerada. A regeneração da chave faz com que todas as aplicações que utilizem a chave anterior falhem na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]  
> Uma assinatura de acesso partilhado URI está associada à chave de conta utilizada para criar a assinatura, e à política de acesso armazenada associada (se houver). Se não for especificada nenhuma política de acesso armazenada, a única forma de revogar uma assinatura de acesso partilhado é alterar a chave da conta.

Recomendamos que utilize sempre as políticas de acesso armazenadas. Ao utilizar as políticas armazenadas, pode revogar as assinaturas ou prolongar a data de validade, conforme necessário. Os passos neste documento utilizam políticas de acesso armazenadas para gerar SAS.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [compreender o modelo SAS.](../storage/common/storage-sas-overview.md)

## <a name="create-a-stored-policy-and-sas"></a>Criar uma política armazenada e SAS

Salve o token SAS que é produzido no final de cada método. O token será semelhante à seguinte saída:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Com o PowerShell

`RESOURCEGROUP` `STORAGEACCOUNT` Substitua, e pelos `STORAGECONTAINER` valores adequados para o seu recipiente de armazenamento existente. Altere o diretório para `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` ou reveja o `-File` parâmetro para conter o caminho absoluto para `Set-AzStorageblobcontent` . Introduza o seguinte comando PowerShell:

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

A utilização de variáveis nesta secção baseia-se num ambiente Windows. Serão necessárias ligeiras variações para a bash ou outros ambientes.

1. Substitua `STORAGEACCOUNT` , e `STORAGECONTAINER` pelos valores adequados para o seu recipiente de armazenamento existente.

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

2. Desa estale a chave primária recuperada para uma variável para posterior utilização. `PRIMARYKEY`Substitua-o pelo valor recuperado no passo anterior e, em seguida, introduza o comando abaixo:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Altere o diretório para `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` ou reveja o `--file` parâmetro para conter o caminho absoluto para `az storage blob upload` . Execute os restantes comandos:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Utilizar o Python

Abra o `SASToken.py` ficheiro e `storage_account_name` substitua, e com os `storage_account_key` `storage_container_name` valores adequados para o seu recipiente de armazenamento existente, e, em seguida, execute o script.

Poderá ter de ser executado `pip install --upgrade azure-storage` se receber a mensagem de erro `ImportError: No module named azure.storage` .

### <a name="using-c"></a>Usando C\#

1. Abra a solução no Visual Studio.

2. No Solution Explorer, clique com o botão direito no projeto **SASExample** e selecione **Propriedades**.

3. Selecione **Definições** e adicione valores para as seguintes entradas:

    |Item |Descrição |
    |---|---|
    |ArmazenamentoConnectionString|O fio de ligação para a conta de armazenamento para a que pretende criar uma política armazenada e SAS para. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde está o nome da sua conta de armazenamento e é a chave para a conta de `myaccount` `mykey` armazenamento.|
    |ContainerName|O recipiente na conta de armazenamento a que pretende restringir o acesso.|
    |SASPolicyName|O nome a usar para a política armazenada para criar.|
    |FileToUpload|O caminho para um ficheiro que é enviado para o contentor.|

4. Executar o projeto. Guarde o token da política SAS, o nome da conta de armazenamento e o nome do recipiente. Estes valores são utilizados ao associar a conta de armazenamento ao seu cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Use o SAS com HDInsight

Ao criar um cluster HDInsight, deve especificar uma conta de armazenamento primário. Também pode especificar contas de armazenamento adicionais. Ambos os métodos de adição de armazenamento requerem acesso total às contas de armazenamento e aos recipientes utilizados.

Utilize uma Assinatura de Acesso Partilhado para limitar o acesso ao contentor. Adicione uma entrada personalizada à configuração do **núcleo do site** para o cluster. Pode adicionar a entrada durante a criação do cluster usando PowerShell ou após a criação do cluster usando Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que usa o SAS

`CLUSTERNAME`Substitua, , , , , , e pelos `RESOURCEGROUP` `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` `STORAGEACCOUNT` `TOKEN` valores apropriados. Insira os comandos PowerShell:

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
> Quando solicitado para o nome de utilizador http/s ou SSH e palavra-passe, deve fornecer uma palavra-passe que satisfaça os seguintes critérios:
>
> * Deve ter pelo menos 10 caracteres de comprimento.
> * Deve conter pelo menos um dígito.
> * Deve conter pelo menos um carácter não alfanumérico.
> * Deve conter pelo menos uma letra maiúscula ou inferior.

Leva um tempo para que este script esteja completo, geralmente cerca de 15 minutos. Quando o script termina sem erros, o cluster foi criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Utilize o SAS com um cluster existente

Se tiver um cluster existente, pode adicionar o SAS à configuração do **núcleo** utilizando os seguintes passos:

1. Abra a UI web Ambari para o seu cluster. O endereço desta página é `https://YOURCLUSTERNAME.azurehdinsight.net` . Quando solicitado, autente para o cluster utilizando o nome de administração (administrador) e a palavra-passe que utilizou ao criar o cluster.

1. Navegue para **o núcleo** avançado de  >  **hdfs Configs.**  >    >  

1. Expandir a secção **de núcleo personalizado,** deslocar até ao fim e, em seguida, selecionar **Adicionar propriedade...**. Utilize os seguintes valores para **Chave** e **Valor:**

    * **Chave:**`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Valor**: O SAS devolvido por um dos métodos anteriormente executados.

    `CONTAINERNAME`Substitua-o pelo nome do recipiente que utilizou pela aplicação C# ou SAS. `STORAGEACCOUNTNAME`Substitua-o pelo nome da conta de armazenamento que utilizou.

    **Selecione Adicionar** para guardar esta chave e valor

1. Selecione o botão **Guardar** para guardar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionar acesso ao armazenamento SAS" por exemplo) e, em seguida, **selecione Save**.

    Selecione **OK** quando as alterações estiverem concluídas.

   > [!IMPORTANT]  
   > Deve reiniciar vários serviços antes que a alteração entre em vigor.

1. Aparecerá uma lista de recuos do **Restart.** Selecione **Reiniciar todos os afetados** da lista de drop-down e, em seguida, __confirmar Recomeça tudo__.

    Repita este processo para **MapReduce2** e **YARN**.

1. Uma vez reiniciado os serviços, selecione cada um deles e desative o modo de manutenção das Ações de **Serviço.**

## <a name="test-restricted-access"></a>Teste de acesso restrito

Utilize os seguintes passos para verificar se só pode ler e listar itens na conta de armazenamento SAS.

1. Ligue-se ao aglomerado. `CLUSTERNAME`Substitua-o pelo nome do seu cluster e introduza o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para listar o conteúdo do recipiente, utilize o seguinte comando a partir do aviso:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    `SASCONTAINER`Substitua-o pelo nome do recipiente criado para a conta de armazenamento SAS. `SASACCOUNTNAME`Substitua-a pelo nome da conta de armazenamento utilizada para o SAS.

    A lista inclui o ficheiro carregado quando o contentor e o SAS foram criados.

3. Utilize o seguinte comando para verificar se pode ler o conteúdo do ficheiro. Substitua o `SASCONTAINER` e como no passo `SASACCOUNTNAME` anterior. `sample.log`Substitua-o pelo nome do ficheiro apresentado no comando anterior:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Este comando lista o conteúdo do ficheiro.

4. Utilize o seguinte comando para descarregar o ficheiro para o sistema de ficheiros local:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Este comando descarrega o ficheiro para um ficheiro local chamado **testfile.txt**.

5. Utilize o seguinte comando para enviar o ficheiro local para um novo ficheiro nomeado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Recebe uma mensagem semelhante ao seguinte texto:

    ```output
    put: java.io.IOException
    ```

    Este erro ocorre porque o local de armazenamento é apenas leitura+lista. Utilize o seguinte comando para colocar os dados no armazenamento predefinido para o cluster, que é preceitável:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Desta vez, a operação deve estar concluída com sucesso.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a adicionar armazenamento de acesso limitado ao seu cluster HDInsight, aprenda outras formas de trabalhar com dados no seu cluster:

* [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Autorizar utilizadores para as Vistas do Apache Ambari](hdinsight-authorize-users-to-ambari.md)
