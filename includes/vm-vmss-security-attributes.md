---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800279"
---
## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação em repouso (por exemplo, a encriptação do lado do servidor, a encriptação do lado do servidor com chaves geridas pelo cliente e a outros recursos de criptografia) | Sim | Ver [como encriptar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/encrypt-disks.md) e [encriptar discos virtuais numa VM do Windows](/azure/virtual-machines/windows/encrypt-disks.md). |
| Encriptação em trânsito (por exemplo, a encriptação do ExpressRoute, na encriptação de VNet e a encriptação de VNet a VNet)| Sim | O Azure suporta máquinas virtuais [ExpressRoute](/azure/expressroute) e encriptação de VNET. Ver [encryption em VMs em trânsito](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | Chaves geridas pelo cliente é um cenário de encriptação do Azure suportados; ver [descrição geral da encriptação do Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Através de HTTPS e SSL. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | |
| Suporte de injeção de VNet| Sim | . |
| Suporte de isolamento de rede e Firewalling| Sim |  |
| Suporte de encapsulamento de forçado| Sim | Ver [configurar imposição do túnel com o modelo de implementação Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Ver [monitorizar e atualizar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/tutorial-monitoring.md) e [monitorizar e atualizar uma máquina virtual do Windows no Azure](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim |  |
| Autorização| Sim |  |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim |  |
| Auditoria e registo de plano de dados | Não |  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim |  | 
