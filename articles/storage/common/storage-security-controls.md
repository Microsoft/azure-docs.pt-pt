---
title: Controles de segurança para o armazenamento do Azure
description: Uma lista de verificação de controles de segurança para avaliar o armazenamento do Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886350"
---
# <a name="security-controls-for-azure-storage"></a>Controles de segurança para o armazenamento do Azure

Este artigo documenta os controles de segurança incorporados ao armazenamento do Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | Sim |  |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | Consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Suporte a mecanismos HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de serem transmitidos para o serviço. |
| Chamadas de API criptografadas| Sim |  |

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| N/A |  |
| Isolamento de rede e suporte de firewall| Sim | |
| Suporte a túnel forçado| N/A |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Azure Monitor métricas disponíveis agora, logs iniciando a visualização |
| Registro e auditoria do plano de gerenciamento e controle | Sim | Log de atividades Azure Resource Manager |
| Log e auditoria do plano de dados| Sim | Logs de diagnóstico de serviço e log de Azure Monitor iniciando a visualização  |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Azure Active Directory, chave compartilhada, token de acesso compartilhado. |
| Autorização| Sim | Suporte à autorização via RBAC, ACLs POSIX e tokens SAS |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Suporte ao controle de versão do provedor de recursos por meio de APIs de Azure Resource Manager |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../../security/fundamentals/security-controls.md).