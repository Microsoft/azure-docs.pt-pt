---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 520fd50b2b0864f43c08687f05de377679b36d84
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886933"
---
## <a name="preventative"></a>Preventiva

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | Sim | Consulte [como criptografar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/encrypt-disks) e [criptografar discos virtuais em uma VM do Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | As máquinas virtuais do Azure dão suporte à criptografia de [ExpressRoute](/azure/expressroute) e VNet. Consulte [criptografia em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | As chaves gerenciadas pelo cliente são um cenário de criptografia do Azure com suporte; consulte [visão geral da criptografia do Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Via HTTPS e SSL. |

## <a name="network-segmentation"></a>Segmentação de rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | |
| Suporte à injeção de VNet| Sim | . |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Sim | Consulte [Configurar o túnel forçado usando o modelo de implantação Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="detection"></a>Deteção

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitorar e atualizar uma máquina virtual Linux no Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [monitorar e atualizar uma máquina virtual do Windows no Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim |  |
| Autorização| Sim |  |


## <a name="audit-trail"></a>Trilha de auditoria

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados | Não |  |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  | 
