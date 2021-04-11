---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065502"
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

### <a name="eav4-series-support"></a>Suporte da série Eav4
HDInsight adicionou suporte da série Eav4 nesta versão. Saiba mais sobre [a série Dav4 aqui.](../virtual-machines/eav4-easv4-series.md) A série foi disponibilizada nas seguintes regiões: 

* Leste da Austrália
* Sul do Brasil
* E.U.A. Central
* Ásia Leste
* E.U.A. Leste
* Leste do Japão
* Sudeste Asiático
* Sul do Reino Unido
* Europa Ocidental
* E.U.A. Oeste 2

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

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Suporte básico para HDInsight 3.6 a partir de 1 de julho de 2021
A partir de 1 de julho de 2021, a Microsoft irá oferecer [suporte básico](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) para certos tipos de cluster HDInsight 3.6. O plano básico de apoio estará disponível até 3 de abril de 2022. Será automaticamente inscrito no Suporte Básico a partir de 1 de julho de 2021. Não é necessária nenhuma ação por si para optar. Consulte a [nossa documentação](hdinsight-36-component-versioning.md) para a qual os tipos de cluster estão incluídos no suporte básico. 

Não recomendamos a construção de novas soluções no HDInsight 3.6, congelar alterações nos ambientes existentes 3.6. Recomendamos que [emigre os seus clusters para HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Saiba mais sobre [as novidades em HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Suporte adicional para Spark 3.0.0 e Kafka 2.4.1 como Preview. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](./hdinsight-component-versioning.md).

## <a name="recommanded-features"></a>Recursos recomunicados
### <a name="service-tags"></a>Etiquetas de serviço
As etiquetas de serviço simplificam o acesso à rede azure para máquinas virtuais Azure e redes virtuais Azure. As etiquetas de serviço nas regras do seu grupo de segurança de rede (NSG) permitem ou negam o tráfego a um serviço Azure específico. A regra pode ser definida globalmente ou por região de Azure. O Azure fornece a manutenção de endereços IP subjacentes a cada tag. As tags de serviço HDInsight para grupos de segurança de rede (NSGs) são grupos de endereços IP para serviços de saúde e gestão. Estes grupos ajudam a minimizar a complexidade da criação de regras de segurança. Os clientes HDInsight podem ativar a etiqueta de serviço através do portal Azure, PowerShell e REST API. Para obter mais informações, consulte [as etiquetas de serviço do Grupo de Segurança da Rede (NSG) para Azure HDInsight](./hdinsight-service-tags.md).
