---
title: Configure a preferência de encaminhamento para um endereço IP público utilizando o Azure CLI
titlesuffix: Azure Virtual Network
description: Saiba como criar um IP público com uma preferência de encaminhamento de tráfego na Internet
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 30e66df4f1a1b7e22d05bef70296e198963e3da2
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597780"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configure a preferência de encaminhamento para um endereço IP público utilizando o Azure CLI

Este artigo mostra-lhe como configurar a preferência de encaminhamento através da rede ISP (opção**Internet)** para um endereço IP público utilizando o Azure CLI. Depois de criar o endereço IP público, pode associá-lo aos seguintes recursos Azure para tráfego de entrada e saída para a internet:

* Máquina virtual
* Conjuntos de dimensionamento de máquinas virtuais
* Azure Kubernetes Service (AKS)
* Equilibrador de carga virado para a Internet
* Gateway de Aplicação
* Azure Firewall

Por padrão, a preferência de encaminhamento para o endereço IP público está definida para a rede global da Microsoft para todos os serviços Azure e pode ser associada a qualquer serviço Azure.

> [!IMPORTANT]
> A preferência por encaminhamento está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão Azure CLI 2.0.49 ou posterior. Para encontrar a sua versão instalada, `az --version` corra. Consulte [o Azure CLI](/cli/azure/install-azure-cli) para instalar ou atualizar informações.

## <a name="register-the-feature-for-your-subscription"></a>Registe a funcionalidade para a sua subscrição
A função De Preferência de Encaminhamento está atualmente em pré-visualização. Registe a funcionalidade para a sua subscrição da seguinte forma:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos na região **de East US** Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Crie um Endereço IP Público com preferência de encaminhamento do tipo "Internet" utilizando a rede de comando [az public-ip criar,](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)com o formato como mostrado abaixo.

O seguinte comando cria um novo IP público com preferência de encaminhamento de **Internet** na região **de East US** Azure.

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
>  Atualmente, a preferência de encaminhamento apenas suporta endereços IP públicos IPV4.

Pode associar o endereço IP público acima criado a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Utilize a secção CLI na página tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP público ao seu VM. Também pode associar o endereço IP público acima com um Balancer de [Carga Azure,](../load-balancer/load-balancer-overview.md)atribuindo-o à configuração **frontal** do equilíbrio de carga. O endereço IP público serve como endereço IP virtual (VIP) com balanceamento de carga.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a preferência de encaminhamento em endereços IP públicos](routing-preference-overview.md). 
- [Configure a preferência de encaminhamento para um VM utilizando o Azure CLI](configure-routing-preference-virtual-machine-cli.md).

