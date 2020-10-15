---
title: Trabalhar com VMs e NSGs em Azure Bastion
description: Pode utilizar grupos de segurança de rede com Azure Bastion. Saiba mais sobre as sub-redes necessárias para esta configuração.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: 7853ac3ece01057282bc6cb421018020e15273b5
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079195"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Trabalhar com acesso NSG e Azure Bastion

Ao trabalhar com a Azure Bastion, pode utilizar grupos de segurança de rede (NSGs). Para mais informações, consulte [os Grupos de Segurança.](../virtual-network/network-security-groups-overview.md)

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

Neste diagrama:

* O hospedeiro Bastion está implantado na rede virtual.
* O utilizador liga-se ao portal Azure utilizando qualquer navegador HTML5.
* O utilizador navega para a máquina virtual Azure para RDP/SSH.
* Connect Integration - Sessão RDP/SSH de clique único dentro do navegador
* Não é necessário um IP público no Azure VM.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede

Esta secção mostra-lhe o tráfego de rede entre o utilizador e o Bastião Azure, e através de VMs de destino na sua rede virtual:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

O Azure Bastion é implantado especificamente para ***a AzureBastionSubnet***.

* **Tráfego ingresso:**

   * **Tráfego ingresso da internet pública:** O Bastião Azure criará um IP público que necessita do porto 443 habilitado no IP público para o tráfego de entrada. A porta 3389/22 NÃO é obrigada a ser aberta na AzureBastionSubnet.
   * **Tráfego ingresso do avião de controlo Azure Bastion:** Para a conectividade do plano de controlo, ative a porta 443 de entrada a partir da etiqueta de serviço **GatewayManager.** Isto permite que o avião de controlo, isto é, o Gateway Manager possa falar com Azure Bastion.


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="NSG":::

* **Tráfego de Egress:**

   * **Tráfego Egress para alvo VMs:** Azure Bastion atingirá os VMs alvo em IP privado. Os NSGs precisam de permitir o tráfego de saída para outras sub-redes VM alvo para as portas 3389 e 22.
   * **Tráfego Egress para outros pontos finais públicos em Azure:** O Azure Bastion precisa de ser capaz de se ligar a vários pontos finais públicos dentro de Azure (por exemplo, para armazenar registos de diagnósticos e registos de medição). Por esta razão, a Azure Bastion precisa de uma saída para a marca de serviço 443 para **a AzureCloud.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="NSG":::

### <a name="target-vm-subnet"></a>Sub-rede VM alvo
Esta é a sub-rede que contém a máquina virtual alvo a que pretende fazer RDP/SSH.

   * **Tráfego ingresso de Azure Bastion:** Azure Bastion chegará ao VM alvo em vez de IP privado. As portas RDP/SSH (portas 3389/22, respectivamente) precisam de ser abertas no lado alvo do VM em vez de IP privado. Como uma boa prática, pode adicionar o intervalo de endereço IP da Subnet Azure Bastion nesta regra para permitir que apenas Bastion possa abrir estas portas nos VMs-alvo na sub-rede VM alvo.


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre O Azure Bastion, consulte as [FAQ](bastion-faq.md).
