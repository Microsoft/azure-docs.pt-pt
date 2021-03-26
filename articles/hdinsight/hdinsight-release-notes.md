---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607223"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

Se quiser subscrever as notas de lançamento, assista a lançamentos [neste repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Data de lançamento: 03/24/2021

Esta versão aplica-se tanto para HDInsight 3.6 como HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="spark-30-preview"></a>Pré-visualização de faísca 3.0
HDInsight adicionou [suporte Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) ao HDInsight 4.0 como uma função de pré-visualização. 

### <a name="kafka-24-preview"></a>Pré-visualização de Kafka 2.4
HDInsight adicionou [suporte Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) ao HDInsight 4.0 como uma funcionalidade de pré-visualização.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight agora utiliza máquinas virtuais Azure para abastecer o cluster. O serviço está gradualmente a migrar para [conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Após a migração das suas regiões e subscrições, os clusters HDInsight recém-criados serão executados em conjuntos de escala de máquinas virtuais sem ações do cliente. Não se espera mudanças de rutura.

## <a name="deprecation"></a>Preterição
Sem depreciação nesta libertação.

## <a name="behavior-changes"></a>Mudanças de comportamento
### <a name="default-cluster-version-is-changed-to-40"></a>A versão de cluster padrão é alterada para 4.0
A versão padrão do cluster HDInsight é alterada de 3.6 para 4.0. Para obter mais informações sobre as versões disponíveis, consulte as [versões disponíveis.](./hdinsight-component-versioning.md) Saiba mais sobre as novidades em [HDInsight 4.0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Os tamanhos VM do cluster padrão são alterados para série Ev3 
Os tamanhos VM do cluster padrão são alterados de série D para série Ev3. Esta alteração aplica-se aos nós da cabeça e aos nós dos trabalhadores. Para evitar que esta alteração tenha impacto nos seus fluxos de trabalho testados, especifique os tamanhos VM que pretende utilizar no modelo ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Recurso de interface de rede não visível para clusters em execução em conjuntos de escala de máquina virtual Azure
O HDInsight está gradualmente a migrar para conjuntos de escala de máquina virtual Azure. As interfaces de rede para máquinas virtuais já não são visíveis para os clientes para os clusters que utilizam conjuntos de escala de máquina virtual Azure.

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos.

### <a name="os-version-upgrade"></a>Upgrade da versão OS
O HDInsight irá atualizar a versão OS de Ubuntu 16.04 a 18.04. A atualização estará concluída antes de abril de 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 fim de suporte em 30 de junho de 2021
HDInsight 3.6 será o fim do suporte. A partir de 30 de junho de 2021, os clientes não podem criar novos clusters HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Suporte adicional para Spark 3.0.0 e Kafka 2.4.1 como Preview. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](./hdinsight-component-versioning.md).
