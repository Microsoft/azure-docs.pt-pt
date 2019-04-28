---
title: Resolução de problemas de conectividade entre as VMs do Azure | Documentos da Microsoft
description: Saiba como resolver os problemas de conectividade entre as VMs do Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036390"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Resolução de problemas de conectividade entre as VMs do Azure

Podem ocorrer problemas de conectividade entre máquinas virtuais do Azure (VMs). Este artigo fornece passos de resolução de problemas para o ajudar a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Uma que VM do Azure não é possível ligar a outra VM do Azure.

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se a NIC está configurado incorretamente](#step-1-check-whether-nic-is-misconfigured)
2. [Verifique se o tráfego de rede está bloqueado por NSG ou UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Verifique se o tráfego de rede é bloqueado pela firewall VM](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Verifique se a aplicação VM ou serviço está a escutar na porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Verifique se o problema foi causado pelo SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Verifique se o tráfego está bloqueado por ACLs para a VM clássica](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Verifique se o ponto final é criado para a VM clássica](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Tentando estabelecer ligação a uma partilha de rede VM](#step-8-try-to-connect-to-a-vm-network-share)
9. [Verifique a conectividade Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Siga estes passos para resolver o problema. Depois de completar cada passo, verifique se o problema está resolvido. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Passo 1: Verifique se a NIC está configurado incorretamente

Siga os passos em [como repor a interface de rede VM do Azure Windows](../virtual-machines/windows/reset-network-interface.md). 

Se o problema ocorrer depois de modificar a interface de rede (NIC), siga estes passos:

**VMs de multi-NIC**

1. Adicionar uma NIC.
2. Corrija os problemas no NIC incorreto ou remover o NIC ruim.  Em seguida, adicione novamente a NIC.

Para obter mais informações, consulte [interfaces de rede para adicionar ou remover das máquinas virtuais](virtual-network-network-interface-vm.md).

**VM de NIC único** 

- [Reimplementar VM do Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Reimplementar VM do Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Passo 2: Verifique se o tráfego de rede está bloqueado por NSG ou UDR

Uso [verificação do fluxo de rede observador IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) e [registo de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se é um grupo de segurança de rede (NSG) ou definidas pelo utilizador rota (UDR) que está a interferir com o fluxo de tráfego.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Passo 3: Verifique se o tráfego de rede é bloqueado pela firewall VM

Desabilitar o firewall e, em seguida, o resultado do teste. Se o problema for resolvido, verifique as definições de firewall e, em seguida, volte a ativar a firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Passo 4: Verifique se a aplicação VM ou serviço está a escutar na porta

Pode utilizar um dos seguintes métodos para verificar se a aplicação VM ou o serviço está a escutar na porta.

- Execute os seguintes comandos para verificar se o servidor está à escuta nessa porta.

**VM do Windows**

    netstat –ano

**VM do Linux**

    netstat -l

- Executar o **telnet** comando na máquina virtual-se para a porta de teste. Se o teste falhar, a aplicação ou serviço não está configurado para escutar essa porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Passo 5: Verifique se o problema foi causado pelo SNAT

Em alguns cenários, a VM é colocada atrás de uma solução de balanceamento de carga que tem uma dependência em recursos fora do Azure. Nestes cenários, se tiver problemas de ligação intermitentes, o problema pode ser causado por [exaustão de porta SNAT](../load-balancer/load-balancer-outbound-connections.md). Para resolver o problema, crie um VIP (ou ILPIP para a clássica) para cada VM que está por detrás do Balanceador de carga e proteger com NSG ou a ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Passo 6: Verifique se o tráfego está bloqueado por ACLs para a VM clássica

Uma lista de controlo de acesso (ACL) fornece a capacidade para seletivamente permitir ou negar o tráfego para um ponto de final de máquina virtual. Para obter mais informações, consulte [gerir a ACL num ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Passo 7: Verifique se o ponto final é criado para a VM clássica

Todas as VMs que criar no Azure, utilizando o modelo de implementação clássica podem automaticamente comunicar através de um canal de rede privada com outras máquinas virtuais no mesmo serviço cloud ou rede virtual. No entanto, os computadores em outras redes virtuais requerem pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Para obter mais informações, consulte [como configurar pontos finais](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Passo 8: Tentando estabelecer ligação a uma partilha de rede VM

Se não conseguir ligar-se para uma partilha de rede VM, o problema pode dever-se indisponível NICs na VM. Para eliminar os NICs indisponíveis, veja [como eliminar os NICs indisponíveis](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Passo 9: Verifique a conectividade Inter-Vnet

Uso [verificação do fluxo de rede observador IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) e [registo de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se é um NSG ou UDR que está a interferir com o fluxo de tráfego. Também pode verificar a configuração de Inter-Vnet [aqui](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.