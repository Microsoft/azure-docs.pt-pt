---
title: Configure a preferência de encaminhamento para um endereço IP público usando O Azure CLI
titlesuffix: Azure Virtual Network
description: Saiba como criar um IP público com uma preferência de encaminhamento de tráfego de Internet utilizando o Azure CLI.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 39514f8a34e85525e2a8900a8e6e3cc8656fb84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329300"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configure a preferência de encaminhamento para um endereço IP público usando O Azure CLI

Este artigo mostra-lhe como configurar a preferência de encaminhamento através da rede ISP (opção**Internet)** para um endereço IP público utilizando O Azure CLI. Depois de criar o endereço IP público, pode associá-lo aos seguintes recursos Azure para o tráfego de entrada e saída para a internet:

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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.49 do Azure CLI ou mais tarde. Para encontrar a sua versão instalada, corra `az --version` . Consulte [o Azure CLI](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="register-the-feature-for-your-subscription"></a>Registe a funcionalidade da sua subscrição
A função 'Preferência de encaminhamento' está atualmente em pré-visualização. Registe a funcionalidade da sua subscrição da seguinte forma:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região **de Azure oriental dos EUA:**

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Crie um Endereço IP Público com preferência de encaminhamento do tipo "Internet" utilizando a rede de comando [az public-ip create,](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)com o formato como mostrado abaixo.

O seguinte comando cria um novo IP público com preferência por encaminhamento de **Internet** na região **de Azure leste dos EUA.**

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Atualmente, a preferência de encaminhamento suporta apenas endereços IPV4 públicos IPV4.

Pode associar o endereço IP público acima criado a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Utilize a secção CLI na página tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP público ao seu VM. Também pode associar o endereço IP público acima criado com um [Balançador de Carga Azure,](../load-balancer/load-balancer-overview.md)atribuindo-o à configuração frontal do balançador **de** carga. O endereço IP público serve como endereço IP virtual (VIP) com balanceamento de carga.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a preferência de encaminhamento em endereços IP públicos.](routing-preference-overview.md) 
- [Configure a preferência de encaminhamento para um VM utilizando o Azure CLI](configure-routing-preference-virtual-machine-cli.md).

