---
title: Trabalhar com VMs e NSGs no Bastion do Azure | Documentos da Microsoft
description: Este artigo descreve como incorporar o acesso NSG com Bastion do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5312ad2593e732f4c84eb67ed263bc9e4666a67a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594197"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Trabalhar com acesso NSG e Bastion do Azure (pré-visualização)

Ao trabalhar com Bastion do Azure, pode utilizar grupos de segurança de rede (NSGs). Para obter mais informações, consulte [grupos de segurança](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

![Arquitetura](./media/bastion-nsg/nsg_architecture.png)

Neste diagrama:

* O anfitrião de bastião é implementado na rede virtual.
* O utilizador liga-se ao portal do Azure com qualquer navegador do HTML5.
* O utilizador seleciona a máquina virtual para ligar a.
* Com um único clique, a sessão do RDP/SSH é aberto no browser.
* Não existe nenhum IP público é necessária na VM do Azure.

## <a name="nsg"></a>Grupos de segurança de rede

* **AzureBastionSubnet:** Bastion do Azure é implementada no AzureBastionSubnet específico.  
    * **Tráfego de entrada da internet pública:** Bastion o Azure irá criar um IP público que precisa da porta 443 ativada no IP público para o tráfego de entrada. Porta de 3389/22 não têm de estar abertas no AzureBastionSubnet.
    * **Tráfego de saída para VMs de destino:** Bastion do Azure irá entrar em VMs de destino através de IP privado. Os NSGs têm de permitir tráfego de saída para outras sub-redes VM de destino.
* **Sub-rede VM de destino:** Trata-se a sub-rede que contém a máquina virtual de destino que pretende fazer RDP/SSH.
    * **Tráfego de entrada de Bastion do Azure:** Bastion do Azure irá entrar para a VM de destino através de IP privado. Portas RDP/SSH (portas 3389 e 22, respectivamente) tem de ser aberto no lado VM de destino através de IP privado.

## <a name="apply"></a>Aplicar NSGs a AzureBastionSubnet

Se aplicar NSGs para o **AzureBastionSubnet**, permitir que as seguintes duas etiquetas de serviço para o plano de controlo do Azure e infraestrutura:

* **GatewayManager (apenas Resource Manager)** : Esta etiqueta denota os prefixos de endereço do serviço Gestor de Gateway do Azure. Se especificar GatewayManager para o valor, o tráfego é permitido ou negado para GatewayManager.  Se estiver a criar NSGs a AzureBastionSubnet, ative a marca de GatewayManager para tráfego de entrada.

* **AzureCloud (apenas Resource Manager)** : Esta etiqueta denota o espaço de endereços IP para o Azure, incluindo todos os endereços IP públicos datacenter. Se especificar AzureCloud para o valor, o tráfego é permitido ou negado para os endereços IP públicos do Azure. Se pretender permitir o acesso apenas a AzureCloud numa região específica, pode especificar a região. Por exemplo, se pretender permitir o acesso apenas a AzureCloud do Azure na região E.U.A. leste, poderia especificar AzureCloud.EastUS como a etiqueta de serviço. Se estiver a criar NSGs a AzureBastionSubnet, ative a marca de AzureCloud para tráfego de saída.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre Bastion do Azure, consulte o [FAQ](bastion-faq.md)
