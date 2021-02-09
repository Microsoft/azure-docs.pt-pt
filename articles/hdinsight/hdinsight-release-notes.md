---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f6b7000812f1adfe6ff7bd93711c9b8fe4ff9adc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988367"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

Se quiser subscrever as notas de lançamento, assista a lançamentos [neste repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Data de lançamento: 02/05/2021

Esta versão aplica-se tanto para HDInsight 3.6 como HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="dav4-series-support"></a>Suporte da série Dav4
HDInsight adicionou suporte da série Dav4 nesta versão. Saiba mais sobre [a série Dav4 aqui.](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)

### <a name="kafka-rest-proxy-ga"></a>Kafka REST Proxy GA 
O Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de uma API REST sobre HTTPS. Kafka Rest Proxy está disponível a partir desta versão. Saiba mais sobre [kafka REST Proxy aqui.](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy)

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight agora utiliza máquinas virtuais Azure para abastecer o cluster. O serviço está gradualmente a migrar para [conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Após a migração das suas regiões e subscrições, os clusters HDInsight recém-criados serão executados em conjuntos de escala de máquinas virtuais sem ações do cliente. Não se espera mudanças de rutura.

## <a name="deprecation"></a>Preterição
### <a name="disabled-vm-sizes"></a>Tamanhos VM desativados
A partir de 9 de janeiro de 2021, o HDInsight bloqueará todos os clientes que criam clusters utilizando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos VM. Os aglomerados existentes funcionarão como estão. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

## <a name="behavior-changes"></a>Mudanças de comportamento
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Alterações padrão do tamanho do VM do cluster para a série Ev3 
Os tamanhos VM do cluster padrão serão alterados de série D para série Ev3. Esta alteração aplica-se aos nós da cabeça e aos nós dos trabalhadores. Para evitar que esta alteração tenha impacto nos seus fluxos de trabalho testados, especifique os tamanhos VM que pretende utilizar no modelo ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Recurso de interface de rede não visível para clusters em execução em conjuntos de escala de máquina virtual Azure
O HDInsight está gradualmente a migrar para conjuntos de escala de máquina virtual Azure. As interfaces de rede para máquinas virtuais já não são visíveis para os clientes para os clusters que utilizam conjuntos de escala de máquina virtual Azure.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Quebra de mudança para .NET para Apache Spark 1.0.0
HDInsight introduz o primeiro grande lançamento oficial de .NET para Apache Spark no próximo lançamento. Fornece a completude da API dataFrame para Spark 2.4.x e Spark 3.0.x juntamente com outras funcionalidades. Haverá alterações para esta versão importante, consulte [este guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) para entender as medidas necessárias para atualizar o seu código e os seus oleodutos. Saiba mais [aqui.](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A versão padrão do cluster será alterada para 4.0
A partir de fevereiro de 2021, a versão padrão do cluster HDInsight será alterada de 3.6 para 4.0. Para obter mais informações sobre as versões disponíveis, consulte as [versões disponíveis.](./hdinsight-component-versioning.md#available-versions) Saiba mais sobre as novidades em [HDInsight 4.0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Upgrade da versão OS
O HDInsight está a atualizar a versão OS de Ubuntu 16.04 para 18.04. A atualização estará concluída antes de abril de 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 fim de suporte em 30 de junho de 2021
HDInsight 3.6 será o fim do suporte. A partir de 30 de junho de 2021, os clientes não podem criar novos clusters HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](./hdinsight-component-versioning.md).

