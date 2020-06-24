---
title: Configure a preferência de encaminhamento para um endereço IP público - Portal Azure
description: Saiba como criar um IP público com uma preferência de encaminhamento de tráfego de Internet
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 7db8dcc87606e5c58ddac8f609d1538989af5ed7
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84689304"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configure a preferência de encaminhamento para um endereço IP público utilizando o portal Azure

Este artigo mostra-lhe como configurar a preferência de encaminhamento através da rede ISP (opção**Internet)** para um endereço IP público. Depois de criar o endereço IP público, pode associá-lo aos seguintes recursos Azure para o tráfego de entrada e saída para a internet:

* Máquina virtual
* Conjuntos de dimensionamento de máquinas virtuais
* Serviço de Kubernetes do Azure (AKS)
* Equilibrador de carga virado para a Internet
* Gateway de Aplicação
* Azure Firewall

Por padrão, a preferência de encaminhamento para endereço IP público está definida para a rede global da Microsoft para todos os serviços Azure e pode ser associada a qualquer serviço Azure.

> [!IMPORTANT]
> A preferência de encaminhamento está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="register-the-feature-for-your-subscription"></a>Registe a funcionalidade da sua subscrição
A função 'Preferência de encaminhamento' está atualmente em pré-visualização. Registe a funcionalidade da sua subscrição utilizando o Azure PowerShell da seguinte forma:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Criar um endereço IP público com uma preferência de encaminhamento
1. Inicie sessão no [portal do Azure](https://preview.portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, *digite o endereço IP público.*
3. Nos resultados da pesquisa, selecione **o endereço IP público.** Em seguida, na página **de endereços IP público,** selecione **Criar**.
3. Nas opções **de preferência de encaminhamento,** selecione **Internet**.

      ![Criar um endereço IP público](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. No entanto, a preferência de encaminhamento apenas suporta o IPV4 atualmente.

Pode associar o endereço IP público acima criado a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Utilize a secção CLI na página tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP público ao seu VM. Também pode associar o endereço IP público acima criado com um [Balançador de Carga Azure,](../load-balancer/load-balancer-overview.md)atribuindo-o à configuração frontal do balançador **de** carga. O endereço IP público serve como endereço IP virtual (VIP) com balanceamento de carga.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [IP público com preferência por encaminhamento](routing-preference-overview.md).
- [Configure a preferência de encaminhamento para um VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configure a preferência de encaminhamento para um endereço IP público utilizando o PowerShell](routing-preference-powershell.md).
- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
