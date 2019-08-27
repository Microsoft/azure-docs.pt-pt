---
title: Solucionar problemas de falhas de criação de cluster com o Azure HDInsight
description: Saiba como solucionar problemas de criação de cluster para o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.openlocfilehash: 84dc0115edcab07036b43d5fa19310918f7a2408
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035972"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Solucionar problemas de falhas de criação de cluster com o Azure HDInsight

Os seguintes problemas são causas raiz mais comuns para falhas de criação de cluster:

- Problemas de permissão
- Restrições de política de recurso
- Firewalls
- Bloqueios de recurso
- Versões de componentes sem suporte
- Restrições de nome de conta de armazenamento
- Interrupções de serviço

## <a name="permissions-issues"></a>Problemas de permissões

Se você estiver usando Azure data Lake Storage Gen2 e receber o erro ```AmbariClusterCreationFailedErrorCode```, ```Internal server error occurred while processing the request. Please retry the request or contact support.```Abra o portal do Azure, vá para sua conta de armazenamento e, em controle de acesso (iam), certifique-se de que o colaborador de dados do blob de **armazenamento** ou o blob de **armazenamento** A função de proprietário de dados atribuiu acesso à **identidade gerenciada atribuída pelo usuário** para a assinatura. Consulte [configurar permissões para a identidade gerenciada na conta de data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) para obter instruções detalhadas.

Se você estiver usando Azure Data Lake Storage Gen1, consulte as instruções de instalação e configuração [aqui](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 não tem suporte para clusters HBase e não tem suporte no HDInsight versão 4,0.

Se estiver usando o armazenamento do Azure, verifique se o nome da conta de armazenamento é válido durante a criação do cluster.

## <a name="resource-policy-restrictions"></a>Restrições de política de recurso

As políticas do Azure baseadas em assinatura podem negar a criação de endereços IP públicos. A criação do cluster do HDInsight requer dois IPs públicos.  

Em geral, as políticas a seguir podem afetar a criação do cluster:

* As políticas que impedem a criação de endereços IP & balanceadores de carga na assinatura.
* Política que impede a criação da conta de armazenamento.
* Política que impede a exclusão de recursos de rede (endereço IP/Load balanceadores de carga).

## <a name="firewalls"></a>Firewalls

Firewalls em sua rede virtual ou conta de armazenamento podem negar comunicação com endereços IP de gerenciamento do HDInsight.

Permita o tráfego dos endereços IP na tabela abaixo.

| Endereço IP de origem | Destino | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | Entrada |
| 23.99.5.239 | *: 443 | Entrada |
| 168.61.48.131 | *: 443 | Entrada |
| 138.91.141.162 | *: 443 | Entrada |

Além disso, adicione os endereços IP específicos à região em que o cluster é criado. Consulte [endereços IP de gerenciamento do HDInsight](../hdinsight-management-ip-addresses.md) para obter uma lista dos endereços para cada região do Azure.

Se você estiver usando uma rota expressa ou seu próprio servidor DNS personalizado, consulte [planejar uma rede virtual para o Azure HDInsight – Conectando várias redes](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Bloqueios de recursos  

Verifique se não há [bloqueios em sua rede virtual e grupo de recursos](../../azure-resource-manager/resource-group-lock-resources.md).  

## <a name="unsupported-component-versions"></a>Versões de componentes sem suporte

Verifique se você está usando uma [versão com suporte do Azure HDInsight](../hdinsight-component-versioning.md) e quaisquer [Apache Hadoop componentes](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) em sua solução.  

## <a name="storage-account-name-restrictions"></a>Restrições de nome de conta de armazenamento

Os nomes de conta de armazenamento não podem ter mais de 24 caracteres e não podem conter um caractere especial. Essas restrições também se aplicam ao nome do contentor predefinido na conta de armazenamento.

Outras restrições de nomenclatura também se aplicam à criação do cluster. Consulte [restrições de nome de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)para obter mais informações.

## <a name="service-outages"></a>Interrupções de serviço

Verifique o [status do Azure](https://status.azure.com/status) quanto a possíveis interrupções ou problemas de serviço.

## <a name="next-steps"></a>Passos seguintes

* [Expandir o Azure HDInsight utilizando uma rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Utilizar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
