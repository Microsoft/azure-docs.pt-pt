---
title: Trabalhar com VMs e NSGs em Azure Bastion
description: Pode utilizar grupos de segurança de rede com Azure Bastion. Saiba mais sobre as sub-redes necessárias para esta configuração.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700223"
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

> [!IMPORTANT]
> Se optar por utilizar um NSG com o seu recurso Azure Bastion, **deve** criar todas as seguintes regras de trânsito de entrada e saída. Omitir qualquer uma das seguintes regras no seu NSG irá bloquear o seu recurso Azure Bastion de receber as atualizações necessárias no futuro e, portanto, abrir o seu recurso para futuras vulnerabilidades de segurança.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

O Azure Bastion é implantado especificamente para ***a AzureBastionSubnet***.

* **Tráfego ingresso:**

   * **Tráfego ingresso da internet pública:** O Bastião Azure criará um IP público que necessita do porto 443 habilitado no IP público para o tráfego de entrada. A porta 3389/22 NÃO é obrigada a ser aberta na AzureBastionSubnet.
   * **Tráfego ingresso do avião de controlo Azure Bastion:** Para a conectividade do plano de controlo, ative a porta 443 de entrada a partir da etiqueta de serviço **GatewayManager.** Isto permite que o avião de controlo, isto é, o Gateway Manager possa falar com Azure Bastion.
   * **Tráfego ingresso do avião de dados de Azure Bastion:** Para a comunicação de planos de dados entre os componentes subjacentes do Azure Bastion, ative as portas 8080, 5701 a entrar da etiqueta de serviço **VirtualNetwork** para a etiqueta de serviço **VirtualNetwork.** Isto permite que os componentes de Azure Bastion falem uns com os outros.
   * **Tráfego ingresso do Equilibrador de Carga Azure:** Para as sondas de saúde, ative a porta 443 a partir da etiqueta de serviço **AzureLoadBalancer.** Isto permite ao Azure Load Balancer detetar conectividade


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="A screenshot mostra regras de segurança de entrada para a conectividade Azure Bastion.":::

* **Tráfego de Egress:**

   * **Tráfego Egress para alvo VMs:** Azure Bastion atingirá os VMs alvo em IP privado. Os NSGs precisam de permitir o tráfego de saída para outras sub-redes VM alvo para as portas 3389 e 22.
   * **Avião de dados do Tráfego Egress para Azure Bastion:** Para a comunicação de planos de dados entre os componentes subjacentes do Azure Bastion, ative as portas 8080, 5701 saídas da etiqueta de serviço **VirtualNetwork** para a etiqueta de serviço **VirtualNetwork.** Isto permite que os componentes de Azure Bastion falem uns com os outros.
   * **Tráfego Egress para outros pontos finais públicos em Azure:** O Azure Bastion precisa de ser capaz de se ligar a vários pontos finais públicos dentro de Azure (por exemplo, para armazenar registos de diagnósticos e registos de medição). Por esta razão, a Azure Bastion precisa de uma saída para a marca de serviço 443 para **a AzureCloud.**
   * **Tráfego de Egress para a Internet:** O Azure Bastion precisa de ser capaz de comunicar com a Internet para validação de sessão e certificado. Por esta razão, recomendamos permitir a entrada do porto 80 para a **Internet.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="A screenshot mostra regras de segurança de saída para a conectividade Azure Bastion.":::

### <a name="target-vm-subnet"></a>Sub-rede VM alvo
Esta é a sub-rede que contém a máquina virtual alvo a que pretende fazer RDP/SSH.

   * **Tráfego ingresso de Azure Bastion:** Azure Bastion chegará ao VM alvo em vez de IP privado. As portas RDP/SSH (portas 3389/22, respectivamente) precisam de ser abertas no lado alvo do VM em vez de IP privado. Como uma boa prática, pode adicionar o intervalo de endereço IP da Subnet Azure Bastion nesta regra para permitir que apenas Bastion possa abrir estas portas nos VMs-alvo na sub-rede VM alvo.


## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre O Azure Bastion, consulte as [FAQ](bastion-faq.md).
