---
title: Trabalhar com VMs e NSGs em Azure Bastion
description: Este artigo descreve como incorporar o acesso da NSG ao Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732201"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Trabalhar com o NSG access e azure Bastion

Ao trabalhar com o Azure Bastion, pode utilizar grupos de segurança de rede (NSGs). Para mais informações, consulte [Grupos de Segurança](../virtual-network/security-overview.md). 

![Arquitetura](./media/bastion-nsg/nsg-architecture.png)

Neste diagrama:

* O anfitrião bastião está implantado na rede virtual.
* O utilizador liga-se ao portal Azure utilizando qualquer navegador HTML5.
* O utilizador navega para a máquina virtual Azure para RDP/SSH.
* Connect Integration - Sessão RDP/SSH de clique único dentro do navegador
* Não é necessário ip público no Azure VM.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede

Esta secção mostra-lhe o tráfego de rede entre o utilizador e o Bastião Azure, e através de VMs-alvo na sua rede virtual:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

O Azure Bastion é implantado especificamente na ***AzureBastionSubnet.***

* **Tráfego ingress:**

   * **Tráfego ingresso da internet pública:** O Bastião Azure vai criar um IP público que precisa do porto 443 habilitado no IP público para o tráfego de ingressos. A porta 3389/22 não é necessária para ser aberta na AzureBastionSubnet.
   * **Tráfego ingresso do avião de controlo do Bastião Azure:** Para a conectividade do plano de controlo, ative a entrada da porta 443 a partir da etiqueta de serviço **GatewayManager.** Isto permite ao plano de controlo, isto é, gateway manager ser capaz de falar com Azure Bastion.

* **Tráfego de Egress:**

   * **Tráfego de Egress para os VMs-alvo:** O Azure Bastion vai atingir os VMs-alvo em IP privado. Os NSGs precisam de permitir o tráfego de saída para outras subredes VM-alvo para as portas 3389 e 22.
   * **Tráfego de Egress para outros pontos finais públicos em Azure:** O Azure Bastion precisa de ser capaz de se ligar a vários pontos finais públicos dentro do Azure (por exemplo, para armazenar registos de diagnóstico e registos de medição). Por esta razão, o Azure Bastion precisa de saída para 443 para a etiqueta de serviço **AzureCloud.**

### <a name="target-vm-subnet"></a>Subnet-alvo VM
Esta é a sub-rede que contém a máquina virtual alvo a que pretende rdp/SSH.

   * **Tráfego ingresso do Bastião Azure:** O Azure Bastion chegará ao vm alvo em vez de IP privado. As portas RDP/SSH (portas 3389/22 respectivamente) devem ser abertas do lado vM-alvo em relação ao IP privado. Como uma boa prática, você pode adicionar a gama de endereços IP Subnet Da Uigar IU nesta regra para permitir que apenas a Bastion possa abrir estas portas nos VMs-alvo na sua subnet VM alvo.


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Bastião Azure, consulte as [FAQ.](bastion-faq.md)
