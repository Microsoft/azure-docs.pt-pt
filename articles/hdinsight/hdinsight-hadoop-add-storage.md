---
title: Adicionar contas de armazenamento do Azure adicionais ao HDInsight
description: Saiba como adicionar contas de armazenamento do Azure adicionais a um cluster do HDInsight existente.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 5f46e7846a5b0cd9479f19d475d4604f37bead4f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168746"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar mais contas de armazenamento ao HDInsight

Saiba como utilizar as ações de script para adicionar contas de armazenamento do Azure adicionais ao HDInsight. Os passos neste documento adicionar uma conta de armazenamento a um cluster do HDInsight baseado em Linux existente. Este artigo aplica-se ao [armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md) e apenas contas de armazenamento adicional (não a cluster conta de armazenamento predefinida). Este artigo não se aplica aos [Gen1 de armazenamento do Azure Data Lake](hdinsight-hadoop-use-data-lake-store.md) e [Gen2 de armazenamento do Azure Data Lake](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> As informações neste documento são sobre como adicionar armazenamento adicional a um cluster, depois de este ter sido criado. Para obter informações sobre como adicionar contas de armazenamento durante a criação do cluster, consulte [configurar clusters no HDInsight com o Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

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

__Requisitos de__:

* O script deve ser aplicado a __nós principais__.

## <a name="to-use-the-script"></a>Para utilizar o script

Este script pode ser utilizado a partir do portal do Azure, Azure PowerShell ou a CLI clássica do Azure. Para obter mais informações, consulte a [HDInsight baseado em Linux personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) documento.

> [!IMPORTANT]  
> Ao usar as etapas fornecidas no documento de personalização, utilize as seguintes informações para aplicar este script:
>
> * Substitua qualquer ação de script de exemplo URI com o URI para este script (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Substitua os parâmetros de exemplo com o nome da conta de armazenamento do Azure e a chave da conta de armazenamento para ser adicionado ao cluster. Se utilizar o portal do Azure, esses parâmetros devem ser separados por um espaço.
> * Não é necessário marcar este script como __persistentes__, como ele atualiza diretamente a configuração do Ambari do cluster.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Não é apresentadas no portal do Azure ou ferramentas de contas de armazenamento

Ao visualizar o cluster de HDInsight no portal do Azure, selecionando o __contas de armazenamento__ entrada sob __propriedades__ não apresenta as contas de armazenamento adicionadas através desta ação de script. Azure PowerShell e CLI clássica do Azure não apresentar a conta de armazenamento adicional seja.

As informações de armazenamento não são apresentadas uma vez que o script apenas modifica a configuração de site principal para o cluster. Estas informações não são utilizadas ao obter as informações de cluster utilizando APIs de gestão do Azure.

Para ver informações de conta de armazenamento adicionadas ao cluster com este script, utilize a API de REST do Ambari. Utilize os seguintes comandos para obter estas informações para o seu cluster:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]  
> Definir `$clusterName` para o nome do HDInsight cluster. Definir `$storageAccountName` para o nome da conta de armazenamento. Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]  
> Definir `$PASSWORD` para a palavra-passe da conta de início de sessão (admin) do cluster. Definir `$CLUSTERNAME` para o nome do HDInsight cluster. Definir `$STORAGEACCOUNTNAME` para o nome da conta de armazenamento.
>
> Este exemplo utiliza [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) e [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) para recuperar e analisar dados JSON.

Quando utilizar este comando, substitua __CLUSTERNAME__ com o nome do HDInsight cluster. Substitua __palavra-passe__ com a palavra-passe de início de sessão HTTP para o cluster. Substitua __STORAGEACCOUNT__ com o nome da conta de armazenamento foi adicionado com a ação de script. Informações retornadas este comando parece ser semelhantes ao seguinte texto:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Este texto é um exemplo de uma chave encriptada, o que é utilizado para aceder à conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível aceder ao armazenamento depois de alterar a chave

Se alterar a chave para uma conta de armazenamento, o HDInsight já não consegue aceder à conta de armazenamento. HDInsight utiliza uma cópia em cache da chave no core-site para o cluster. Esta cópia em cache tem de ser atualizada para corresponder a nova chave.

Executar novamente a ação de script faz __não__ atualizar a chave, como o script verifica para ver se já existe uma entrada para a conta de armazenamento. Se já existir uma entrada, ele não faz quaisquer alterações.

Para contornar este problema, tem de remover a entrada existente para a conta de armazenamento. Utilize os seguintes passos para remover a entrada existente:

1. Num browser, abra a interface do Usuário de Web do Ambari para o seu cluster do HDInsight. O URI é https://CLUSTERNAME.azurehdinsight.net. Substitua __CLUSTERNAME__ pelo nome do cluster.

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

> [!WARNING]  
> Não é suportada a utilizar uma conta de armazenamento numa região diferente do que o cluster do HDInsight.

### <a name="additional-charges"></a>Encargos adicionais

Se a conta de armazenamento numa região diferente do que o cluster do HDInsight, pode observar os custos de saída adicionais sobre a faturação do Azure. É aplicada uma taxa de saída quando dados deixam um Datacenter regional. Esta cobrança é aplicada, mesmo que o tráfego é destinado para outro Datacenter do Azure numa região diferente.

> [!WARNING]  
> Não é suportada a utilizar uma conta de armazenamento numa região diferente do que o cluster do HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Aprendeu como adicionar mais contas de armazenamento a um cluster do HDInsight existente. Para obter mais informações sobre as ações de script, consulte [HDInsight baseado em Linux personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md)
