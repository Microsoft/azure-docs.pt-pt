---
title: Trabalhando com VMs e NSGs na bastiões do Azure | Microsoft Docs
description: Este artigo descreve como incorporar o acesso NSG com a bastiões do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694933"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Trabalhando com o acesso NSG e a bastiões do Azure (visualização)

Ao trabalhar com a bastiões do Azure, você pode usar NSGs (grupos de segurança de rede). Para obter mais informações, consulte [grupos de segurança](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

![Arquitetura](./media/bastion-nsg/nsg_architecture.png)

Neste diagrama:

* O host de bastiões é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador HTML5.
* O usuário seleciona a máquina virtual à qual se conectar.
* Com um único clique, a sessão RDP/SSH é aberta no navegador.
* Nenhum IP público é necessário na VM do Azure.

## <a name="nsg"></a>Grupos de segurança de rede

* **AzureBastionSubnet:** A bastiões do Azure é implantada no AzureBastionSubnet específico.  
    * **Tráfego de entrada da Internet pública:** A bastiões do Azure criará um IP público que precisa da porta 443 habilitada no IP público para o tráfego de entrada. A porta 3389/22 não precisa ser aberta no AzureBastionSubnet.
    * **Tráfego de saída para VMs de destino:** A bastiões do Azure alcançará as VMs de destino sobre o IP privado. O NSGs precisa permitir o tráfego de saída para outras sub-redes de VM de destino.
* **Sub-rede VM de destino:** Essa é a sub-rede que contém a máquina virtual de destino para a qual você deseja RDP/SSH.
    * **Tráfego de entrada da bastiões do Azure:** A bastiões do Azure alcançará a VM de destino sobre o IP privado. As portas RDP/SSH (portas 3389 e 22, respectivamente) precisam ser abertas no lado da VM de destino sobre o IP privado.

## <a name="apply"></a>Aplicar NSGs a AzureBastionSubnet

Se você aplicar NSGs ao **AzureBastionSubnet**, permita as duas marcas de serviço a seguir para a infraestrutura e o plano de controle do Azure:

* **Gatewaymanager (somente no Gerenciador de recursos)** : Essa marca denota os prefixos de endereço do serviço do Gerenciador de gateway do Azure. Se você especificar Gatewaymanager para o valor, o tráfego será permitido ou negado ao Gatewaymanager.  Se você estiver criando NSGs no AzureBastionSubnet, habilite a marca Gatewaymanager para o tráfego de entrada.

* **AzureCloud (somente no Gerenciador de recursos)** : Essa marca denota o espaço de endereço IP do Azure, incluindo todos os endereços IP públicos do datacenter. Se você especificar AzureCloud para o valor, o tráfego será permitido ou negado aos endereços IP públicos do Azure. Se você quiser permitir o acesso somente a AzureCloud em uma região específica, poderá especificar a região. Por exemplo, se você quiser permitir o acesso somente ao Azure AzureCloud na região leste dos EUA, poderá especificar AzureCloud. Eastus como uma marca de serviço. Se você estiver criando NSGs no AzureBastionSubnet, habilite a marca AzureCloud para o tráfego de saída. Se você abrir a porta 443 para entrada na Internet, não precisará habilitar a marca AzureCloud para o tráfego de entrada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a bastiões do Azure, consulte as [perguntas frequentes](bastion-faq.md)
