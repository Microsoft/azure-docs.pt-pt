---
title: Reinicie VMs para clusters Azure HDInsight
description: Aprenda a reiniciar VMs sem resposta para clusters Azure HDInsight.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: a61735dba60860459d007eb54d4655f41d5ae51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946872"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Reinicie VMs para clusters HDInsight

Os clusters Azure HDInsight contêm grupos de máquinas virtuais (VMs) como nós de cluster. Para aglomerados de longa duração, estes nós podem tornar-se sem resposta por várias razões. Este artigo descreve como reiniciar VMs sem resposta num cluster HDInsight.

## <a name="when-to-reboot"></a>Quando reiniciar

> [!WARNING]
> Quando reinicia os VMs num cluster, o nó não está disponível para utilização e os serviços no nó devem ser reiniciados.

Quando um nó está a reiniciar, o cluster pode tornar-se insalubre, e os empregos podem abrandar ou falhar. Se estás a tentar reiniciar o nó de cabeça ativo, todos os trabalhos de corrida serão interrompidos. Não poderá apresentar empregos ao agrupamento até que os serviços estejam a funcionar novamente. Por estas razões, só deverá reiniciar os VMs quando necessário. Considere reiniciar VMs quando:

- Não podes usar o SSH para entrar no nó, mas responde a pings.
- O nó do trabalhador está em baixo sem batimentos cardíacos na UI de Ambari.
- O disco temporário está cheio no nó.
- A tabela de processos no VM tem muitas entradas onde o processo foi concluído, mas está listado com "Estado Encerrado".

> [!NOTE]
> O reboot de VMs não é suportado para clusters **HBase** e **Kafka** porque o reboot pode causar a perda de dados.

## <a name="use-powershell-to-reboot-vms"></a>Use PowerShell para reiniciar VMs

São necessários dois passos para utilizar a operação de reinicialização do nó: listar nós e reiniciar os nós.

1. Listam nós. Você pode obter a lista de nós de cluster no [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost).

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Reinicie os anfitriões. Depois de obter os nomes dos nós que pretende reiniciar, reinicie os nós utilizando [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost).

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Use uma API REST para reiniciar VMs

Pode utilizar a função **'Tentar'** no doc API para enviar pedidos para o HDInsight. São necessários dois passos para utilizar a operação de reinicialização do nó: listar nós e reiniciar os nós.

1. Listam nós. Você pode obter a lista de nó de cluster da API REST ou em Ambari. Para obter mais informações, consulte [a lista HDInsight que acolhe a operação REST API](/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Reinicie os anfitriões. Depois de obter os nomes dos nós que pretende reiniciar, reinicie os nós utilizando a API REST para reiniciar os nós. O nome do nó segue o padrão de *NodeType (wn/hn/zk/gw)*  +  *x*  +  *primeiros seis caracteres de nome de cluster*. Para obter mais informações, consulte [o reinício do HDInsight para receber a operação REST API](/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Os nomes reais dos nós que pretende reiniciar estão especificados numa matriz JSON no corpo de pedido.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Passos seguintes

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HdInsight máquinas virtuais REST API](/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](/rest/api/hdinsight/)