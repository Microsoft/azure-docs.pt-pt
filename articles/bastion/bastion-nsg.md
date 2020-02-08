---
title: Trabalhar com VMs e NSGs em Azure Bastion
description: Este artigo descreve como incorporar o acesso NSG com a bastiões do Azure
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087264"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Trabalhando com acesso NSG e bastiões do Azure

Ao trabalhar com a bastiões do Azure, você pode usar NSGs (grupos de segurança de rede). Para mais informações, consulte [Grupos de Segurança](../virtual-network/security-overview.md). 

![Arquitetura](./media/bastion-nsg/nsg-architecture.png)

Neste diagrama:

* O host de bastiões é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador HTML5.
* O usuário navega para a máquina virtual do Azure para RDP/SSH.
* Conectar integração-sessão RDP/SSH de clique único dentro do navegador
* Nenhum IP público é necessário na VM do Azure.

## <a name="nsg"></a>Grupos de segurança da rede

Esta seção mostra o tráfego de rede entre o usuário e a bastiões do Azure e para as VMs de destino em sua rede virtual:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

A bastiões do Azure é implantada especificamente para o AzureBastionSubnet.

* **Tráfego ingress:**

   * **Tráfego ingresso da internet pública:** O Bastião Azure vai criar um IP público que precisa do porto 443 habilitado no IP público para o tráfego de ingressos. A porta 3389/22 não precisa ser aberta no AzureBastionSubnet.
   * **Tráfego ingresso do avião de controlo do Bastião Azure:** Para a conectividade do plano de controlo, ative a entrada da porta 443 a partir da etiqueta de serviço **GatewayManager.** Isso permite que o plano de controle, ou seja, o Gerenciador de gateway seja capaz de se comunicar com a bastiões do Azure.

* **Tráfego de Egress:**

   * **Tráfego de Egress para os VMs-alvo:** O Azure Bastion vai atingir os VMs-alvo em IP privado. O NSGs precisa permitir o tráfego de saída para outras sub-redes de VM de destino para a porta 3389 e 22.
   * **Tráfego de Egress para outros pontos finais públicos em Azure:** O Azure Bastion precisa de ser capaz de se ligar a vários pontos finais públicos dentro do Azure (por exemplo, para armazenar registos de diagnóstico e registos de medição). Por esta razão, o Azure Bastion precisa de saída para 443 para a etiqueta de serviço **AzureCloud.**

* **Subnet-alvo VM:** Esta é a sub-rede que contém a máquina virtual alvo a que pretende rdp/SSH.

   * **Tráfego ingresso do Bastião Azure:** O Azure Bastion chegará ao vm alvo em vez de IP privado. As portas RDP/SSH (portas 3389/22, respectivamente) precisam ser abertas no lado da VM de destino sobre o IP privado. Como prática recomendada, você pode adicionar o intervalo de endereços IP da sub-rede de bastiões do Azure nesta regra para permitir que somente a bastiões possa abrir essas portas nas VMs de destino em sua sub-rede de VM de destino.

## <a name="apply"></a>Aplicar NSGs à AzureBastionSubnet

Se criar e aplicar um NSG à ***AzureBastionSubnet,*** certifique-se de ter adicionado as seguintes regras no seu NSG. Se você não adicionar essas regras, a criação/atualização do NSG falhará:

* Controlar a **conectividade do avião:** Entrada na 443 do GatewayManager
* **Registo de diagnósticos e outros:** Saída na 443 para AzureCloud. Ainda não há suporte para marcas regionais nessa marcação de serviço.
* **VM alvo:** Saída para 3389 e 22 para VirtualNetwork

Um exemplo de regra NSG está disponível para referência neste [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Bastião Azure, consulte as [FAQ.](bastion-faq.md)
