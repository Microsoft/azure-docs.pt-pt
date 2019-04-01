---
title: Adicionar contas de armazenamento do Azure adicionais ao HDInsight
description: Saiba como adicionar contas de armazenamento do Azure adicionais a um cluster do HDInsight existente.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: hrasheed
ms.openlocfilehash: 373851c406d95a2e458c017cb311bd5cc4e5b30f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664295"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar mais contas de armazenamento ao HDInsight

Saiba como utilizar as ações de script para adicionar o armazenamento do Azure adicional *contas* para HDInsight. Os passos neste documento, adicione um armazenamento *conta* a um cluster do HDInsight baseado em Linux existente. Este artigo aplica-se ao armazenamento *contas* (não a cluster conta de armazenamento predefinida) e o armazenamento não adicional, tais como [Gen1 de armazenamento do Azure Data Lake](hdinsight-hadoop-use-data-lake-store.md) e [Gen2 de armazenamento do Azure Data Lake ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> As informações neste documento são sobre como adicionar armazenamento adicional a um cluster, depois de este ter sido criado. Para obter informações sobre como adicionar contas de armazenamento durante a criação do cluster, consulte [configurar clusters no HDInsight com o Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Hadoop no HDInsight. Ver [introdução ao HDInsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome da conta de armazenamento e a chave. Ver [gerir definições de conta de armazenamento no portal do Azure](../storage/common/storage-account-manage.md).
* [Nome do cluster corretamente com maiúsculas e minúsculas](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Se utilizar o PowerShell, terá do módulo de AZ.  Ver [descrição geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Se ainda não instalou a CLI do Azure, consulte [Interface de linha de comandos (CLI do Azure)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Se utilizar o bash ou uma linha de comandos do windows, também precisará **jq**, um processador JSON da linha de comandos.  Ver [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/). Para o bash no Ubuntu no Windows 10, veja [subsistema Windows para o guia de instalação de Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Como funciona

Este script utiliza os seguintes parâmetros:

* __Nome da conta de armazenamento do Azure__: O nome da conta de armazenamento para adicionar ao HDInsight cluster. Depois de executar o script, o HDInsight pode ler e escrever dados armazenados nesta conta de armazenamento.

* __Chave de conta de armazenamento do Azure__: Uma chave que conceda acesso à conta de armazenamento.

* __-p__ (opcional): Se for especificado, a chave não é encriptada e é armazenada no ficheiro core-site como texto simples.

Durante o processamento, o script realiza as seguintes ações:

* Se a conta de armazenamento já existe na configuração do site principal para o cluster, o script é encerrado e não mais ações são efetuadas.

* Verifica se a conta de armazenamento existe e que pode ser acessada usando a chave.

* Encripta a chave utilizando as credenciais de cluster.

* Adiciona a conta de armazenamento para o ficheiro core-site.

* Para e reinicia o [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), e [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) serviços. Parar e iniciar estes serviços permitem-lhe utilizar a nova conta de armazenamento.

> [!WARNING]  
> Não é suportada a utilizar uma conta de armazenamento numa localização diferente do que o cluster do HDInsight.

## <a name="the-script"></a>O script

__Localização do script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisitos de__:  O script deve ser aplicado a __nós principais__. Não é necessário marcar este script como __persistentes__, como ele atualiza diretamente a configuração do Ambari do cluster.

## <a name="to-use-the-script"></a>Para utilizar o script

Este script pode ser utilizado entre o Azure PowerShell, CLI do Azure ou o portal do Azure.

### <a name="powershell"></a>PowerShell

Usando [AzHDInsightScriptAction submeter](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Substitua `CLUSTERNAME`, `ACCOUNTNAME`, e `ACCOUNTKEY` com os valores apropriados.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>CLI do Azure

Usando [executar a ação de script do hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Substitua `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, e `ACCOUNTKEY` com os valores apropriados.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Portal do Azure

Ver [aplicam-se uma ação de script para um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Não é apresentadas no portal do Azure ou ferramentas de contas de armazenamento

Ao visualizar o cluster de HDInsight no portal do Azure, selecionando o __contas de armazenamento__ entrada sob __propriedades__ não apresenta as contas de armazenamento adicionadas através desta ação de script. Azure PowerShell e CLI do Azure não apresentar a conta de armazenamento adicional seja.

As informações de armazenamento não são apresentadas uma vez que o script apenas modifica a configuração de site principal para o cluster. Estas informações não são utilizadas ao obter as informações de cluster utilizando APIs de gestão do Azure.

Para ver informações de conta de armazenamento adicionadas ao cluster com este script, utilize a API de REST do Ambari. Utilize os seguintes comandos para obter estas informações para o seu cluster:

### <a name="powershell"></a>PowerShell

Substitua `CLUSTERNAME` com o nome do cluster corretamente com maiúsculas e minúsculas. Em primeiro lugar, identifique a versão de configuração do serviço em utilização, introduzindo o comando abaixo:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Substitua `ACCOUNTNAME` com os nomes reais. Em seguida, substitua `4` real com a versão de configuração do serviço e introduza o comando. Quando lhe for pedido, introduza a palavra-passe de início de sessão do cluster.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
Substitua `myCluster` com o nome do cluster corretamente com maiúsculas e minúsculas.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Substitua `myAccount` com o nome de conta de armazenamento real. Em seguida, substitua `4` real com a versão de configuração do serviço e introduza o comando:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Substitua `CLUSTERNAME` com o nome do cluster corretamente com maiúsculas e minúsculas em ambos os scripts. Em primeiro lugar, identifique a versão de configuração do serviço em utilização, introduzindo o comando abaixo:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Substitua `ACCOUNTNAME` com o nome de conta de armazenamento real. Em seguida, substitua `4` real com a versão de configuração do serviço e introduza o comando:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 Informações retornadas este comando parece ser semelhantes ao seguinte texto:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Este texto é um exemplo de uma chave encriptada, o que é utilizado para aceder à conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível aceder ao armazenamento depois de alterar a chave

Se alterar a chave para uma conta de armazenamento, o HDInsight já não consegue aceder à conta de armazenamento. HDInsight utiliza uma cópia em cache da chave no core-site para o cluster. Esta cópia em cache tem de ser atualizada para corresponder a nova chave.

Executar novamente a ação de script faz __não__ atualizar a chave, como o script verifica para ver se já existe uma entrada para a conta de armazenamento. Se já existir uma entrada, ele não faz quaisquer alterações.

Para contornar este problema, tem de remover a entrada existente para a conta de armazenamento. Utilize os seguintes passos para remover a entrada existente:

1. Num browser, abra a interface do Usuário de Web do Ambari para o seu cluster do HDInsight. O URI é `https://CLUSTERNAME.azurehdinsight.net`. Substitua `CLUSTERNAME` pelo nome do cluster.

    Quando lhe for pedido, introduza o utilizador de início de sessão HTTP e a palavra-passe para o seu cluster.

2. Na lista de serviços no lado esquerdo da página, selecione __HDFS__. Em seguida, selecione o __configurações__ separador no centro da página.

3. Na __filtro...__  , insira um valor de __fs.azure.account__. Esta ação devolve entradas para as contas de armazenamento adicional que foram adicionadas ao cluster. Existem dois tipos de entradas; __keyprovider__ e __chave__. Ambos contêm o nome da conta de armazenamento como parte do nome da chave.

    Seguem-se entradas de exemplo para uma conta de armazenamento com o nome __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Depois de identificar as chaves para a conta de armazenamento tem de remover, utilize o vermelho '-' ícone à direita da entrada de eliminá-lo. Em seguida, utilize o __guardar__ botão para guardar as alterações.

5. Depois das alterações foram guardadas, utilize a ação de script para adicionar a conta de armazenamento e o novo valor de chave para o cluster.

### <a name="poor-performance"></a>Um mau desempenho

Se a conta de armazenamento numa região diferente do que o cluster do HDInsight, pode ter um mau desempenho. Aceder aos dados numa região diferente envia o tráfego de rede fora do Datacenter do Azure regional e na internet pública, que pode introduzir a latência.

### <a name="additional-charges"></a>Encargos adicionais

Se a conta de armazenamento numa região diferente do que o cluster do HDInsight, pode observar os custos de saída adicionais sobre a faturação do Azure. É aplicada uma taxa de saída quando dados deixam um Datacenter regional. Esta cobrança é aplicada, mesmo que o tráfego é destinado para outro Datacenter do Azure numa região diferente.

## <a name="next-steps"></a>Passos Seguintes

Aprendeu como adicionar mais contas de armazenamento a um cluster do HDInsight existente. Para obter mais informações sobre as ações de script, consulte [HDInsight baseado em Linux personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md)
