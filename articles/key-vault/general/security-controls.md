---
title: Controlos de segurança para cofre de chaves Azure
description: Uma lista de controlos de segurança para avaliar o Cofre chave Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429866"
---
# <a name="security-controls-for-azure-key-vault"></a>Controlos de segurança para cofre de chaves Azure

Este artigo documenta os controlos de segurança incorporados no Cofre chave Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim | Utilizando pontos finais de serviço de Rede Virtual (VNet). |
| Suporte à injeção VNet| Não |  |
| Isolamento da rede e suporte de firewalling| Sim | Usando as regras de firewall VNet. |
| Apoio de túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Usando log analytics. |
| Controlo/Gestão de Registos e Auditoria de planos de controlo/gestão| Sim | Usando log analytics. |
| Registo e auditoria de planos de dados| Sim | Usando log analytics. |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Diretório Ativo Azure. |
| Autorização| Sim | Usando a política de acesso ao cofre chave. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Todos os objetos estão encriptados. |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim | O cliente controla todas as chaves do cofre da chave. Quando são especificadas as teclas apoiadas pelo módulo de segurança do hardware (HSM), um HSM de nível 2 do FIPS protege a chave, o certificado ou o segredo. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | Toda a comunicação é através de chamadas API encriptadas |
| Chamadas api encriptadas| Sim | Utilizando HTTPS. |

## <a name="access-controls"></a>Controlos de acesso

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Controlo/Controlo dos controlos de acesso a planos | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controlos de acesso a planos de dados (em todos os níveis de serviço) | Sim | Política de acesso ao cofre chave |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../../security/fundamentals/security-controls.md)