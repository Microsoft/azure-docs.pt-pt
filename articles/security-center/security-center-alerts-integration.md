---
title: Alertas de detecção de ameaças de produtos de segurança do Azure na central de segurança do Azure
description: Este tópico apresenta os produtos de segurança do Azure dos quais a central de segurança do Azure pode exibir alertas de ameaça
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913027"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Alertas de detecção de ameaças do Azure WAF e da proteção contra DDoS do Azure

A central de segurança do Azure pode exibir e coletar alertas de detecção de ameaças gerados pelos seguintes produtos de segurança do Azure, (uma licença separada para cada produto é necessária):

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

O Gateway de Aplicação do Azure oferece uma firewall de aplicações Web (WAF) que fornece proteção centralizada para as suas aplicações Web contra exploits e vulnerabilidades comuns.

Os aplicativos Web são cada vez mais direcionados a ataques mal-intencionados que exploram vulnerabilidades comumente conhecidas. O WAF do gateway de aplicativo baseia-se no conjunto de regras principais 3,0 ou 2.2.9 do projeto de segurança de aplicativo Web aberto. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades, sem a necessidade de configuração adicional. Os alertas do WAF são transmitidos para a central de segurança. Para obter mais informações sobre os alertas gerados pelo WAF, consulte [regras e grupos de regras CRS do firewall do aplicativo Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Proteção contra DDoS do Azure<a name="azure-ddos"></a>

Ataques de DDoS (negação de serviço distribuído) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você estiver movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para legitimar utilizadores. Os ataques de DDoS podem ter como destino qualquer ponto de extremidade que possa ser acessado pela Internet.

A proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, fornece uma defesa contra ataques de DDoS. A proteção contra DDoS fornece diferentes camadas de serviço. Para obter mais informações, consulte [visão geral da proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Para obter uma lista dos alertas de proteção contra DDoS do Azure, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azureddos).