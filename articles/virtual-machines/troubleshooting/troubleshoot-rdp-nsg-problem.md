---
title: Não é possível ligar a VMs do Azure, porque a porta RDP não está ativada no NSG | Documentos da Microsoft
description: Saiba como resolver um problema em que o RDP falha devido a configuração de NSG no portal do Azure | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318940"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Não é possível ligar remotamente a uma VM porque a porta RDP não está ativada no NSG

Este artigo explica como resolver um problema em que não é possível ligar-se a uma máquina virtual do Windows Azure (VM) porque a porta de protocolo RDP (Remote Desktop) não está ativada no grupo de segurança de rede (NSG).


> [!NOTE] 
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Recomendamos que utilize o modelo de implementação do Resource Manager em vez do modelo de implementação clássica para novas implementações. 

## <a name="symptom"></a>Sintoma

Não pode fazer uma ligação RDP a uma VM no Azure, porque a porta RDP não está aberta no grupo de segurança de rede.

## <a name="solution"></a>Solução 

Quando cria uma nova VM, todo o tráfego da Internet é bloqueado por predefinição. 

Para ativar a porta RDP num NSG, siga estes passos:
1. Inicie sessão no [do portal do Azure](https://portal.azure.com).
2. Na **máquinas virtuais**, selecione a VM que tem o problema. 
3. Na **configurações**, selecione **Networking**. 
4. Na **regras de porta de entrada**, verifique se a porta para RDP está definida corretamente. Segue-se um exemplo da configuração: 

    **Prioridade**: 300 </br>
    **Porta**: 3389 </br>
    **Nome**: Port_3389 </br>
    **Porta**: 3389 </br>
    **Protocolo**: TCP </br>
    **origem**: Qualquer </br>
    **Destinos**: Qualquer </br>
    **Ação**: Permitir </br>

Se especificar o endereço IP de origem, esta definição permite o tráfego apenas a partir de um endereço IP específico ou intervalo de endereços IP para ligar à VM. Certifique-se de que o computador que estiver a utilizar para iniciar a sessão do RDP está dentro do intervalo.

Para obter mais informações sobre NSGs, consulte [grupo de segurança de rede](../../virtual-network/security-overview.md).

> [!NOTE]
> A porta RDP 3389 está exposta à Internet. Por conseguinte, recomendamos que utilize esta porta apenas para que recomendado para teste. Para ambientes de produção, recomendamos que utilize uma VPN ou ligação privada.

## <a name="next-steps"></a>Passos Seguintes

Se a porta RDP já estiver ativada no NSG, consulte [resolver problemas relacionados com um erro geral do RDP na VM do Azure](./troubleshoot-rdp-general-error.md).



