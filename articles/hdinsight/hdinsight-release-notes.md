---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: d4983ecd1b8afe1ec6bd3cc31df8b711cebaddf1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350267"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

Se quiser subscrever as notas de lançamento, assista a lançamentos [neste repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Data de lançamento: 11/18/2020

Esta versão aplica-se tanto para HDInsight 3.6 como HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Rotação da chave automática para encriptação da chave gerida pelo cliente em repouso
A partir desta versão, os clientes podem utilizar urLs de encriptação sem versão Azure KeyValut para encriptação de chaves gerida pelo cliente em repouso. O HDInsight roda automaticamente as teclas à medida que caducam ou são substituídas por novas versões. Saiba mais detalhes [aqui.](./disk-encryption.md)

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes tamanhos de máquina virtual Zookeeper para serviços spark, Hadoop e ML
A HDInsight anteriormente não suportava a personalização do tamanho do nóleiro zookeeper para tipos de cluster Spark, Hadoop e ML Services. É predefinido para A2_v2/A2 tamanhos de máquina virtual, que são fornecidos gratuitamente. A partir desta versão, pode selecionar um tamanho de máquina virtual Zookeeper que seja mais apropriado para o seu cenário. Serão carregados os nós zookeeper com o tamanho da máquina virtual que não A2_v2/A2. A2_v2 e as máquinas virtuais A2 ainda são fornecidas gratuitamente.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight agora utiliza máquinas virtuais Azure para abastecer o cluster. A partir desta versão, o serviço migrará gradualmente para [conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Após a migração das suas regiões e subscrições, os clusters HDInsight recém-criados serão executados em conjuntos de escala de máquinas virtuais sem ações do cliente. Não se espera mudanças de rutura.

## <a name="deprecation"></a>Preterição
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Depreciação do cluster hdInsight 3.6 ML Services
O tipo de cluster de serviços HDInsight 3.6 ML Services estará terminando o suporte até 31 de dezembro de 2020. Os clientes não vão criar novos clusters de 3,6 ML Services depois de 31 de dezembro de 2020. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Consulte [aqui](./hdinsight-component-versioning.md#available-versions)a expiração do suporte para versões HDInsight e tipos de cluster .

### <a name="disabled-vm-sizes"></a>Tamanhos VM desativados
A partir de 16 de novembro de 2020, o HDInsight vai bloquear novos clientes que criam clusters utilizando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos VM. Os clientes existentes que utilizaram estes tamanhos VM nos últimos três meses não serão afetados. A partir de 9 de janeiro de 2021, o HDInsight bloqueará todos os clientes que criam clusters utilizando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos VM. Os aglomerados existentes funcionarão como estão. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

### <a name="behavior-changes"></a>Mudanças de comportamento
Nenhuma mudança de comportamento para esta libertação.

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A versão padrão do cluster será alterada para 4.0
A partir de fevereiro de 2021, a versão padrão do cluster HDInsight será alterada de 3.6 para 4.0. Para obter mais informações sobre as versões disponíveis, consulte as [versões disponíveis.](./hdinsight-component-versioning.md#available-versions) Saiba mais sobre as novidades em [HDInsight 4.0](./hdinsight-version-release.md)

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 fim de suporte em 30 de junho de 2021
HDInsight 3.6 será o fim do suporte. A partir de 30 de junho de 2021, os clientes não podem criar novos clusters HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](./hdinsight-component-versioning.md).