---
title: Problemas de conectividade de resolução de problemas entre os VMs do Azure | Microsoft Docs
description: Aprenda a resolver problemas e resolva os problemas de conectividade que poderá experimentar entre os VMs Azure.
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
ms.openlocfilehash: 6ece3e639e0ef3516696e2a0bad7deeb833433a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219203"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Troubleshooting connectivity problems between Azure VMs (Resolver problemas de conectividade entre VMs do Azure)

Pode sentir problemas de conectividade entre máquinas virtuais Azure (VMs). Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Um Azure VM não pode ligar-se a outro Azure VM.

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se nic está mal configurado](#step-1-check-whether-nic-is-misconfigured)
2. [Verifique se o tráfego de rede está bloqueado pela NSG ou pela UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Verifique se o tráfego de rede está bloqueado por firewall VM](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Verifique se a aplicação ou serviço VM está a ouvir na porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Verifique se o problema é causado pelo SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Verifique se o tráfego está bloqueado pelos ACLs para o clássico VM](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Verifique se o ponto final é criado para o VM clássico](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Tente ligar-se a uma partilha de rede VM](#step-8-try-to-connect-to-a-vm-network-share)
9. [Verifique a conectividade Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Siga estes passos para resolver o problema. Depois de completar cada passo, verifique se o problema está resolvido. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Passo 1: Verifique se o NIC está mal configurado

Siga os passos em [Como redefinir a interface de rede para O Azure Windows VM](../virtual-machines/troubleshooting/reset-network-interface.md). 

Se o problema ocorrer depois de modificar a interface de rede (NIC), siga estes passos:

**Multi-NIC VMs**

1. Adicione um NIC.
2. Corrija os problemas no NIC mau ou remova o NIC mau.  Em seguida, adicione o NIC novamente.

Para obter mais informações, consulte [adicionar interfaces de rede ou remover de máquinas virtuais.](virtual-network-network-interface-vm.md)

**Single-NIC VM** 

- [Reimplantia Janelas VM](../virtual-machines/troubleshooting/redeploy-to-new-node-windows.md)
- [Reposicionar Linux VM](../virtual-machines/troubleshooting/redeploy-to-new-node-linux.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Passo 2: Verifique se o tráfego da rede está bloqueado pela NSG ou pela UDR

Utilize [o Verificador de Fluxo IP do Observador de Rede](../network-watcher/network-watcher-ip-flow-verify-overview.md) e registo de fluxo [NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se existe um Grupo de Segurança de Rede (NSG) ou User-Defined Rota (UDR) que esteja a interferir com o fluxo de tráfego.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Passo 3: Verifique se o tráfego de rede está bloqueado pela firewall VM

Desative a firewall e, em seguida, teste o resultado. Se o problema for resolvido, verifique as definições da firewall e, em seguida, re-activar a firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Passo 4: Verifique se a aplicação vM ou o serviço estão a ouvir na porta

Pode utilizar um dos seguintes métodos para verificar se a aplicação ou serviço VM está a ouvir na porta.

- Executar os seguintes comandos para verificar se o servidor está a ouvir nessa porta.

**VM do Windows**

```console
netstat –ano
```

**VM do Linux**

```console
netstat -l
```

- Executar o comando **telnet** na própria máquina virtual para testar a porta. Se o teste falhar, a aplicação ou o serviço não estão configurados para ouvir na porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Passo 5: Verifique se o problema é causado pelo SNAT

Em alguns cenários, o VM é colocado atrás de uma solução de equilíbrio de carga que tem uma dependência de recursos fora de Azure. Nestes cenários, se tiver problemas de ligação intermitentes, o problema pode ser causado pela [exaustão da porta SNAT](../load-balancer/load-balancer-outbound-connections.md). Para resolver o problema, crie um VIP (ou ILPIP para clássico) para cada VM que esteja por trás do equilibrista de carga e seguro com NSG ou ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Passo 6: Verifique se o tráfego está bloqueado pelos ACLs para o clássico VM

Uma lista de controlo de acesso (ACL) fornece a capacidade de permitir ou negar seletivamente o tráfego para um ponto final de máquina virtual. Para obter mais informações, consulte [Gerir a ACL num ponto final.](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Passo 7: Verifique se o ponto final foi criado para o VM clássico

Todos os VMs que cria no Azure utilizando o modelo de implementação clássico podem comunicar automaticamente através de um canal de rede privado com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual. No entanto, os computadores de outras redes virtuais requerem pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Para obter mais informações, consulte [Como configurar pontos finais.](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints)

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Passo 8: Tente ligar-se a uma partilha de rede VM

Se não conseguir ligar-se a uma partilha de rede VM, o problema pode ser causado por NICs indisponíveis no VM. Para eliminar os NICs indisponíveis, consulte [Como eliminar os NICs indisponíveis](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Passo 9: Verifique Inter-Vnet conectividade

Utilize [o Verificador de Fluxo IP do Observador de Rede](../network-watcher/network-watcher-ip-flow-verify-overview.md) e registo de fluxo [NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se existe um NSG ou UDR que esteja a interferir com o fluxo de tráfego. Também pode verificar aqui a [sua](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)configuração de Inter-Vnet .

### <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.