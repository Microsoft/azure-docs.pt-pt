---
title: Controlos de segurança para Azure Key Vault
description: Uma lista de controlos de segurança para avaliar o Cofre da Chave Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81429866"
---
# <a name="security-controls-for-azure-key-vault"></a>Controlos de segurança para Azure Key Vault

Este artigo documenta os controlos de segurança incorporados no Cofre da Chave Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Sim | Utilizando pontos finais de serviço de Rede Virtual (VNet). |
| Suporte à injeção VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | Usando as regras de firewall VNet. |
| Suporte de túneis forçados| Não |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Usando o Log Analytics. |
| Registo e auditoria do avião de controlo/gestão| Sim | Usando o Log Analytics. |
| Registo e auditoria de planos de dados| Sim | Usando o Log Analytics. |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Diretório Ativo Azure. |
| Autorização| Sim | Usando a política de acesso ao cofre de chaves. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | Todos os objetos estão encriptados. |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim | O cliente controla todas as chaves do cofre. Quando são especificadas as teclas com o módulo de segurança de hardware (HSM), um HSM de nível FIPS 2 protege a chave, certificado ou segredo. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | Toda a comunicação é através de chamadas encriptadas da API |
| Chamadas da API encriptadas| Sim | Utilizando HTTPS. |

## <a name="access-controls"></a>Controlos de acesso

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Controlo/Controlo de controlo de acesso a planos | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controlos de acesso a planos de dados (em todos os níveis de serviço) | Sim | Política de acesso a cofre chave |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)