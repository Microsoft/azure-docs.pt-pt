---
title: Solucionar problemas de conectividade entre VMs do Azure | Microsoft Docs
description: Saiba como solucionar problemas de conectividade entre VMs do Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ab3ae45081ecc481cb90af8961174e23c86e84b5
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056806"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Solucionando problemas de conectividade entre VMs do Azure

Você pode enfrentar problemas de conectividade entre as VMs (máquinas virtuais) do Azure. Este artigo fornece etapas de solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Uma VM do Azure não pode se conectar a outra VM do Azure.

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se a NIC está configurada incorretamente](#step-1-check-whether-nic-is-misconfigured)
2. [Verifique se o tráfego de rede está bloqueado por NSG ou UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Verifique se o tráfego de rede está bloqueado pelo firewall da VM](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Verificar se o aplicativo ou serviço de VM está escutando na porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Verifique se o problema é causado por SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Verificar se o tráfego está bloqueado pelas ACLs para a VM clássica](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Verificar se o ponto de extremidade foi criado para a VM clássica](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Tentar se conectar a um compartilhamento de rede de VM](#step-8-try-to-connect-to-a-vm-network-share)
9. [Verificar conectividade entre vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Siga estas etapas para solucionar o problema. Depois de concluir cada etapa, verifique se o problema foi resolvido. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Passo 1: Verifique se a NIC está configurada incorretamente

Siga as etapas em [como redefinir a interface de rede para a VM do Windows do Azure](../virtual-machines/windows/reset-network-interface.md). 

Se o problema ocorrer depois que você modificar a NIC (interface de rede), siga estas etapas:

**VMs com várias NICs**

1. Adicione uma NIC.
2. Corrija os problemas na NIC inadequada ou remova a NIC inadequada.  Em seguida, adicione a NIC novamente.

Para obter mais informações, consulte [Adicionar adaptadores de rede ou remover de máquinas virtuais](virtual-network-network-interface-vm.md).

**VM de NIC única** 

- [Reimplantar a VM do Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Reimplantar VM Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Passo 2: Verifique se o tráfego de rede está bloqueado por NSG ou UDR

Use a [verificação de fluxo de IP do observador de rede](../network-watcher/network-watcher-ip-flow-verify-overview.md) e o log de [fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se há um NSG (grupo de segurança de rede) ou UDR (rota definida pelo usuário) que está interferindo com o fluxo de tráfego.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Passo 3: Verifique se o tráfego de rede está bloqueado pelo firewall da VM

Desabilite o firewall e, em seguida, teste o resultado. Se o problema for resolvido, verifique as configurações do firewall e habilite novamente o firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Passo 4: Verificar se o aplicativo ou serviço de VM está escutando na porta

Você pode usar um dos métodos a seguir para verificar se o aplicativo ou serviço de VM está escutando na porta.

- Execute os comandos a seguir para verificar se o servidor está escutando nessa porta.

**VM do Windows**

    netstat –ano

**VM do Linux**

    netstat -l

- Execute o comando **Telnet** na própria máquina virtual para testar a porta. Se o teste falhar, o aplicativo ou serviço não será configurado para escutar nessa porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Passo 5: Verifique se o problema é causado por SNAT

Em alguns cenários, a VM é colocada atrás de uma solução de balanceamento de carga que tem uma dependência de recursos fora do Azure. Nesses cenários, se você tiver problemas intermitentes de conexão, o problema poderá ser causado pelo [esgotamento da porta SNAT](../load-balancer/load-balancer-outbound-connections.md). Para resolver o problema, crie um VIP (ou ILPIP para clássico) para cada VM que está atrás do balanceador de carga e proteja-se com NSG ou ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Passo 6: Verificar se o tráfego está bloqueado pelas ACLs para a VM clássica

Uma ACL (lista de controle de acesso) fornece a capacidade de permitir ou negar seletivamente o tráfego para um ponto de extremidade de máquina virtual. Para obter mais informações, consulte [gerenciar a ACL em um ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Passo 7: Verificar se o ponto de extremidade foi criado para a VM clássica

Todas as VMs que você cria no Azure usando o modelo de implantação clássico podem se comunicar automaticamente por meio de um canal de rede privada com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual. No entanto, computadores em outras redes virtuais exigem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Para obter mais informações, consulte [como configurar pontos de extremidade](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Passo 8: Tentar se conectar a um compartilhamento de rede de VM

Se você não puder se conectar a um compartilhamento de rede VM, o problema poderá ser causado por NICs não disponíveis na VM. Para excluir as NICs não disponíveis, consulte [como excluir as NICs não disponíveis](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Passo 9: Verificar conectividade entre vnet

Use a [verificação de fluxo de IP do observador de rede](../network-watcher/network-watcher-ip-flow-verify-overview.md) e o log de [fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se há um NSG ou UDR que está interferindo no fluxo de tráfego. Você também pode verificar sua configuração entre vnet [aqui](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.