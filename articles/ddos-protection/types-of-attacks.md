---
title: Tipos de ataques Azure DDoS Protection Standard atenua
description: Saiba que tipo de ataques a Azure DDoS Protection Standard protege contra.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 50dd5cf9a51ebcc6b2df188838ef2910afcd224d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905508"
---
# <a name="types-of-ddos-attacks-overview"></a>Tipos de DDoS atacam visão geral

A Norma de Proteção DDoS pode atenuar os seguintes tipos de ataques:

- **Ataques volumosos** : Estes ataques inundam a camada da rede com uma quantidade substancial de tráfego aparentemente legítimo. Incluem inundações da UDP, inundações de amplificação e outras inundações de pacotes falsificados. A DDoS Protection Standard atenua estes potenciais ataques multi-gigabytes absorvendo-os e esfregando-os, com a escala global de rede da Azure, automaticamente.
- **Ataques de protocolos** : Estes ataques tornam um alvo inacessível, explorando uma fraqueza na pilha de protocolos de camada 3 e camada 4. Incluem ataques de inundações sINA, ataques de reflexão e outros ataques protocolares. A DDoS Protection Standard atenua estes ataques, diferenciando o tráfego malicioso e legítimo, interagindo com o cliente e bloqueando o tráfego malicioso. 
- **Ataques de camadas de recursos (aplicações)** : Estes ataques visam pacotes de aplicações web, para perturbar a transmissão de dados entre anfitriões. Incluem violações do protocolo HTTP, injeção de SQL, scripts de cross-site e outros ataques de camada 7. Utilize uma Firewall de Aplicação Web, como a firewall da aplicação web Azure [Application Gateway,](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)bem como o DDoS Protection Standard para fornecer defesa contra estes ataques. Existem também ofertas de firewall de aplicações web de terceiros disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Norma do Azure DDoS Protection

A DDoS Protection Standard protege os recursos numa rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, equilibradores de carga e gateways de aplicações. Quando associado à firewall da aplicação web Application Gateway, ou a uma firewall de aplicação web de terceiros implantada numa rede virtual com um IP público, o DDoS Protection Standard pode fornecer capacidade de mitigação da camada completa 3 à camada 7.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um plano de proteção DDoS](manage-ddos-protection.md).