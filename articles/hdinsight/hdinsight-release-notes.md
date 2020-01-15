---
title: Notas de versão do Azure HDInsight
description: Notas de versão mais recentes do Azure HDInsight. Obtenha dicas e detalhes de desenvolvimento para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: f0dd7160ae41ac004564ea21300945e135d31689
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945526"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte [arquivo de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre clientes empresariais para análise de software livre no Azure.

## <a name="release-date-01092019"></a>Data de lançamento: 01/09/2019

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação esteja ativa em sua região em vários dias.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte o [artigo controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Novas funcionalidades
### <a name="tls-12-enforcement"></a>Imposição de TLS 1.2
O protocolo TLS e protocolo SSL (SSL) são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Saiba mais sobre o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). O HDInsight usa o TLS 1,2 em pontos de extremidade HTTPs públicos, mas o TLS 1,1 ainda tem suporte para compatibilidade com versões anteriores. 

Com esta versão, os clientes podem optar pelo TLS 1,2 apenas para todas as conexões por meio do ponto de extremidade do cluster público. Para dar suporte a isso, a nova propriedade **minSupportedTlsVersion** é introduzida e pode ser especificada durante a criação do cluster. Se a propriedade não estiver definida, o cluster ainda dará suporte a TLS 1,0, 1,1 e 1,2, que é o mesmo comportamento de hoje. Os clientes podem definir o valor dessa propriedade como "1,2", o que significa que o cluster só oferece suporte a TLS 1,2 e superior. 

### <a name="bring-your-own-key-for-disk-encryption"></a>Traga sua própria chave para a criptografia de disco
Todos os discos gerenciados no HDInsight são protegidos com o Azure Criptografia do Serviço de Armazenamento (SSE). Por padrão, os dados nesses discos são criptografados por chaves gerenciadas pela Microsoft. A partir desta versão, você pode Bring Your Own Key (BYOK) para a criptografia de disco e gerenciá-lo usando Azure Key Vault. A criptografia BYOK é uma configuração de uma etapa durante a criação do cluster sem custo adicional. Basta registrar o HDInsight como uma identidade gerenciada com Azure Key Vault e adicionar a chave de criptografia ao criar o cluster. 

## <a name="deprecation"></a>Preterição
Nenhuma substituição para esta versão. Para se preparar para [as futuras substituições](#upcoming-changes), confira alterações futuras.

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão. Para se preparar para as alterações futuras, confira [alterações futuras](#upcoming-changes).

## <a name="upcoming-changes"></a>Alterações futuras
As seguintes alterações ocorrerão em versões futuras. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Uma VM de 4 núcleos mínima é necessária para o nó de cabeçalho 
Uma VM de 4 núcleos mínima é necessária para o nó de cabeçalho para garantir a alta disponibilidade e a confiabilidade dos clusters HDInsight. A partir de 6 de abril de 2020, os clientes podem escolher apenas 4 núcleos ou acima da VM como nó principal para os novos clusters HDInsight. Os clusters existentes continuarão a ser executados conforme esperado. 

### <a name="esp-spark-cluster-node-size-change"></a>Alteração do tamanho do nó do cluster do Spark do ESP 
Na próxima versão, o tamanho mínimo de nó permitido para o cluster do Spark do ESP será alterado para Standard_D13_V2. As VMs da série a podem causar problemas de cluster ESP devido à capacidade relativamente baixa de CPU e memória. As VMs da série A serão preteridas para a criação de novos clusters ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrando para conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight agora usa máquinas virtuais do Azure para provisionar o cluster. Na próxima versão, o HDInsight usará os conjuntos de dimensionamento de máquinas virtuais do Azure em vez disso. Veja mais sobre os conjuntos de dimensionamento de máquinas virtuais do Azure.

### <a name="hbase-20-to-21"></a>HBase 2,0 a 2,1
Na próxima versão do HDInsight 4,0, a versão do HBase será atualizada da versão 2,0 para a 2,1.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a tornar as melhorias de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração de versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para o HDInsight 4,0 Ad HDInsight 3,6 aqui.
