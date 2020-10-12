---
title: Não é possível ligar-se aos VMs do Azure porque a porta RDP não está ativada no NSG Microsoft Docs
description: Saiba como resolver um problema em que o PDR falha por causa da configuração do NSG no portal Azure Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77918177"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Não é possível ligar-se remotamente a um VM porque a porta RDP não está ativada no NSG

Este artigo explica como resolver um problema em que não é possível ligar-se a uma máquina virtual (VM) do Azure Windows porque a porta do Protocolo de Ambiente de Trabalho Remoto (PDR) não está ativada no grupo de segurança da rede (NSG).


## <a name="symptom"></a>Sintoma

Não é possível escoar uma ligação RDP a um VM em Azure porque a porta RDP não está aberta no grupo de segurança da rede.

## <a name="solution"></a>Solução 

Quando cria um novo VM, todo o tráfego da Internet é bloqueado por padrão. 

Para ativar a porta RDP num NSG, siga estes passos:
1. Inscreva-se [no portal Azure](https://portal.azure.com).
2. Em **Máquinas Virtuais,** selecione o VM que tem o problema. 
3. Em **Definições**, selecione **Networking**. 
4. Nas **regras portuárias de entrada,** verifique se a porta para PDR está corretamente definida. Segue-se um exemplo da configuração: 

    **Prioridade**: 300 </br>
    **Nome**: Port_3389 </br>
    **Porto(Destino)**: 3389 </br>
    **Protocolo**: TCP </br>
    **Fonte**: Qualquer </br>
    **Destinos**: Qualquer </br>
    **Ação**: Permitir </br>

Se especificar o endereço IP de origem, esta definição permite que o tráfego a partir de um endereço IP específico ou intervalo de endereços IP se conecte ao VM. Certifique-se de que o computador que está a utilizar para iniciar a sessão RDP está dentro do alcance.

Para obter mais informações sobre os NSGs, consulte o [grupo de segurança da rede.](../../virtual-network/security-overview.md)

> [!NOTE]
> A porta RDP 3389 está exposta à Internet. Por isso, recomendamos que utilize esta porta apenas para ser recomendado para testes. Para ambientes de produção, recomendamos que utilize uma ligação VPN ou privada.

## <a name="next-steps"></a>Passos seguintes

Se a porta RDP já estiver ativada no NSG, consulte [Troubleshoot um erro geral de RDP em Azure VM](./troubleshoot-rdp-general-error.md).



