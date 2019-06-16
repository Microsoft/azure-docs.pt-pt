---
title: Atributos de segurança para o Gateway de VPN do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o Gateway de VPN do Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083135"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Atributos de segurança para o Gateway de VPN do Azure

Este artigo documenta os atributos de segurança comuns incorporados no Gateway de VPN do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação em repouso (por exemplo, a encriptação do lado do servidor, a encriptação do lado do servidor com chaves geridas pelo cliente e a outros recursos de criptografia) | N/A | Dados de clientes de trânsito de gateway VPN, não armazena dados do cliente |
| Encriptação em trânsito (por exemplo, a encriptação do ExpressRoute, na encriptação de VNet e a encriptação de VNet a VNet)| Sim | Gateway de VPN encriptar pacotes de cliente entre os gateways de VPN do Azure e de dispositivos VPN do cliente no local (S2S) ou de clientes VPN (P2S). Gateways de VPN também suportam a encriptação de VNet a VNet. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não | Chaves pré-partilhadas especificada do cliente são encriptadas em inatividade; mas não integrado com CMK ainda. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Por meio [o Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| N/A | |
| Suporte de injeção de VNet| N/A | . |
| Suporte de isolamento de rede e Firewalling| Sim | Gateways de VPN são instâncias VM dedicadas para cada rede Virtual do cliente  |
| Suporte de encapsulamento de forçado| Sim |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Ver [registos de diagnóstico do Azure Monitor/alerta](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor/alerta de métricas](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [O Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerir o serviço e configurar o gateway de VPN do Azure. |
| Autorização| Sim | Suporta a autorização via [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Registo de atividades do Gestor de recursos do Azure. |
| Auditoria e registo de plano de dados | Sim | [Registos de diagnóstico do Azure Monitor](../azure-resource-manager/resource-group-audit.md) para conectividade VPN, registro e auditoria. |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gestão, o estado de uma configuração de gateway de VPN do Azure pode ser exportado como um modelo Azure Resource Manager e com a versão ao longo do tempo. | 