---
title: Adicione contas adicionais de Armazenamento Azure ao HDInsight
description: Saiba como adicionar contas adicionais de Armazenamento Azure a um cluster HDInsight existente.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 11852046442901c70112b5e80fef371671546412
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945939"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicione contas de armazenamento adicionais ao HDInsight

Saiba como usar as ações de script para adicionar *contas* adicionais de Armazenamento Azure ao HDInsight. Os passos deste documento adicionam uma *conta* de armazenamento a um cluster HDInsight existente. Este artigo aplica-se às *contas* de armazenamento (não à conta de armazenamento padrão), e não ao armazenamento adicional, tais como [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) .

> [!IMPORTANT]  
> A informação neste documento tem a ver com a adição de contas de armazenamento adicionais a um cluster depois de ter sido criada. Para obter informações sobre a adição de contas de armazenamento durante a criação de [clusters, consulte Configurar clusters em HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, entre outros](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chave da conta de armazenamento. Consulte [as teclas de acesso à conta de armazenamento](../storage/common/storage-account-keys-manage.md).
* Se utilizar o PowerShell, necessitará do módulo AZ.  Ver [visão geral da Azure PowerShell](/powershell/azure/).

## <a name="how-it-works"></a>Como funciona

Durante o processamento, o script faz as seguintes ações:

* Se a conta de armazenamento já existir na configuração core-site.xml para o cluster, o script sai e não são feitas mais ações.

* Verifica se a conta de armazenamento existe e pode ser acedida através da chave.

* Encripta a chave utilizando a credencial de cluster.

* Adiciona a conta de armazenamento ao ficheiro core-site.xml.

* Para e reinicia os serviços Apache Oozie, Apache Hadoop YARN, Apache Hadoop MapReduce2 e Apache Hadoop HDFS. Parar e iniciar estes serviços permite-lhes utilizar a nova conta de armazenamento.

> [!WARNING]  
> A utilização de uma conta de armazenamento num local diferente do cluster HDInsight não é suportada.

## <a name="add-storage-account"></a>Adicionar conta de armazenamento

Utilize [a Ação do Script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) para aplicar as alterações com as seguintes considerações:

|Propriedade | Valor |
|---|---|
|URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Tipo de nó(s)|Head|
|Parâmetros|`ACCOUNTNAME``ACCOUNTKEY` `-p` (opcional)|

* `ACCOUNTNAME` é o nome da conta de armazenamento a adicionar ao cluster HDInsight.
* `ACCOUNTKEY` é a chave de acesso para `ACCOUNTNAME` .
* `-p` é opcional. Se especificado, a chave não é encriptada e é armazenada no ficheiro core-site.xml como texto simples.

## <a name="verification"></a>Verificação

Ao visualizar o cluster HDInsight no portal Azure, a seleção da entrada __de Contas de Armazenamento__ em __Propriedades__ não apresenta contas de armazenamento adicionadas através desta ação de script. A Azure PowerShell e a Azure CLI também não apresentam a conta de armazenamento adicional. A informação de armazenamento não é apresentada porque o script apenas modifica a `core-site.xml` configuração para o cluster. Esta informação não é usada quando se recupera a informação do cluster utilizando AS DE Gestão Azure.

Para verificar a utilização adicional do armazenamento, um dos métodos a seguir indicados:

### <a name="powershell"></a>PowerShell

O script devolverá o(s) nome(s) da Conta de Armazenamento associado ao cluster dado. `CLUSTERNAME`Substitua-o pelo nome do cluster real e, em seguida, execute o script.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. Navegue para **o núcleo** avançado de  >  **hdfs Configs.**  >    >  

1. Observe as chaves que começam `fs.azure.account.key` com . O nome da conta será uma parte da chave vista nesta imagem de amostra:

   ![verificação através de Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Remover conta de armazenamento

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. Navegue para **o núcleo** avançado de  >  **hdfs Configs.**  >    >  

1. Retire as seguintes teclas:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Depois de remover estas teclas e guardar a configuração, precisa reiniciar Oozie, Yarn, MapReduce2, HDFS e Hive um a um.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-firewall"></a>Firewall de armazenamento

Se optar por proteger a sua conta de armazenamento com as restrições **de Firewalls e redes virtuais** em redes **selecionadas,** certifique-se de ativar a exceção **Permitir serviços de Microsoft confiáveis...** para que o HDInsight possa aceder à sua conta de armazenamento`.`

### <a name="unable-to-access-storage-after-changing-key"></a>Não conseguir aceder ao armazenamento depois de mudar a chave

Se alterar a chave para uma conta de armazenamento, o HDInsight já não pode aceder à conta de armazenamento. O HDInsight utiliza uma cópia em cache da chave no core-site.xml para o cluster. Esta cópia em cache deve ser atualizada para corresponder à nova chave.

Executar a ação do script novamente **não** atualiza a chave, uma vez que o script verifica para ver se já existe uma entrada para a conta de armazenamento. Se uma entrada já existe, não faz alterações.

Para contornar este problema:  
1. Retire a conta de armazenamento.
1. Adicione a conta de armazenamento.

> [!IMPORTANT]  
> Não é suportada a chave de armazenamento da conta de armazenamento primária anexada a um cluster.

## <a name="next-steps"></a>Próximos passos

Aprendeu a adicionar contas de armazenamento adicionais a um cluster HDInsight existente. Para obter mais informações sobre as ações de script, consulte [os clusters HDInsight baseados em Linux utilizando ação de script](hdinsight-hadoop-customize-cluster-linux.md)