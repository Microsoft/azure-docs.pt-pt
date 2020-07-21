---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0481f92666c8ca547dc427e117f9917afb257629
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511879"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

## <a name="release-date-07132020"></a>Data de lançamento: 07/13/2020

Esta versão aplica-se tanto para HDInsight 3.6 e 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Suporte para lockbox de clientes para Microsoft Azure
A Azure HDInsight suporta agora o Lockbox do cliente Azure. Fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. É utilizado quando o engenheiro da Microsoft precisa de aceder aos dados dos clientes durante um pedido de suporte. Para obter mais informações, consulte [o Customer Lockbox para o Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Políticas de ponto final de serviço para armazenamento
Os clientes podem agora utilizar as Políticas de Ponto final de serviço (SEP) na sub-rede do cluster HDInsight. Saiba mais sobre [a política de pontos finais do serviço Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Preterição
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Descontinuação do Spark 2.1 e 2.2 no cluster do Spark no HDInsight 3.6
A partir de 1 de julho de 2020, os clientes não podem criar novos clusters Spark com Spark 2.1 e 2.2 no HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mover-se para Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar possíveis interrupções do sistema/suporte.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Descontinuação do Spark 2.3 no cluster do Spark no HDInsight 4.0
A partir de 1 de julho de 2020, os clientes não podem criar novos clusters Spark com Spark 2.3 em HDInsight 4.0. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Descontinuação do Kafka 1.1 no cluster do Kafka no HDInsight 4.0
A partir de 1 de julho de 2020, os clientes não poderão criar novos clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.

## <a name="behavior-changes"></a>Mudanças de comportamento
Sem alterações de comportamento a que tenhas de prestar atenção.

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes Zookeeper SKU para spark, Hadoop e ML Services
O HDInsight hoje em dia não suporta a mudança de zookeeper SKU para tipos de cluster Spark, Hadoop e ML Services. Utiliza A2_v2/A2 SKU para nós zookeeper e os clientes não são cobrados por eles. No próximo lançamento, os clientes poderão mudar o Zookeeper SKU para a Spark, Hadoop e ML Services, conforme necessário. Serão carregados os nós zookeeper com SKU que não A2_v2/A2. O SKU predefinido ainda será A2_V2/A2 e gratuito.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Problema de conector de armazém de colmeia fixo
Houve um problema para a usabilidade do conector hive Warehouse em versão anterior. A questão foi corrigida. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Notebook De Zeppelin fixo trunca a principal emissão de zeros
O Zeppelin estava a truncar incorretamente os principais zeros na saída da tabela para o formato string. Resolvemos este problema nesta libertação.

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 ad HDInsight 3.6 [neste doc](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
