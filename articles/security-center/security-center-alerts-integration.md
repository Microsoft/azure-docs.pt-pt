---
title: Integração dos produtos de segurança do Azure na central de segurança do Azure | Microsoft Docs
description: Este tópico apresenta os produtos de segurança do Azure que foram integrados à central de segurança do Azure.
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
ms.author: v-mohabe
ms.openlocfilehash: beac7d08a973dd8dc7e450840669bfd5687e76ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013314"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integração dos produtos de segurança do Azure na central de segurança do Azure

A central de segurança do Azure fornece licenças adicionais da Microsoft para trabalhar com os seguintes produtos de segurança:

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

O Gateway de Aplicação do Azure oferece uma firewall de aplicações Web (WAF) que fornece proteção centralizada para as suas aplicações Web contra exploits e vulnerabilidades comuns.

Os aplicativos Web são cada vez mais direcionados a ataques mal-intencionados que exploram vulnerabilidades comumente conhecidas. O WAF do gateway de aplicativo baseia-se no conjunto de regras principais 3,0 ou 2.2.9 do projeto de segurança de aplicativo Web aberto. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades, sem a necessidade de configuração adicional. Os alertas do WAF são transmitidos para a central de segurança. Para obter mais informações sobre os alertas gerados pelo WAF, consulte [regras e grupos de regras CRS do firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Proteção contra DDoS do Azure<a name="azure-ddos"></a>

Ataques de DDoS (negação de serviço distribuído) são conhecidos por serem fáceis de executar. Eles se tornaram uma grande preocupação de segurança, especialmente se você estiver movendo seus aplicativos para a nuvem. 

Um ataque de DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para legitimar utilizadores. Os ataques de DDoS podem ter como destino qualquer ponto de extremidade que possa ser acessado pela Internet.

A proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, fornece uma defesa contra ataques de DDoS. A proteção contra DDoS fornece diferentes camadas de serviço. Para obter mais informações, consulte [visão geral da proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

A proteção contra DDoS Standard pode reduzir os seguintes tipos de ataques:

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Ataque de volumétricos detectado**|O objetivo desse ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Ele inclui inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A proteção contra DDoS Standard reduz esses ataques potenciais de vários gigabytes, exportando-os e Depurando-os com escala de rede global, automaticamente.|
|**Ataque de protocolo detectado**|Esses ataques renderizam um destino inacessível, explorando um ponto fraco nas pilhas de protocolo da camada 3 e da camada 4. Ele inclui ataques de inundação de SYN, ataques de reflexo e outros ataques de protocolo. A proteção contra DDoS Standard atenua esses ataques, diferenciando o tráfego mal-intencionado e legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado.|
|**Ataque de camada de recurso (aplicativo) detectado**|Esses ataques têm como alvo pacotes de aplicativos Web, para interromper a transmissão de dados entre hosts. Os ataques incluem violações de protocolo HTTP, injeção de SQL, script entre sites e outros ataques de camada 7. Use o Aplicativo Azure gateway WAF, com a proteção contra DDoS Standard, para se defender contra esses ataques. Também há ofertas de WAF de terceiros disponíveis no Azure Marketplace.|
