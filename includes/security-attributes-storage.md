---
author: msmbaldwin
ms.service: storage
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: b242bda524c747b28453061c797afde02cf6f455
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007625"
---
## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim |  |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim | Suporte a mecanismos de HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de ser transmitido para o serviço. |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Ver [encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](../articles/storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A |  |
| Chamadas de API encriptadas| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| suporte de Injeção de vNET| N/A |  |
| Isolamento de rede / suporte de firewall| Sim | |
| Suporte para protocolo de túnel forçado | N/A |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Métricas do Azure Monitor disponíveis agora, registos de partida da pré-visualização |

## <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão de acesso - autenticação| Sim | O Azure Active Directory, chave partilhada, o token de acesso partilhado. |
| Gestão de acesso - autorização| Sim | Autorização de suporte através do RBAC, POSIX ACLs e os Tokens SAS |


## <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Planear a gestão/controlo e auditoria do registo | Sim | Registo de atividades do Gestor de recursos do Azure |
| Registo e auditoria do plano de dados| Sim | Os registos de diagnóstico do serviço e o registo do Azure Monitor partida pré-visualização  |

## <a name="configuration-management"></a>Gestão da Configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Suporte de controle de versão do fornecedor de recursos através de APIs do Azure Resource Manager |