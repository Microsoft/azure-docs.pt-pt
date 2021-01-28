---
title: Segurança da camada de transporte em Azure HDInsight
description: A segurança da camada de transporte (TLS) e a camada de tomadas seguras (SSL) são protocolos criptográficos que proporcionam segurança de comunicações sobre uma rede de computador.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 648f24e87e7d8a49c9c495a78e56afb8ae9e8aae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944704"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Segurança da camada de transporte em Azure HDInsight

As ligações ao cluster HDInsight através do ponto final do cluster público `https://CLUSTERNAME.azurehdinsight.net` são proxiedas através de nós de gateway de cluster. Estas ligações são seguras usando um protocolo chamado TLS. A aplicação de versões mais altas do TLS nos gateways melhora a segurança destas ligações. Para obter mais informações sobre o porquê de utilizar versões mais recentes do TLS, consulte [resolver o Problema TLS 1.0](/security/solving-tls1-problem).

Por padrão, os clusters Azure HDInsight aceitam ligações TLS 1.2 nos pontos finais HTTPS públicos e versões mais antigas para retrocompatibilidade. Pode controlar a versão mínima TLS suportada nos nós de gateway durante a criação do cluster utilizando o portal Azure ou um modelo de Gestor de Recursos. Para o portal, selecione a versão TLS do **separador Segurança + rede** durante a criação do cluster. Para um modelo de Gestor de Recursos no tempo de implementação, utilize a propriedade **minSupportedTlsVersion.** Para obter um modelo de amostra, consulte [o modelo mínimo de TLS 1.2 Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Este imóvel suporta três valores: "1.0", "1.1" e "1.2", que correspondem a TLS 1.0+, TLS 1.1+ e TLS 1.2+ respectivamente.


## <a name="next-steps"></a>Próximos passos

* [Planeie uma rede virtual para a Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).