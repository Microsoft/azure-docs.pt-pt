---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 0c14e5537385b7518fd08d9d3599993bc6d82f88
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535506"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

## <a name="release-date-10082020"></a>Data de lançamento: 10/08/2020

Esta versão aplica-se tanto para HDInsight 3.6 como HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Clusters privados HDInsight sem ligação pública IP e Private (Preview)
O HDInsight suporta agora a criação de clusters sem acesso público de IP e ligação privada aos clusters em pré-visualização. Os clientes podem usar as novas definições avançadas de rede para criar um cluster totalmente isolado sem IP público e usar os seus próprios pontos finais privados para aceder ao cluster. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight agora utiliza máquinas virtuais Azure para abastecer o cluster. A partir desta versão, o serviço migrará gradualmente para [conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Após a migração das suas regiões e subscrições, os clusters HDInsight recém-criados serão executados em conjuntos de escala de máquinas virtuais sem ações do cliente. Não se espera mudanças de rutura.

## <a name="deprecation"></a>Preterição
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Depreciação do cluster hdInsight 3.6 ML Services
O tipo de cluster de serviços HDInsight 3.6 ML Services será o fim do suporte até 31 de dezembro de 2020. Os clientes não vão criar novos clusters de 3,6 ML Services depois disso. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Consulte [aqui](./hdinsight-component-versioning.md#available-versions)a expiração do suporte para versões HDInsight e tipos de cluster .

## <a name="behavior-changes"></a>Mudanças de comportamento
Nenhuma mudança de comportamento para esta libertação.

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes tamanhos de máquina virtual Zookeeper para serviços spark, Hadoop e ML
O HDInsight hoje em dia não suporta personalizar o tamanho do nó do Zookeeper para tipos de cluster Spark, Hadoop e ML Services. É predefinido para A2_v2/A2 tamanhos de máquina virtual, que são fornecidos gratuitamente. No próximo lançamento, pode selecionar um tamanho de máquina virtual Zookeeper que seja mais apropriado para o seu cenário. Serão carregados os nós zookeeper com o tamanho da máquina virtual que não A2_v2/A2. A2_v2 e as máquinas virtuais A2 ainda são fornecidas gratuitamente.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](./hdinsight-component-versioning.md).
