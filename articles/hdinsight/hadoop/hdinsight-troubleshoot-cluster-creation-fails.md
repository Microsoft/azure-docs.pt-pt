---
title: Falhas na criação de clusters de resolução de problemas com Azure HDInsight
description: Saiba como resolver problemas de criação de clusters Apache para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: 14b449590f6ffc5e735faa26baadfcc4e526450c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996401"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Falhas na criação de clusters de resolução de problemas com Azure HDInsight

As seguintes questões são as causas mais comuns para as falhas de criação de clusters:

- Questões de permissão
- Restrições à política de recursos
- Firewalls
- Bloqueios de recurso
- Versões de componentes não suportadas
- Restrições de nome de conta de armazenamento
- Interrupções de serviço

## <a name="permissions-issues"></a>Problemas de permissões

Se estiver a utilizar o Azure Data Lake Storage Gen2 e receber o `AmbariClusterCreationFailedErrorCode` erro: " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: abre o portal Azure, aceda à sua conta de Armazenamento, e ao Controlo de Acesso (IAM), certifique-se de que o **Colaborador de Dados do Bloco de Armazenamento** ou a função de Proprietário de **Dados blob de armazenamento** atribuiu acesso à identidade gerida do Utilizador **atribuído** para a subscrição. Veja [Configurar permissões para a identidade gerida na conta do Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) para obter instruções detalhadas.

Se estiver a utilizar a Azure Data Lake Storage Gen1, consulte as instruções de configuração e configuração [aqui](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 não é suportado para clusters HBase, e não é suportado na versão 4.0 do HDInsight.

Se utilizar o Azure Storage, certifique-se de que o nome da conta de armazenamento é válido durante a criação do cluster.

## <a name="resource-policy-restrictions"></a>Restrições à política de recursos

As políticas Azure baseadas em subscrições podem negar a criação de endereços IP públicos. A criação do cluster do HDInsight requer dois IPs públicos.  

Em geral, as seguintes políticas podem ter impacto na criação de clusters:

* Políticas que impedem a criação de equilibradores ip address & carga dentro da subscrição.
* Política que impede a criação de conta de armazenamento.
* Política que impede a supressão de recursos de rede (Endereço IP /Balanceadores de Carga).

## <a name="firewalls"></a>Firewalls

Firewalls na sua rede virtual ou conta de armazenamento podem negar a comunicação com endereços IP de gestão HDInsight.

Permitir o tráfego a partir dos endereços IP na tabela abaixo.

| Endereço IP de origem | Destino | Direção |
|---|---|---|
| 168.61.49.99 | *:443 | Entrada |
| 23.99.5.239 | *:443 | Entrada |
| 168.61.48.131 | *:443 | Entrada |
| 138.91.141.162 | *:443 | Entrada |

Adicione também os endereços IP específicos da região onde o cluster é criado. Consulte [endereços IP de gestão HDInsight](../hdinsight-management-ip-addresses.md) para uma listagem dos endereços para cada região de Azure.

Se estiver a utilizar uma rota expressa ou o seu próprio servidor DNS personalizado, consulte [Plan a virtual network for Azure HDInsight - conectando várias redes](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Bloqueios de recursos  

Certifique-se de que não existem [bloqueios na sua rede virtual e grupo de recursos.](../../azure-resource-manager/management/lock-resources.md) Os clusters não podem ser criados ou eliminados se o grupo de recursos estiver bloqueado. 

## <a name="unsupported-component-versions"></a>Versões de componentes não suportadas

Certifique-se de que está a utilizar uma [versão suportada do Azure HDInsight](../hdinsight-component-versioning.md) e de quaisquer [componentes Apache Hadoop](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions) na sua solução.  

## <a name="storage-account-name-restrictions"></a>Restrições de nome de conta de armazenamento

Os nomes das contas de armazenamento não podem ter mais de 24 caracteres e não podem conter um carácter especial. Essas restrições também se aplicam ao nome do contentor predefinido na conta de armazenamento.

Outras restrições de nomeação também se aplicam à criação de clusters. Consulte [as restrições de nome cluster,](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)para obter mais informações.

## <a name="service-outages"></a>Interrupções de serviço

Verifique [o estado do Azure](https://status.azure.com) para eventuais interrupções ou problemas de serviço.

## <a name="next-steps"></a>Próximos passos

* [Expandir o Azure HDInsight com uma Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Utilizar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
