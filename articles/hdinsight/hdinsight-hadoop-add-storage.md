---
title: Adicione contas adicionais de Armazenamento Azure ao HDInsight
description: Saiba como adicionar contas adicionais de Armazenamento Azure a um cluster HDInsight existente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206712"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicione contas de armazenamento adicionais ao HDInsight

Aprenda a usar as ações do script para adicionar *contas* adicionais de Armazenamento Azure ao HDInsight. Os passos neste documento adicionam uma *conta* de armazenamento a um cluster HDInsight existente. Este artigo aplica-se a *contas* de armazenamento (não a conta de armazenamento de cluster padrão) e não armazenamento adicional, como [o Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) e [o Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> A informação neste documento é sobre a adição de contas de armazenamento adicionais a um cluster após a sua criação. Para obter informações sobre a adição de contas de armazenamento durante a criação do cluster, consulte [Conjunto de clusters em HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chave da conta de armazenamento. Ver [Gerir as chaves](../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .
* Se utilizar o PowerShell, necessitará do módulo AZ.  Ver [visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Como funciona

Durante o processamento, o script executa as seguintes ações:

* Se a conta de armazenamento já existir na configuração core-site.xml para o cluster, o script sai e não são realizadas mais ações.

* Verifica que a conta de armazenamento existe e pode ser acedida através da chave.

* Encripta a chave usando a credencial do cluster.

* Adiciona a conta de armazenamento ao ficheiro core-site.xml.

* Para e reinicia os serviços [Apache Oozie,](https://oozie.apache.org/) [Apache Hadoop YARN,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)e [Apache Hadoop HDFS.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) Parar e iniciar estes serviços permite-lhes usar a nova conta de armazenamento.

> [!WARNING]  
> A utilização de uma conta de armazenamento num local diferente do cluster HDInsight não é suportada.

## <a name="add-storage-account"></a>Adicionar conta de armazenamento

Utilize a [Ação do Script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) para aplicar as alterações com as seguintes considerações:

|Propriedade | Valor |
|---|---|
|Roteiro de bash URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Tipo de nó(s)|Head|
|Parâmetros|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (opcional)|

* `ACCOUNTNAME` é o nome da conta de armazenamento para adicionar ao cluster HDInsight.
* `ACCOUNTKEY` é a chave de acesso para `ACCOUNTNAME`.
* `-p` é opcional. Se especificada, a chave não está encriptada e é armazenada no ficheiro core-site.xml como texto simples.

## <a name="verification"></a>Verificação

Ao visualizar o cluster HDInsight no portal Azure, a seleção da entrada de Contas de __Armazenamento__ em __Propriedades__ não exibe contas de armazenamento adicionadas através desta ação de script. A Azure PowerShell e a Azure CLI também não apresentam a conta de armazenamento adicional. A informação de armazenamento não é apresentada porque o script apenas modifica a configuração `core-site.xml` para o cluster. Esta informação não é usada na recuperação da informação do cluster utilizando APIs de gestão Azure.

Para verificar o armazenamento adicional utilize um dos métodos a seguir indicados:

### <a name="powershell"></a>PowerShell

O script devolverá o nome ou s da Conta de Armazenamento associado ao cluster dado. Substitua `CLUSTERNAME` com o nome real do cluster e, em seguida, execute o script.

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

1. De um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, onde `CLUSTERNAME` é o nome do seu cluster.

1. Navegue para **HDFS** > **Configs** > site **de núcleo personalizado** **avançado** > personalizado .

1. Observe as teclas que começam com `fs.azure.account.key`. O nome da conta será uma parte da chave como visto nesta imagem da amostra:

   ![verificação através de Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Remover conta de armazenamento

1. De um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, onde `CLUSTERNAME` é o nome do seu cluster.

1. Navegue para **HDFS** > **Configs** > site **de núcleo personalizado** **avançado** > personalizado .

1. Retire as seguintes teclas:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Depois de remover estas teclas e salvar a configuração, precisa reiniciar Oozie, Yarn, MapReduce2, HDFS e Hive um a um.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-firewall"></a>Firewall de armazenamento

Se optar por proteger a sua conta de armazenamento com as **restrições de Firewalls e redes virtuais** em **redes Selecionadas,** certifique-se de que permite a exceção **Permitir serviços confiáveis da Microsoft...** para que o HDInsight possa aceder à sua conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Incapaz de aceder ao armazenamento após mudar de tecla

Se alterar a chave para uma conta de armazenamento, o HDInsight já não pode aceder à conta de armazenamento. O HDInsight utiliza uma cópia em cache da chave no core-site.xml para o cluster. Esta cópia em cache deve ser atualizada para combinar com a nova tecla.

Executar novamente a ação do script __não__ atualiza a chave, uma vez que o script verifica para ver se já existe uma entrada para a conta de armazenamento. Se uma entrada já existe, não faz alterações.

Para resolver este problema:  
1. Retire a conta de armazenamento.
1. Adicione a conta de armazenamento.

> [!IMPORTANT]  
> Não é suportada a rotação da chave de armazenamento da conta de armazenamento primária anexada a um cluster.

### <a name="poor-performance"></a>Mau desempenho

Se a conta de armazenamento estiver numa região diferente do cluster HDInsight, poderá experimentar um fraco desempenho. O acesso a dados numa região diferente envia tráfego de rede fora do centro de dados regional do Azure e através da internet pública, o que pode introduzir latência.

### <a name="additional-charges"></a>Encargos adicionais

Se a conta de armazenamento estiver numa região diferente do cluster HDInsight, poderá notar encargos adicionais de saída na sua faturação Azure. Uma carga de saída é aplicada quando os dados deixam um centro de dados regional. Esta carga é aplicada mesmo que o tráfego esteja destinado a outro centro de dados Azure numa região diferente.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a adicionar contas de armazenamento adicionais a um cluster HDInsight existente. Para obter mais informações sobre as ações do script, consulte [clusters HDInsight baseados em Linux usando ação](hdinsight-hadoop-customize-cluster-linux.md) de script
