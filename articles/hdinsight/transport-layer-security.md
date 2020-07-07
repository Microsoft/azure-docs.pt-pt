---
title: Segurança da camada de transporte em Azure HDInsight
description: A segurança da camada de transporte (TLS) e a camada de tomadas seguras (SSL) são protocolos criptográficos que proporcionam segurança de comunicações sobre uma rede de computador.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183505"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Segurança da camada de transporte em Azure HDInsight

As ligações ao cluster HDInsight através do ponto final do cluster público `https://CLUSTERNAME.azurehdinsight.net` são proxiedas através de nós de gateway de cluster. Estas ligações são seguras usando um protocolo chamado TLS. A aplicação de versões mais altas do TLS nos gateways melhora a segurança destas ligações. Para obter mais informações sobre o porquê de utilizar versões mais recentes do TLS, consulte [resolver o Problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Por padrão, os clusters Azure HDInsight aceitam ligações TLS 1.2 nos pontos finais HTTPS públicos e versões mais antigas para retrocompatibilidade. Pode controlar a versão mínima TLS suportada nos nós de gateway durante a criação do cluster utilizando o portal Azure ou um modelo de Gestor de Recursos. Para o portal, selecione a versão TLS do **separador Segurança + rede** durante a criação do cluster. Para um modelo de Gestor de Recursos no tempo de implementação, utilize a propriedade **minSupportedTlsVersion.** Para obter um modelo de amostra, consulte [o modelo mínimo de TLS 1.2 Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Este imóvel suporta três valores: "1.0", "1.1" e "1.2", que correspondem a TLS 1.0+, TLS 1.1+ e TLS 1.2+ respectivamente.

> [!IMPORTANT]
> A partir de 30 de junho de 2020, o Azure HDInsight irá impor versões TLS 1.2 ou posteriores para todas as ligações HTTPS. Recomendamos que garanta que todos os seus clientes estão prontos para lidar com as versões TLS 1.2 ou posteriores. Para obter mais informações, consulte [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Passos seguintes

* [Planeie uma rede virtual para a Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* [Grupos de segurança de rede](../virtual-network/security-overview.md).
