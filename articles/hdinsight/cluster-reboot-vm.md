---
title: Reinicie VMs para cluster Azure HDInsight
description: Aprenda a reiniciar VMs sem resposta para o cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 46efccad60c06c73145bbf30c119f6a47cb856d5
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323457"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Reinicie VMs para cluster HDInsight

Os aglomerados HDInsight contêm grupos de VMs como nós de cluster. Para aglomerados de longa duração, estes nós podem tornar-se sem resposta por várias razões. Este artigo descreve como reiniciar VMs sem resposta num cluster HDInsight.

## <a name="when-to-reboot"></a>Quando reiniciar

O reinício de VMs no cluster traz tempo de inatividade do nó e reinicialização dos serviços no nó. Enquanto o nó está reiniciando, o cluster pode tornar-se insalubre, os empregos podem abrandar ou falhar. Se está a tentar reiniciar o nó de cabeça ativo, todos os postos de trabalho serão mortos, e não pode submeter empregos ao agrupamento até que os serviços estejam a funcionar novamente. Deve considerar reiniciar VMs apenas quando necessário. Aqui está uma orientação para quando considerar reiniciar VMs.

- Não se pode entrar no nó, mas responde a pings.
- O nó do trabalhador está em baixo sem batimentos cardíacos na UI de Ambari.
- O disco temporário está cheio no nó.
- A tabela de processos no VM tem muitas entradas onde o processo foi concluído, mas está listado com "Estado Encerrado"

## <a name="use-rest-api-to-reboot-vms"></a>Use a API REST para reiniciar VMs

O reboot do nó só é suportado através da REST API atualmente. Pode utilizar a função **'Tentar'** no doc API para enviar pedidos para o HDInsight. São necessários dois passos para utilizar a operação de reinicialização do nó: listar nós e reiniciar os nós.

1. Listam nós. Você pode obter a lista de nó de cluster da API REST ou em Ambari. Mais detalhes podem ser encontrados na [operação HDInsight List Hosts REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Reinicie os anfitriões. Depois de obter os nomes dos nós que pretende reiniciar, use os anfitriões REST API para reiniciar os nós. Mais detalhes podem ser encontrados na [operação API do Reinício de Eventos HDInsight](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Os nomes reais dos nós que pretende reiniciar são especificados numa matriz JSON no corpo de pedido.

```json
[
  "gateway1",
  "gateway3"
]
```

## <a name="next-steps"></a>Passos seguintes

* [HdInsight Máquinas virtuais REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)