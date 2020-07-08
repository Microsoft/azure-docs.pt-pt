---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564402"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

## <a name="release-date-06112020"></a>Data de lançamento: 06/11/2020

Esta versão aplica-se tanto para HDInsight 3.6 e 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight utiliza máquinas virtuais Azure para abastecer o cluster agora. A partir desta versão, os novos clusters HDInsight criados começam a usar o conjunto de escala de máquina virtual Azure. A mudança está a desenrolar-se gradualmente. Não deves esperar mudanças. Veja mais sobre [os conjuntos de balanças de máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Reinicie VMs no cluster HDInsight
Nesta versão, apoiamos o reinício de VMs no cluster HDInsight para reiniciar nós sem resposta. Atualmente só é possível fazê-lo através do suporte API, PowerShell e CLI. Para mais informações sobre a API, consulte [este doc.](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json)
 
## <a name="deprecation"></a>Preterição
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Descontinuação do Spark 2.1 e 2.2 no cluster do Spark no HDInsight 3.6
A partir de 1 de julho de 2020, os clientes não podem criar novos clusters Spark com Spark 2.1 e 2.2 no HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mover-se para Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar possíveis interrupções do sistema/suporte.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Descontinuação do Spark 2.3 no cluster do Spark no HDInsight 4.0
A partir de 1 de julho de 2020, os clientes não podem criar novos clusters Spark com Spark 2.3 em HDInsight 4.0. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Descontinuação do Kafka 1.1 no cluster do Kafka no HDInsight 4.0
A partir de 1 de julho de 2020, os clientes não poderão criar novos clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.
 
## <a name="behavior-changes"></a>Mudanças de comportamento
### <a name="esp-spark-cluster-head-node-size-change"></a>Alteração do tamanho do nó de cabeça de cluster ESP Spark 
O tamanho mínimo permitido do nó de cabeça para o cluster ESP Spark é alterado para Standard_D13_V2. VMs com núcleos baixos e memória como nó de cabeça pode causar problemas de cluster ESP devido a CPU relativamente baixo e capacidade de memória. A partir do lançamento, utilize SKUs mais alto do que Standard_D13_V2 e Standard_E16_V3 como nó de cabeça para clusters ESP Spark.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Um VM mínimo de 4 núcleos é necessário para o nó de cabeça 
É necessário um VM mínimo de 4 núcleos para o nó de cabeça para garantir a elevada disponibilidade e fiabilidade dos clusters HDInsight. A partir de 6 de abril de 2020, os clientes só podem escolher 4 núcleos ou acima de VM como Nó de Cabeça para os novos clusters HDInsight. Os aglomerados existentes continuarão a funcionar como esperado. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Alteração do fornecimento de nó de trabalhadores de cluster
Quando 80% dos nós dos trabalhadores estão prontos, o cluster entra em fase **operacional.** Nesta fase, os clientes podem fazer todas as operações de data plane, como executar scripts e empregos. Mas os clientes não podem fazer nenhuma operação de avião de controlo como escalar para cima/para baixo. Só a supressão é suportada.
 
Após a fase **operacional,** o cluster espera mais 60 minutos para os restantes 20% de nós operários. Ao fim destes 60 minutos, o cluster avança para a fase de **corrida,** mesmo que todos os nós dos trabalhadores ainda não estejam disponíveis. Uma vez que um cluster entra na fase **de execução,** você pode usá-lo normalmente. Ambas as operações de plano de controlo como a escalada/para baixo, e as operações de plano de dados como executar scripts e empregos são aceites. Se alguns dos nós de trabalhadores solicitados não estiverem disponíveis, o cluster será marcado como sucesso parcial. É cobrado pelos nós que foram implantados com sucesso. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Criar novo diretor de serviço através do HDInsight
Anteriormente, com a criação de clusters, os clientes podem criar um novo principal serviço para aceder à conta ADLS Gen 1 conectada no portal Azure. A partir de 15 de junho de 2020, os clientes não podem criar um novo principal serviço no fluxo de trabalho de criação hdInsight, apenas o principal de serviço existente é suportado. Consulte [Criar O Título principal e Certificados de Utilização do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Time out para ações de script com criação de cluster
HDInsight suporta executar ações de script com criação de cluster. A partir desta versão, todas as ações de script com criação de cluster devem terminar dentro de **60 minutos**, ou eles estão fora de tempo. As ações de script submetidas a clusters de execução não são impactadas. Saiba mais detalhes [aqui.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process)
 
## <a name="upcoming-changes"></a>Próximas alterações
Sem próximas alterações de rutura a que tens de prestar atenção.
 
## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 
 
## <a name="component-version-change"></a>Alteração da versão do componente
### <a name="hbase-20-to-216"></a>HBase 2.0 a 2.1.6
A versão HBase é atualizada da versão 2.0 para 2.1.6.
 
### <a name="spark-240-to-244"></a>Faísca 2.4.0 a 2.4.4
A versão Spark é atualizada da versão 2.4.0 para 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 a 2.1.1
A versão Kafka é atualizada da versão 2.1.0 para 2.1.1.
 
Pode encontrar as versões componentes atuais para HDInsight 4.0 ad HDInsight 3.6 [neste doc](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="hive-warehouse-connector-issue"></a>Problema do conector do Armazém da Colmeia
Há um problema para o Conector do Armazém da Colmeia nesta versão. A correção será incluída no próximo lançamento. Os aglomerados existentes criados antes desta libertação não são impactados. Evite largar e recriar o cluster, se possível. Por favor, abra o bilhete de apoio se precisar de mais ajuda.
