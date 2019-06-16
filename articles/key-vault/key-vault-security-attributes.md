---
title: Atributos comuns de segurança para o Azure Key Vault
description: Uma lista de verificação de atributos comuns de segurança para avaliar o Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000184"
---
# <a name="security-attributes-for-azure-key-vault"></a>Atributos de segurança para o Azure Key Vault

Este artigo documenta os atributos de segurança incorporados no Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | Todos os objetos são encriptados. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Toda a comunicação é por meio de chamadas de API encriptadas |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | O cliente controla todas as chaves no seu Cofre de chaves. Quando são especificadas chaves de módulo (HSM) cópia de segurança de hardware, um HSM de 2 de nível de FIPS protege a chave, o certificado ou o segredo. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim | Através de HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Através de pontos finais de serviço de rede Virtual (VNet). |
| Suporte de injeção de VNet| Não |  |
| Isolamento de rede e o suporte de firewall| Sim | Usando as regras de firewall da VNet. |
| Suporte de encapsulamento de forçado| Não |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Utilizar o Log Analytics. |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Autorização| Sim | A utilizar a política de acesso do Cofre de chaves. |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão/controlo do plano de auditoria e Registro em log| Sim | Utilizar o Log Analytics. |
| Auditoria e registo de plano de dados| Sim | Utilizar o Log Analytics. |

## <a name="access-controls"></a>Controlos de acesso

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Controlos de acesso do plano de gestão/controlo | Sim | Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Controlos de acesso do plano de dados (em todos os níveis de serviço) | Sim | Política de acesso do Cofre de chaves |