---
title: Problemas de conectividade entre Os VMs do Azure Microsoft Docs
description: Saiba como resolver os problemas de conectividade entre Os VMs Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71056806"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Troubleshooting connectivity problems between Azure VMs (Resolver problemas de conectividade entre VMs do Azure)

Pode experimentar problemas de conectividade entre máquinas virtuais Azure (VMs). Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Um Azure VM não pode ligar-se a outro Azure VM.

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se nic está mal configurado](#step-1-check-whether-nic-is-misconfigured)
2. [Verifique se o tráfego de rede está bloqueado por NSG ou UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Verifique se o tráfego de rede está bloqueado por firewall VM](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Verifique se a aplicação ou serviço VM está a ouvir na porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Verifique se o problema é causado pela SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Verifique se o tráfego está bloqueado por ACLs para o Clássico VM](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Verifique se o ponto final é criado para o Clássico VM](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Tente ligar-se a uma partilha de rede VM](#step-8-try-to-connect-to-a-vm-network-share)
9. [Verifique a conectividade Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Siga estes passos para resolver o problema. Depois de completar cada passo, verifique se o problema está resolvido. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Passo 1: Verifique se nic está mal configurado

Siga os passos em [Como redefinir a interface de rede para o Azure Windows VM](../virtual-machines/windows/reset-network-interface.md). 

Se o problema ocorrer depois de modificar a interface de rede (NIC), siga estes passos:

**Multi-NIC VMs**

1. Adicione um NIC.
2. Corrija os problemas no NIC mau ou retire o NIC mau.  Em seguida, adicione o NIC novamente.

Para mais informações, consulte Adicionar interfaces de [rede para ou remover de máquinas virtuais](virtual-network-network-interface-vm.md).

**Single-NIC VM** 

- [Reimplantar O VM do Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Reimplantação de Linux VM](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Passo 2: Verifique se o tráfego de rede está bloqueado por NSG ou UDR

Utilize o [Verificador](../network-watcher/network-watcher-ip-flow-verify-overview.md) de Fluxo IP do Observador de Rede e o [Registo de Fluxo nsg](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se existe um Grupo de Segurança de Rede (NSG) ou uma Rota Definida pelo Utilizador (UDR) que está a interferir com o fluxo de tráfego.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Passo 3: Verifique se o tráfego de rede está bloqueado por firewall VM

Desative a firewall e, em seguida, teste o resultado. Se o problema estiver resolvido, verifique as definições de firewall e, em seguida, reative a firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Passo 4: Verifique se a aplicação ou o serviço VM estão a ouvir na porta

Pode utilizar um dos seguintes métodos para verificar se a aplicação ou serviço VM está a ouvir na porta.

- Execute os seguintes comandos para verificar se o servidor está a ouvir nessa porta.

**VM do Windows**

    netstat –ano

**VM do Linux**

    netstat -l

- Mande o comando da **rede** na própria máquina virtual para testar a porta. Se o teste falhar, a aplicação ou serviço não está configurado para ouvir nessa porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Passo 5: Verifique se o problema é causado pela SNAT

Em alguns cenários, o VM é colocado atrás de uma solução de equilíbrio de carga que tem uma dependência de recursos fora do Azure. Nestes cenários, se tiver problemas de ligação intermitentes, o problema pode ser causado pela exaustão da [porta SNAT](../load-balancer/load-balancer-outbound-connections.md). Para resolver o problema, crie um VIP (ou ILPIP para clássico) para cada VM que esteja por trás do equilibrador de carga e proteja com NSG ou ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Passo 6: Verifique se o tráfego está bloqueado por ACLs para o clássico VM

Uma lista de controlo de acesso (ACL) fornece a capacidade de permitir ou negar seletivamente o tráfego para um ponto final de máquina virtual. Para mais informações, consulte [Gerir o ACL num ponto final](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Passo 7: Verifique se o ponto final é criado para o Clássico VM

Todos os VMs que cria em Azure utilizando o modelo de implementação clássico podem comunicar automaticamente através de um canal de rede privado com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual. No entanto, os computadores de outras redes virtuais requerem pontos finais para direcionar o tráfego da rede de entrada para uma máquina virtual. Para mais informações, consulte [Como configurar pontos finais](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Passo 8: Tente ligar-se a uma partilha de rede VM

Se não conseguir ligar-se a uma partilha de rede VM, o problema pode ser causado por NICs indisponíveis no VM. Para eliminar os NICs indisponíveis, consulte [Como eliminar os NICs indisponíveis](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Passo 9: Verificar conectividade Inter-Vnet

Utilize o [Verificador de Fluxo IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) do Observador de Rede e o [Registo de Fluxo nsg](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se existe um NSG ou UDR que está a interferir com o fluxo de tráfego. Também pode verificar a sua configuração Inter-Vnet [aqui](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.