---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535615"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

## <a name="release-date-09282020"></a>Data de lançamento: 09/28/2020

Esta versão aplica-se tanto para HDInsight 3.6 como HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>Autoescala para Consulta Interativa com HDInsight 4.0 está agora geralmente disponível
A escala automática para o tipo de cluster de consulta interativa está agora disponível geral (GA) para HDInsight 4.0. Todos os clusters Interactive Query 4.0 criados após 27 de agosto de 2020 terão suporte de GA para a escala automática.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>Cluster HBase suporta Premium ADLS Gen2
O HDInsight agora suporta o Premium ADLS Gen2 como conta de armazenamento primário para os clusters HDInsight HBase 3.6 e 4.0. Juntamente com [As Escritas Aceleradas,](./hbase/apache-hbase-accelerated-writes.md)pode obter um melhor desempenho para os seus clusters HBase.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Distribuição de partição kafka nos domínios de falhas de Azure
Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. Antes de HDInsight Kafka pode armazenar todas as réplicas de partição no mesmo domínio de avaria. A partir desta versão, o HDInsight suporta agora a distribuição automática de divisórias Kafka com base em domínios de avarias Azure. 

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação
Os clientes podem ativar a encriptação em trânsito entre nós de cluster usando encriptação IPSec com teclas geridas pela plataforma. Esta opção pode ser ativada no tempo de criação do cluster. Consulte mais detalhes sobre [como permitir a encriptação em trânsito.](./domain-joined/encryption-in-transit.md)

### <a name="encryption-at-host"></a>Encriptação no anfitrião
Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de armazenamento. A partir desta versão, pode **ativar a encriptação no anfitrião no disco de dados temporários** ao criar o cluster. A encriptação no anfitrião só é suportada [em certos SKUs VM em regiões limitadas](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). O HDInsight suporta a [seguinte configuração do nó e SKUs](./hdinsight-supported-node-configuration.md). Consulte mais detalhes sobre [como ativar a encriptação no anfitrião.](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys)

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight agora utiliza máquinas virtuais Azure para abastecer o cluster. A partir desta versão, o serviço migrará gradualmente para [conjuntos de escala de máquina virtual Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Todo o processo pode levar meses. Após a migração das suas regiões e subscrições, os clusters HDInsight recém-criados serão executados em conjuntos de escala de máquinas virtuais sem ações do cliente. Não se espera mudanças de rutura.

## <a name="deprecation"></a>Preterição
Sem depreciação para esta libertação.

## <a name="behavior-changes"></a>Mudanças de comportamento
Nenhuma mudança de comportamento para esta libertação.

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes Zookeeper SKU para spark, Hadoop e ML Services
O HDInsight hoje em dia não suporta a mudança de zookeeper SKU para tipos de cluster Spark, Hadoop e ML Services. Utiliza A2_v2/A2 SKU para nós zookeeper e os clientes não são cobrados por eles. No próximo lançamento, os clientes podem mudar o Zookeeper SKU para spark, Hadoop e ML Services, conforme necessário. Serão carregados os nós zookeeper com SKU que não A2_v2/A2. O SKU predefinido ainda será A2_V2/A2 e gratuito.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).