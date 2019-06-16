---
title: Atributos comuns de segurança para o Azure ExpressRoute
description: Uma lista de verificação de atributos comuns de segurança para avaliar o Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083278"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Atributos comuns de segurança para o Azure ExpressRoute

A segurança integra todos os aspectos de um serviço do Azure. Este artigo documenta os atributos comuns de segurança integrados do Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>|  N/A | ExpressRoute não armazena dados do cliente. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Não | |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| N/A |  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Por meio [o Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| N/A |  |
| suporte de injeção de vNET| N/A | |
| Isolamento de rede e o suporte de firewall| Sim | Cada cliente está contido em seu próprio domínio de encaminhamento e de túnel para a sua própria VNet |
| Suporte de encapsulamento de forçado| N/A | Por meio do Border Gateway Protocol (BGP). |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Ver [ExpressRoute monitorização, métricas e alertas](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Conta de serviço para o Gateway para a Microsoft (GWM) (controlador); Just-in-Time (JIT) de acesso para desenvolvimento e OP. |
| Autorização|  Sim |Conta de serviço para o Gateway para a Microsoft (GWM) (controlador); Just-in-Time (JIT) de acesso para desenvolvimento e OP. |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas| 
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim |  |
| Auditoria e registo de plano de dados| Não |   |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Através do fornecedor de recursos de rede (NRP). |
