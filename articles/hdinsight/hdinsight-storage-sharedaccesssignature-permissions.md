---
title: Restringir o acesso usando assinaturas de acesso compartilhado – Azure HDInsight
description: Saiba como usar assinaturas de acesso compartilhado para restringir o acesso do HDInsight aos dados armazenados em blobs de armazenamento do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 46cf7d3dd7efecff0280320c100af432367e25f2
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180816"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Usar assinaturas de acesso compartilhado do armazenamento do Azure para restringir o acesso a dados no HDInsight

O HDInsight tem acesso completo aos dados nas contas de armazenamento do Azure associadas ao cluster. Você pode usar assinaturas de acesso compartilhado no contêiner de BLOB para restringir o acesso aos dados. As assinaturas de acesso compartilhado (SAS) são um recurso das contas de armazenamento do Azure que permitem que você limite o acesso aos dados. Por exemplo, fornecendo acesso somente leitura aos dados.

> [!IMPORTANT]  
> Para obter uma solução usando o Apache Ranger, considere usar o HDInsight ingressado no domínio. Para obter mais informações, consulte o documento [Configurar o HDInsight ingressado no domínio](./domain-joined/apache-domain-joined-configure.md) .

> [!WARNING]  
> O HDInsight deve ter acesso completo ao armazenamento padrão para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Um [contêiner de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)existente.  

