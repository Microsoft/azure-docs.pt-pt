---
title: Trabalhar com VMs e NSGs em Azure Bastion
description: Este artigo descreve como incorporar o acesso da NSG ao Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087264"
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

## <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança da rede

Esta secção mostra-lhe o tráfego de rede entre o utilizador e o Bastião Azure, e através de VMs-alvo na sua rede virtual:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

O Azure Bastion está implantado especificamente na AzureBastionSubnet.

* **Tráfego ingress:**

   * **Tráfego ingresso da internet pública:** O Bastião Azure vai criar um IP público que precisa do porto 443 habilitado no IP público para o tráfego de ingressos. A porta 3389/22 não é necessária para ser aberta na AzureBastionSubnet.
   * **Tráfego ingresso do avião de controlo do Bastião Azure:** Para a conectividade do plano de controlo, ative a entrada da porta 443 a partir da etiqueta de serviço **GatewayManager.** Isto permite ao plano de controlo, isto é, gateway manager ser capaz de falar com Azure Bastion.

* **Tráfego de Egress:**

   * **Tráfego de Egress para os VMs-alvo:** O Azure Bastion vai atingir os VMs-alvo em IP privado. Os NSGs precisam de permitir o tráfego de saída para outras subredes VM-alvo para as portas 3389 e 22.
   * **Tráfego de Egress para outros pontos finais públicos em Azure:** O Azure Bastion precisa de ser capaz de se ligar a vários pontos finais públicos dentro do Azure (por exemplo, para armazenar registos de diagnóstico e registos de medição). Por esta razão, o Azure Bastion precisa de saída para 443 para a etiqueta de serviço **AzureCloud.**

* **Subnet-alvo VM:** Esta é a sub-rede que contém a máquina virtual alvo a que pretende rdp/SSH.

   * **Tráfego ingresso do Bastião Azure:** O Azure Bastion chegará ao vm alvo em vez de IP privado. As portas RDP/SSH (portas 3389/22 respectivamente) devem ser abertas do lado vM-alvo em relação ao IP privado. Como uma boa prática, você pode adicionar a gama de endereços IP Subnet Da Uigar IU nesta regra para permitir que apenas a Bastion possa abrir estas portas nos VMs-alvo na sua subnet VM alvo.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>Aplicar NSGs à AzureBastionSubnet

Se criar e aplicar um NSG à ***AzureBastionSubnet,*** certifique-se de ter adicionado as seguintes regras no seu NSG. Se não adicionar estas regras, a criação/atualização do NSG falhará:

* Controlar a **conectividade do avião:** Entrada na 443 do GatewayManager
* **Registo de diagnósticos e outros:** Saída na 443 para AzureCloud. As etiquetas regionais dentro desta etiqueta de serviço ainda não são suportadas.
* **VM alvo:** Saída para 3389 e 22 para VirtualNetwork

Um exemplo de regra NSG está disponível para referência neste [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Bastião Azure, consulte as [FAQ.](bastion-faq.md)
