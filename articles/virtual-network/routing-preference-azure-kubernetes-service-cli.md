---
title: Configure preferência de encaminhamento para um serviço Azure Kubernetes usando Azure CLI
titlesuffix: Azure Virtual Network
description: Aprenda a configurar um cluster AKS com preferência de encaminhamento utilizando o Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680344"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Configure a preferência de encaminhamento para um cluster Kubernetes usando O Azure CLI

Este artigo mostra-lhe como configurar a preferência de encaminhamento através da rede ISP (opção **Internet)** para um cluster Kubernetes usando O Azure CLI. A preferência de encaminhamento é definida através da criação de um endereço IP público do tipo de preferência de encaminhamento **Internet**** e, em seguida, usá-lo enquanto cria o cluster AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região **de Azure oriental dos EUA:**

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Crie um endereço IP público com preferência de encaminhamento do tipo de **Internet** utilizando a rede de comando [az public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

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

## <a name="get-the-id-of-public-ip-address"></a>Obtenha o ID do endereço IP público

O seguinte comando devolve o ID do endereço IP público criado na secção anterior:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Criar cluster Kubernetes com o ip público

O seguinte comando cria o cluster AKS com o IP público criado na secção anterior:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Leva alguns minutos para implantar o cluster AKS.

Para validar, procure o IP público criado no passo anterior no portal Azure, verá que o IP está associado ao equilibrador de carga que está associado ao cluster Kubernetes, conforme mostrado abaixo:

 ![Encaminhamento de prioridade pública IP para Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a preferência de encaminhamento em endereços IP públicos.](routing-preference-overview.md) 
- [Configure a preferência de encaminhamento para um VM utilizando o Azure CLI](configure-routing-preference-virtual-machine-cli.md).

