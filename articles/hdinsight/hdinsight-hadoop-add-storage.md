---
title: Adicionar outras contas de armazenamento do Azure ao HDInsight
description: Saiba como adicionar mais contas de armazenamento do Azure a um cluster HDInsight existente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 6ad583fdb880e36e6ac9c2dfda56bb68378ea598
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314011"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar mais contas de armazenamento ao HDInsight

Saiba como usar ações de script para adicionar mais *contas* de armazenamento do Azure ao HDInsight. As etapas neste documento adicionam uma *conta* de armazenamento a um cluster HDInsight existente. Este artigo se aplica a *contas* de armazenamento (não a conta de armazenamento de cluster padrão) e não a armazenamento adicional, como [Azure data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) e [Azure data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> As informações neste documento são sobre como adicionar mais contas de armazenamento a um cluster depois que ele tiver sido criado. Para obter informações sobre como adicionar contas de armazenamento durante a criação do cluster, consulte [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chave da conta de armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md).
* Se estiver usando o PowerShell, você precisará do módulo AZ.  Consulte [visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Como funciona

Durante o processamento, o script executa as seguintes ações:

* Se a conta de armazenamento já existir na configuração Core-site. xml para o cluster, o script será encerrado e nenhuma ação adicional será executada.

* Verifica se a conta de armazenamento existe e pode ser acessada usando a chave.

* Criptografa a chave usando a credencial do cluster.

* Adiciona a conta de armazenamento ao arquivo Core-site. xml.

* Para e reinicia os serviços [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)e [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) . Parar e iniciar esses serviços permite que eles usem a nova conta de armazenamento.

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

## <a name="add-storage-account"></a>Adicionar conta de armazenamento

Use a [ação de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) para aplicar as alterações com as seguintes considerações:

|Propriedade | Valor |
|---|---|
|URI do script bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Tipo (s) de nó|Head|
|Parâmetros|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (opcional)|

* `ACCOUNTNAME` é o nome da conta de armazenamento a ser adicionada ao cluster HDInsight.
* `ACCOUNTKEY` é a chave de acesso para `ACCOUNTNAME`.
* `-p` é opcional. Se especificado, a chave não é criptografada e armazenada no arquivo Core-site. xml como texto sem formatação.

## <a name="verification"></a>Verificação

Ao exibir o cluster HDInsight no portal do Azure, a seleção da entrada __contas de armazenamento__ em __Propriedades__ não exibe as contas de armazenamento adicionadas por meio dessa ação de script. Azure PowerShell e CLI do Azure não exibem também a conta de armazenamento adicional. As informações de armazenamento não são exibidas porque o script modifica apenas a configuração de `core-site.xml` para o cluster. Essas informações não são usadas ao recuperar as informações de cluster usando as APIs de gerenciamento do Azure.

Para verificar se o armazenamento adicional usa um dos métodos mostrados abaixo:

### <a name="powershell"></a>PowerShell

O script retornará os nomes de conta de armazenamento associados ao cluster especificado. Substitua `CLUSTERNAME` pelo nome real do cluster e, em seguida, execute o script.

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

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Navegue até **HDFS** > **configurações** > **avançado** > **núcleo personalizado-site**.

1. Observe as chaves que começam com `fs.azure.account.key`. O nome da conta fará parte da chave, como visto nesta imagem de exemplo:

   ![verificação por meio do Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Remover conta de armazenamento

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Navegue até **HDFS** > **configurações** > **avançado** > **núcleo personalizado-site**.

1. Remova as seguintes chaves:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Depois de remover essas chaves e salvar a configuração, você precisa reiniciar Oozie, yarn, MapReduce2, HDFS e Hive um por um.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-firewall"></a>Firewall de armazenamento

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...** para que o HDInsight possa acessar sua conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível acessar o armazenamento após a alteração da chave

Se você alterar a chave de uma conta de armazenamento, o HDInsight não poderá mais acessar a conta de armazenamento. O HDInsight usa uma cópia armazenada em cache da chave no Core-site. xml para o cluster. Essa cópia armazenada em cache deve ser atualizada para corresponder à nova chave.

Executar a ação de script novamente __não atualiza a__ chave, pois o script verifica se já existe uma entrada para a conta de armazenamento. Se uma entrada já existir, ela não fará nenhuma alteração.

Para contornar esse problema:  
1. Remova a conta de armazenamento.
1. Adicione a conta de armazenamento.

> [!IMPORTANT]  
> Não há suporte para a rotação da chave de armazenamento para a conta de armazenamento primária anexada a um cluster.

### <a name="poor-performance"></a>Desempenho insatisfatório

Se a conta de armazenamento estiver em uma região diferente do cluster HDInsight, você poderá experimentar um desempenho insatisfatório. O acesso a dados em uma região diferente envia o tráfego de rede fora do data center regional do Azure e pela Internet pública, que pode introduzir latência.

### <a name="additional-charges"></a>Encargos adicionais

Se a conta de armazenamento estiver em uma região diferente do cluster HDInsight, você poderá notar encargos de egresso adicionais em sua cobrança do Azure. Um encargo de egresso é aplicado quando os dados saem de um data center regional. Essa cobrança é aplicada mesmo que o tráfego seja destinado a outro data center do Azure em uma região diferente.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a adicionar mais contas de armazenamento a um cluster HDInsight existente. Para obter mais informações sobre as ações de script, consulte [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
