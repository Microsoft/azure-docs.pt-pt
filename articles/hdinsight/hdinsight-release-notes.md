---
title: Notas de versão do Azure HDInsight
description: Notas de lançamento mais recentes para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: f8331b64792781a89428f8c5070375c31c0da48d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084894"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre lançamentos anteriores, consulte [HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de código aberto no Azure.

## <a name="release-date-01092020"></a>Data de lançamento: 01/09/2020

Esta versão aplica-se tanto para HDInsight 3.6 como 4.0. O lançamento do HDInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir abaixo as alterações, por favor espere que o lançamento seja transmitido na sua região em vários dias.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para mais informações, consulte o artigo de [versão HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Novas funcionalidades
### <a name="tls-12-enforcement"></a>Imposição de TLS 1.2
A Segurança da Camada de Transporte (TLS) e a Camada de Tomadas Seguras (SSL) são protocolos criptográficos que fornecem segurança de comunicações sobre uma rede informática. Saiba mais sobre [o TLS.](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0) O HDInsight utiliza TLS 1.2 em pontos finais públicos de HTTPs, mas o TLS 1.1 ainda é suportado para a retrocompatibilidade. 

Com esta versão, os clientes podem optar pelo TLS 1.2 apenas para todas as ligações através do ponto final do cluster público. Para suportar isto, o novo imóvel **minSupportedTlsVersion** é introduzido e pode ser especificado durante a criação do cluster. Se a propriedade não estiver definida, o cluster ainda suporta TLS 1.0, 1.1 e 1.2, o que é o mesmo que o comportamento de hoje. Os clientes podem definir o valor deste imóvel para "1.2", o que significa que o cluster suporta apenas TLS 1.2 ou superior. Para mais informações, consulte [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Traga a sua própria chave para encriptação de disco
Todos os discos geridos no HDInsight estão protegidos com encriptação do serviço de armazenamento Azure (SSE). Os dados desses discos são encriptados por teclas geridas pela Microsoft por padrão. A partir desta versão, pode trazer a sua própria chave (BYOK) para encriptação de disco e geri-la utilizando o Cofre de Chaves Azure. A encriptação BYOK é uma configuração de um passo durante a criação do cluster sem custoadicional. Basta registar o HDInsight como uma identidade gerida com o Azure Key Vault e adicionar a chave de encriptação quando criar o seu cluster. Para obter mais informações, consulte a [encriptação](https://docs.microsoft.com/azure/hdinsight/disk-encryption)do disco de chave gerida pelo Cliente .

## <a name="deprecation"></a>Preterição
Sem depreciações para esta libertação. Para se preparar para as próximas depreciações, consulte [as próximas alterações.](#upcoming-changes)

## <a name="behavior-changes"></a>Mudanças de comportamento
Nenhuma mudança de comportamento para esta libertação. Para se preparar para as próximas alterações, consulte [as próximas alterações.](#upcoming-changes)

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações acontecerão nos próximos lançamentos. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>É necessário um VM mínimo de 4 núcleos para o nó de cabeça 
É necessário um VM mínimo de 4 núcleos para o Nó de Cabeça para garantir a elevada disponibilidade e fiabilidade dos clusters HDInsight. A partir de 6 de abril de 2020, os clientes só podem escolher 4 núcleos ou acima de VM como Head Node para os novos clusters HDInsight. Os aglomerados existentes continuarão a funcionar como esperado. 

### <a name="esp-spark-cluster-node-size-change"></a>Mudança de tamanho do nó do cluster esp Spark 
Na próxima versão, o tamanho mínimo permitido para o cluster ESP Spark será alterado para Standard_D13_V2. Os VMs da série A podem causar problemas de cluster esp devido à cpu relativamente baixa e à capacidade de memória. Os VMs da série A serão depreciados pela criação de novos clusters esp.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquinas virtuais Azure
O HDInsight utiliza agora máquinas virtuais Azure para fornecer o cluster. No próximo lançamento, o HDInsight utilizará conjuntos de escala de máquinas virtuais Azure. Veja mais sobre os conjuntos de escala de máquinas virtuais Azure.

### <a name="hbase-20-to-21"></a>HBase 2.0 a 2.1
No próximo lançamento do HDInsight 4.0, a versão HBase será atualizada da versão 2.0 para 2.1.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração na versão do componente para esta versão. Pode encontrar aqui as versões atuais do componente para HDInsight 4.0 ad HDInsight 3.6.

