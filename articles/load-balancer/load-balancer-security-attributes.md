---
title: Atributos de segurança para o Balanceador de carga do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o Balanceador de carga
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800084"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Atributos de segurança para o Balanceador de carga do Azure

Este artigo documenta os atributos de segurança comuns incorporados no balanceador de carga do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação em repouso (por exemplo, a encriptação do lado do servidor, a encriptação do lado do servidor com chaves geridas pelo cliente e a outros recursos de criptografia) | N/A | |
| Encriptação em trânsito (por exemplo, a encriptação do ExpressRoute, na encriptação de VNet e a encriptação de VNet a VNet)| N/A | |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| N/A | |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Através da [o Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| N/A | |
| Suporte de injeção de VNet| N/A | . |
| Suporte de isolamento de rede e Firewalling| N/A |  |
| Suporte de encapsulamento de forçado| N/A | |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Ver [registos do Azure Monitor para o Balanceador de carga básico público](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| N/A |  |
| Autorização| N/A |  |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Ver [registos do Azure Monitor para o Balanceador de carga básico público](load-balancer-monitor-log.md). |
| Auditoria e registo de plano de dados | N/A |  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| N/A |  | 
