---
title: Secure Azure centros virtuais usando Check Point Cloudguard Connect
description: Saiba mais sobre Check Point CloudGuard Connect para proteger os centros virtuais do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146863"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Proteger hubs virtuais com o Check Point Cloudguard Connect

Check Point CloudGuard Connect é um parceiro de segurança fidedigno no Azure Firewall Manager. Protege a sucursal distribuída globalmente pela Internet (B2I) ou rede virtual para ligações à Internet (V2I) com prevenção avançada de ameaças. 

Com uma configuração simples no Azure Firewall Manager, pode encaminhar o hub de ramificação e as ligações de rede virtual para a Internet através da segurança CloudGuard Connect como um serviço (SECaaS). O tráfego está protegido em trânsito do seu hub para o serviço de nuvem Check Point nos túneis VPN IPsec.

Quando ativar a sincronização automática no portal Check Point, qualquer recurso marcado como *protegido* no portal Azure é automaticamente protegido. Não tens de gerir os teus bens duas vezes. Simplesmente escolhes prendê-los uma vez no portal Azure.

Check Point unifica vários serviços de segurança sob um guarda-chuva. O tráfego de segurança integrado é desencriptado uma vez e inspecionado num único passe. O Controlo de Aplicações, a Filtragem de URL e a Consciência de Conteúdo (DLP) impõem uma utilização segura da web e protegem os seus dados. IPS e Antivírus protegem os utilizadores de explorações de rede conhecidas. O Anti-Bot bloqueia as ligações aos servidores de Comando e Controlo e alerta-o se um hospedeiro estiver infetado.

A Threat Emulation (sandboxing) protege os utilizadores de ameaças desconhecidas e de zero dias. Check Point SandBlast Zero-Day Protection é uma tecnologia de boxe de areia hospedada em nuvem onde os ficheiros são rapidamente colocados em quarentena e inspecionados. Funciona numa caixa de areia virtual para descobrir comportamentos maliciosos antes de entrar na sua rede. Evita ameaças antes de os danos serem causados para poupar tempo valioso ao pessoal para responder a ameaças. 

## <a name="deployment-example"></a>Exemplo de implantação

Veja o seguinte vídeo para ver como implementar o Check Point CloudGuard Connect como um parceiro de segurança fidedigno da Azure.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Passos seguintes

- [Implementar um fornecedor de parceiros de segurança](deploy-trusted-security-partner.md)