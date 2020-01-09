---
title: Notas de versão do Azure HDInsight
description: Notas de versão mais recentes do Azure HDInsight. Obtenha dicas e detalhes de desenvolvimento para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435428"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte [arquivo de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre clientes corporativos para Apache Hadoop de software livre e análise de Apache Spark no Azure.

## <a name="release-date-12172019"></a>Data de lançamento: 12/17/2019

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao 4,0.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte o [artigo controle de versão do HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Novas funcionalidades

### <a name="service-tags"></a>Etiquetas de serviço
As marcas de serviço simplificam a segurança para máquinas virtuais do Azure e redes virtuais do Azure, permitindo que você restrinja facilmente o acesso à rede para os serviços do Azure. Você pode usar marcas de serviço em suas regras de NSG (grupo de segurança de rede) para permitir ou negar o tráfego para um serviço específico do Azure globalmente ou por região do Azure. O Azure fornece a manutenção de endereços IP subjacentes a cada marca. As marcas de serviço do HDInsight para NSGs (grupos de segurança de rede) são grupos de endereços IP para serviços de integridade e gerenciamento. Esses grupos ajudam a minimizar a complexidade para a criação de regras de segurança. Os clientes do HDInsight podem habilitar a marca de serviço por meio do portal do Azure, do PowerShell e da API REST. Para obter mais informações, consulte [marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Ambari DB Personalizado
O HDInsight agora permite que você use seu próprio banco de BD SQL para Apache Ambari. Você pode configurar esse Ambari DB personalizado no portal do Azure ou por meio do modelo do Resource Manager.  Esse recurso permite que você escolha o banco de BD SQL correto para suas necessidades de processamento e capacidade. Você também pode fazer upgrade facilmente para atender aos requisitos de crescimento de negócios. Para obter mais informações, consulte [Configurar clusters do HDInsight com um AMBARI DB personalizado](hdinsight-custom-ambari-db.md).

![Ambari DB Personalizado](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Preterição
Nenhuma substituição para esta versão. Para se preparar para [as futuras substituições](#upcoming-changes), confira alterações futuras.

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão. Para se preparar para futuras alterações de comportamento, consulte [próximas alterações](#upcoming-changes).

## <a name="upcoming-changes"></a>Alterações futuras
As seguintes alterações ocorrerão em versões futuras. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Imposição de TLS (segurança de camada de transporte) 1,2
O protocolo TLS e protocolo SSL (SSL) são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Para obter mais informações, consulte [segurança da camada de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Embora os clusters do Azure HDInsight aceitem conexões TLS 1,2 em pontos de extremidade HTTPS públicos, o TLS 1,1 ainda tem suporte para compatibilidade com versões anteriores com clientes mais antigos.

A partir da próxima versão, você poderá optar por aceitar e configurar seus novos clusters do HDInsight para apenas as conexões TLS 1,2. 

Posteriormente no ano, a partir do 6/30/2020, o Azure HDInsight impedirá o TLS 1,2 ou versões posteriores para todas as conexões HTTPS. Recomendamos que você verifique se todos os seus clientes estão prontos para lidar com o TLS 1,2 ou versões posteriores.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrando para conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight agora usa máquinas virtuais do Azure para provisionar o cluster. A partir do fevereiro 2020 (a data exata será comunicada posteriormente), o HDInsight usará os conjuntos de dimensionamento de máquinas virtuais do Azure em vez disso. Veja mais sobre os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Alteração do tamanho do nó do cluster do Spark do ESP 
Na próxima versão:
- O tamanho mínimo de nó permitido para o cluster do Spark do ESP será alterado para Standard_D13_V2. 
- As VMs da série a serão preteridas para a criação de novos clusters ESP, pois as VMs da série A podem causar problemas de cluster ESP devido a uma capacidade relativamente baixa de CPU e memória.

### <a name="hbase-20-to-21"></a>HBase 2,0 a 2,1
Na próxima versão do HDInsight 4,0, a versão do HBase será atualizada da versão 2,0 para a 2,1.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a tornar as melhorias de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração de versão do componente
Ampliamos o suporte do HDInsight 3,6 para 31 de dezembro de 2020. Você pode encontrar mais detalhes em [versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).

Nenhuma alteração de versão de componente para o HDInsight 4,0.

Apache Zeppelin no HDInsight 3,6:0.7.0--> 0.7.3. 

Você pode encontrar as versões de componente mais atualizadas [deste documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="new-regions"></a>Novas regiões

### <a name="uae-north"></a>Norte dos E.A.U.
Os IPs de gerenciamento de Norte dos EAU são: `65.52.252.96` e `65.52.252.97`.
