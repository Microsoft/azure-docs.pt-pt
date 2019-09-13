---
title: Controles de segurança para Azure Key Vault
description: Uma lista de verificação de controles de segurança para avaliar Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886484"
---
# <a name="security-controls-for-azure-key-vault"></a>Controles de segurança para Azure Key Vault

Este artigo documenta os controles de segurança internos do Azure Key Vault. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Usando pontos de extremidade de serviço de rede virtual (VNet). |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | Usando regras de firewall de VNet. |
| Suporte a túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando Log Analytics. |
| Registro e auditoria do plano de gerenciamento/controle| Sim | Usando Log Analytics. |
| Log e auditoria do plano de dados| Sim | Usando Log Analytics. |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | Usando a política de acesso Key Vault. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | Sim | Todos os objetos são criptografados. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | O cliente controla todas as chaves em seu Key Vault. Quando as chaves com suporte ao módulo de segurança de hardware (HSM) são especificadas, um HSM de nível 2 do FIPS protege a chave, o certificado ou o segredo. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Toda a comunicação é por meio de chamadas de API criptografadas |
| Chamadas de API criptografadas| Sim | Usando HTTPS. |

## <a name="access-controls"></a>Controlos de acesso

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Controles de acesso do plano de gerenciamento/controle | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso de Key Vault |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).