---
title: Integração do Centro de segurança com produtos de segurança do Azure | Documentos da Microsoft
description: Este tópico apresenta os produtos de segurança do Azure que foram integrados com o Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571741"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Integração do Centro de segurança com produtos de segurança do Azure no ASC

Centro de segurança oferece aos clientes com licenças Microsoft adicionais para integrar suas descobertas ao centro de segurança e visualizá-los de forma consolidada.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

O Gateway de Aplicação do Azure oferece uma firewall de aplicações Web (WAF) que fornece proteção centralizada para as suas aplicações Web contra exploits e vulnerabilidades comuns.

Aplicativos Web são cada vez mais direcionados por ataques maliciosos que exploram vulnerabilidades conhecidas comuns. A WAF do Gateway de aplicação baseia-se no Core regra definido (CRS) 3.0 ou 2.2.9 do Open Web Application Security Project (OWASP). O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades, com qualquer configuração adicional necessária. Alertas gerados por WAF são transmitidas ao centro de segurança. Para obter mais informações sobre os alertas gerados pelo WAF, veja este [artigo](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Ataques denial of service (DDoS) distribuídos são conhecidos por ser fácil de executar. Portanto, eles têm se tornado uma preocupação de segurança excelentes para os clientes que estão a mover seus aplicativos para a cloud. 

Um ataque de DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para legitimar utilizadores. Ataques de DDoS podem direcionar qualquer ponto final que pode ser contatado através da Internet.

A proteção contra DDoS do Azure, juntamente com práticas recomendadas do design de aplicativo, fornecem uma linha de defesa contra ataques DDoS. A proteção contra DDoS do Azure fornece diferentes escalões de serviço. Para obter mais informações, consulte [descrição geral do Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Padrão de proteção de DDoS capazes de atenuar os seguintes tipos de ataques:

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Ataque volumetric detetado**|O objetivo deste ataque é inundar da camada de rede com uma quantidade substancial de tráfego aparentemente legítimo. Ele inclui inundações UDP, inundações de amplificação e outras inundações de pacote falsificado. Padrão de proteção de DDoS atenua esses potenciais ataques de gigabytes multi, absorver e limpeza, com o dimensionamento de rede global do Azure, automaticamente.|
|**Ataque de protocolo detetado**|Esses ataques compor um destino inacessível, através da exploração de um ponto fraco na camada 3 e a pilha de protocolo 4 de camada. Ele inclui, ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. Padrão de proteção contra DDoS mitiga esses ataques, diferenciar entre o tráfego mal-intencionado e legítimo, ao interagir com o cliente e a bloquear tráfego malicioso.|
|**Ataque de camada de recursos (aplicação) detetado**|Esses ataques pacotes de aplicação web, para interromper a transmissão de dados entre anfitriões de destino. Os ataques incluem HTTP violações de protocolo, SQL injeção, scripts entre sites e outros ataques de camada 7. Utilize a firewall de aplicações web do Gateway de aplicação do Azure, com DDoS Protection padrão, se proteger contra esses ataques. Também existem ofertas de firewall de aplicação web de terceiros disponíveis no Azure Marketplace.|