* Se estiver usando o PowerShell, você precisará do [módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

* Se desejar usar CLI do Azure e você ainda não o tiver instalado, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Se estiver usando [Python](https://www.python.org/downloads/), versão 2,7 ou superior.

* Se estiver C#usando, o Visual Studio deverá ser a versão 2013 ou superior.

* O [esquema de URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) para sua conta de armazenamento. Isso seria `wasb://` para o armazenamento do Azure `abfs://` , por Azure data Lake Storage Gen2 `adl://` ou para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure, `wasbs://`o URI será. Consulte também a [transferência segura](../storage/common/storage-require-secure-transfer.md).

* Um cluster HDInsight existente ao qual adicionar uma assinatura de acesso compartilhado. Caso contrário, você pode usar Azure PowerShell para criar um cluster e adicionar uma assinatura de acesso compartilhado durante a criação do cluster.

* Os arquivos de exemplo [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)do. Esse repositório contém os seguintes itens:

  * Um projeto do Visual Studio que pode criar um contêiner de armazenamento, uma política armazenada e uma SAS para uso com o HDInsight
  * Um script Python que pode criar um contêiner de armazenamento, uma política armazenada e uma SAS para uso com o HDInsight
  * Um script do PowerShell que pode criar um cluster HDInsight e configurá-lo para usar a SAS. Uma versão atualizada é usada mais adiante.
  * Um arquivo de exemplo:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Assinaturas de Acesso Partilhado

Há duas formas de assinaturas de acesso compartilhado:

* Ad hoc: A hora de início, a hora de expiração e as permissões para a SAS são todas especificadas no URI de SAS.

* Política de acesso armazenada: Uma política de acesso armazenada é definida em um contêiner de recursos, como um contêiner de BLOB. Uma política pode ser usada para gerenciar restrições para uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS a uma política de acesso armazenada, a SAS herda as restrições-a hora de início, a hora de expiração e as permissões definidas para a política de acesso armazenada.

A diferença entre as duas formas é importante para um cenário-chave: revogação. Uma SAS é uma URL, portanto, qualquer pessoa que obtenha a SAS pode usá-la, independentemente de quem a solicitou para começar. Se uma SAS for publicada publicamente, ela poderá ser usada por qualquer pessoa do mundo. Uma SAS distribuída é válida até que uma das quatro coisas aconteça:

1. O tempo de expiração especificado na SAS é atingido.

2. O tempo de expiração especificado na política de acesso armazenada referenciada pela SAS é atingido. Os cenários a seguir fazem com que o tempo de expiração seja atingido:

    * O intervalo de tempo decorrido.
    * A política de acesso armazenada é modificada para ter uma hora de expiração no passado. A alteração da hora de expiração é uma maneira de revogar a SAS.

3. A política de acesso armazenada referenciada pela SAS é excluída, que é outra maneira de revogar a SAS. Se você recriar a política de acesso armazenada com o mesmo nome, todos os tokens de SAS para a política anterior serão válidos (se a hora de expiração na SAS não tiver passado). Se você pretende revogar a SAS, certifique-se de usar um nome diferente se você recriar a política de acesso com uma hora de expiração no futuro.

4. A chave de conta que foi usada para criar a SAS é regenerada. A regeneração da chave faz com que todos os aplicativos que usam a chave anterior falhem na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]  
> Um URI de assinatura de acesso compartilhado é associado à chave de conta usada para criar a assinatura e a política de acesso armazenada associada (se houver). Se nenhuma política de acesso armazenada for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta.

Recomendamos que você sempre use políticas de acesso armazenadas. Ao usar políticas armazenadas, você pode revogar assinaturas ou estender a data de expiração conforme necessário. As etapas neste documento usam políticas de acesso armazenado para gerar SAS.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [noções básicas sobre o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Criar uma política armazenada e uma SAS

Salve o token SAS que é produzido no final de cada método. O token será semelhante ao seguinte:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Com o PowerShell

Substitua `RESOURCEGROUP`, `STORAGEACCOUNT` e`STORAGECONTAINER` pelos valores apropriados para seu contêiner de armazenamento existente. Altere o diretório `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` para ou revise `-File` o parâmetro para que ele contenha `Set-AzStorageblobcontent`o caminho absoluto para. Insira o seguinte comando do PowerShell:

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

O uso de variáveis nesta seção é baseado em um ambiente do Windows. Pequenas variações serão necessárias para o bash ou outros ambientes.

1. Substitua `STORAGEACCOUNT` e`STORAGECONTAINER` pelos valores apropriados para seu contêiner de armazenamento existente.

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

2. Defina a chave primária recuperada para uma variável para uso posterior. Substitua `PRIMARYKEY` pelo valor recuperado na etapa anterior e, em seguida, digite o comando a seguir:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Altere o diretório `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` para ou revise `--file` o parâmetro para que ele contenha `az storage blob upload`o caminho absoluto para. Execute os comandos restantes:

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

Abra o `SASToken.py` arquivo e substitua `storage_account_name`, `storage_account_key`e `storage_container_name` pelos valores apropriados para seu contêiner de armazenamento existente e, em seguida, execute o script.

Talvez seja necessário executar `pip install --upgrade azure-storage` se você receber a mensagem `ImportError: No module named azure.storage`de erro.

### <a name="using-c"></a>Utilizar C#

1. Abra a solução no Visual Studio.

2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SASExample** e selecione **Propriedades**.

3. Selecione **configurações** e adicione valores para as seguintes entradas:

   * StorageConnectionString A cadeia de conexão para a conta de armazenamento para a qual você deseja criar uma política armazenada e SAS. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde `myaccount` é o nome da sua conta de armazenamento `mykey` e é a chave para a conta de armazenamento.

   * ContainerName O contêiner na conta de armazenamento ao qual você deseja restringir o acesso.

   * SASPolicyName O nome a ser usado para a política armazenada a ser criada.

   * FileToUpload: O caminho para um arquivo que é carregado no contêiner.

4. Execute o projeto. Salve o token de política SAS, o nome da conta de armazenamento e o nome do contêiner. Esses valores são usados ao associar a conta de armazenamento ao cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Usar a SAS com o HDInsight

Ao criar um cluster HDInsight, você deve especificar uma conta de armazenamento principal e, opcionalmente, pode especificar contas de armazenamento adicionais. Ambos os métodos de adição de armazenamento exigem acesso completo às contas de armazenamento e aos contêineres que são usados.

Para usar uma assinatura de acesso compartilhado para limitar o acesso a um contêiner, adicione uma entrada personalizada à configuração do **site principal** para o cluster. Você pode adicionar a entrada durante a criação do cluster usando o PowerShell ou após a criação do cluster usando o Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que usa a SAS

Substitua `CLUSTERNAME`, `RESOURCEGROUP`, ,`DEFAULTSTORAGEACCOUNT` ,e`TOKEN` pelos valores apropriados. `STORAGECONTAINER` `STORAGEACCOUNT` Insira os comandos do PowerShell:

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
> Quando for solicitado o nome de usuário e a senha do HTTP/s ou SSH, você deverá fornecer uma senha que atenda aos seguintes critérios:
>
> * Deve ter pelo menos 10 caracteres de comprimento.
> * Deve conter pelo menos um dígito.
> * Deve conter pelo menos um caractere não alfanumérico.
> * Deve conter pelo menos uma letra maiúscula ou minúscula.

Leva um tempo para que esse script seja concluído, geralmente em cerca de 15 minutos. Quando o script é concluído sem erros, o cluster foi criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Usar a SAS com um cluster existente

Se você tiver um cluster existente, poderá adicionar a SAS à configuração do **site principal** usando as seguintes etapas:

1. Abra a interface do usuário da Web do amAmbari para seu cluster. O endereço desta página é `https://YOURCLUSTERNAME.azurehdinsight.net`. Quando solicitado, autentique-se no cluster usando o nome do administrador (admin) e a senha que você usou ao criar o cluster.

2. No lado esquerdo da interface do usuário da Web do amAmbari, selecione **HDFS** e, em seguida, selecione a guia **configurações** no meio da página.

3. Selecione a guia **avançado** e, em seguida, role até encontrar a seção **núcleo-site personalizado** .

4. Expanda a seção **Core-site personalizada** , em seguida, role até o final e selecione o link **Adicionar Propriedade...** . Use os seguintes valores para os campos de **chave** e **valor** :

   * **Chave**:`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Valor**: A SAS retornada por um dos métodos executados anteriormente.

     Substitua `CONTAINERNAME` pelo nome do contêiner usado com o C# aplicativo SAS ou. Substitua `STORAGEACCOUNTNAME` pelo nome da conta de armazenamento usado.

5. Clique no botão **Adicionar** para salvar essa chave e o valor e, em seguida, clique no botão **salvar** para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionando acesso de armazenamento SAS", por exemplo) e, em seguida, clique em **salvar**.

    Clique em **OK** quando as alterações forem concluídas.

   > [!IMPORTANT]  
   > Você deve reiniciar vários serviços antes que a alteração entre em vigor.

6. Na interface do usuário da Web do amAmbari, selecione **HDFS** na lista à esquerda e, em seguida, selecione **reiniciar todos os afetados** na lista suspensa **ações de serviço** à direita. Quando solicitado, selecione __confirmar reiniciar tudo__.

    Repita esse processo para MapReduce2 e YARN.

7. Depois que os serviços forem reiniciados, selecione cada um e desabilite o modo de manutenção na lista suspensa **ações de serviço** .

## <a name="test-restricted-access"></a>Testar o acesso restrito

Use as etapas a seguir para verificar se você só pode ler e listar itens na conta de armazenamento SAS.

1. Conecte-se ao cluster. Substitua `CLUSTERNAME` pelo nome do seu cluster e digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para listar o conteúdo do contêiner, use o seguinte comando no prompt:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua `SASCONTAINER` pelo nome do contêiner criado para a conta de armazenamento SAS. Substitua `SASACCOUNTNAME` pelo nome da conta de armazenamento usada para a SAS.

    A lista inclui o arquivo carregado quando o contêiner e a SAS foram criados.

3. Use o comando a seguir para verificar se você pode ler o conteúdo do arquivo. Substitua o `SASCONTAINER` e `SASACCOUNTNAME` o como na etapa anterior. Substituir `sample.log` pelo nome do arquivo exibido no comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Esse comando lista o conteúdo do arquivo.

4. Use o seguinte comando para baixar o arquivo para o sistema de arquivos local:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Esse comando baixa o arquivo em um arquivo local chamado **Testfile. txt**.

5. Use o comando a seguir para carregar o arquivo local em um novo arquivo chamado **testupload. txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Você receberá uma mensagem semelhante ao seguinte texto:

        put: java.io.IOException

    Esse erro ocorre porque o local de armazenamento é somente leitura + lista. Use o seguinte comando para colocar os dados no armazenamento padrão do cluster, que é gravável:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Desta vez, a operação deve ser concluída com êxito.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a adicionar armazenamento de acesso limitado ao seu cluster HDInsight, aprenda outras maneiras de trabalhar com dados em seu cluster:

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

