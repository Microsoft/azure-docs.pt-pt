---
title: Não é possível ligar a VMs Azure porque a porta RDP não está ativada no NSG [ Microsoft Docs
description: Saiba como resolver um problema em que o PDR falha devido à configuração do NSG no portal Azure . Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 6c877690cf27edd73f1d828b8a1dda6f4f34e780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918177"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Não é possível ligar remotamente a um VM porque a porta RDP não está ativada no NSG

Este artigo explica como resolver um problema em que não é possível ligar-se a uma máquina virtual Do Windows (VM) porque a porta do Protocolo de Ambiente de Trabalho Remoto (RDP) não está ativada no grupo de segurança da rede (NSG).


## <a name="symptom"></a>Sintoma

Não é possível fazer uma ligação RDP a um VM em Azure porque a porta RDP não está aberta no grupo de segurança da rede.

## <a name="solution"></a>Solução 

Quando cria um novo VM, todo o tráfego da Internet é bloqueado por padrão. 

Para permitir a porta RDP num NSG, siga estes passos:
1. Inscreva-se [no portal Azure.](https://portal.azure.com)
2. Em **Máquinas Virtuais,** selecione o VM que tem o problema. 
3. Em **Definições,** selecione **Networking**. 
4. Nas **regras da porta de entrada,** verifique se a porta para RDP está corretamente definida. Segue-se um exemplo da configuração: 

    **Prioridade**: 300 </br>
    **Nome**: Port_3389 </br>
    **Porto:** 3389 </br>
    **Protocolo**: TCP </br>
    **Fonte**: Qualquer </br>
    **Destinos**: Qualquer </br>
    **Ação**: Permitir </br>

Se especificar o endereço IP de origem, esta definição permite que o tráfego apenas a partir de um endereço IP específico ou de endereços IP se ligue ao VM. Certifique-se de que o computador que está a utilizar para iniciar a sessão de RDP está dentro do alcance.

Para obter mais informações sobre nsgs, consulte [o grupo de segurança da rede](../../virtual-network/security-overview.md).

> [!NOTE]
> A porta RDP 3389 está exposta à Internet. Por isso, recomendamos que utilize esta porta apenas para ser recomendado para testes. Para ambientes de produção, recomendamos que utilize uma VPN ou uma ligação privada.

## <a name="next-steps"></a>Passos seguintes

Se a porta RDP já estiver ativada no NSG, consulte [Troubleshoot um erro geral rdp no Azure VM](./troubleshoot-rdp-general-error.md).



