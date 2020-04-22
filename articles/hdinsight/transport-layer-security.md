---
title: Segurança da camada de transporte em Azure HDInsight
description: A segurança da camada de transporte (TLS) e a camada de tomadas seguras (SSL) são protocolos criptográficos que fornecem segurança de comunicações sobre uma rede informática.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771966"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Segurança da camada de transporte em Azure HDInsight

As ligações ao cluster HDInsight através `https://CLUSTERNAME.azurehdinsight.net` do ponto final do cluster público são proxidas através de nós de gateway cluster. Estas ligações são protegidas usando um protocolo chamado TLS. A aplicação de versões mais elevadas de TLS nos gateways melhora a segurança destas ligações. Para obter mais informações sobre o porquê de utilizar versões mais recentes de TLS, consulte [A resolução do Problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Por padrão, os clusters Azure HDInsight aceitam ligações TLS 1.2 em pontos finais HTTPS públicos e versões mais antigas para retrocompatibilidade. Pode controlar a versão TLS mínima suportada nos nós de gateway durante a criação do cluster utilizando o portal Azure ou um modelo de Gestor de Recursos. Para o portal, selecione a versão TLS do separador **de rede Security +** durante a criação do cluster. Para um modelo de Gestor de Recursos no momento de implantação, utilize a propriedade **minSupportedTlsVersion.** Para um modelo de amostra, consulte o [modelo mínimo TLS 1.2 Quickstart HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Este imóvel suporta três valores: "1.0", "1.1" e "1.2", que correspondem a TLS 1.0+, TLS 1.1+ e TLS 1.2+, respectivamente.

> [!IMPORTANT]
> A partir de 30 de junho de 2020, o Azure HDInsight aplicará versões TLS 1.2 ou posteriores para todas as ligações HTTPS. Recomendamos que garanta que todos os seus clientes estão prontos para lidar com versões TLS 1.2 ou posteriores. Para mais informações, consulte [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Passos seguintes

* [Planeie uma rede virtual para o Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Crie redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* [Grupos de segurança](../virtual-network/security-overview.md)da rede.
