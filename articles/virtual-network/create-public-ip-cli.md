---
title: Criar um IP público - Azure CLI
description: Saiba como criar um IP público usando O Azure CLI
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 76af225b6f93e0a47799234e3e2b430344f68787
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009998"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Quickstart: Criar um endereço IP público utilizando O Azure CLI

Este artigo mostra-lhe como criar um recurso de endereço IP público usando Azure CLI. Para obter mais informações sobre quais os recursos a que estes recursos podem estar associados, a diferença entre O SKU Básico e Padrão, e outras informações [relacionadas, consulte endereços IP públicos.](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)  Para este exemplo, centrar-nos-emos apenas nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com [o grupo AZ criar](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) o **myResourceGroup** na localização **eastus2.**

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Criar IP público

---
# <a name="standard-sku---using-zones"></a>[**SKU Padrão - Utilização de zonas**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>O seguinte comando funciona para a versão API 2020-08-01 ou posterior.  Para obter mais informações sobre a versão API atualmente utilizada, consulte os [Fornecedores e Tipos de Recursos.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público redundante de zona padrão chamado **myStandardZRPublicIP** no **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Para versões da API com mais de 2020-08-01, executar o comando acima sem especificar um parâmetro de zona para criar um endereço IP redundante de zona. 
>

Para criar um endereço IP público zonal padrão na Zona 2 denominado **myStandardZonalPublicIP** no **myResourceGroup,** utilize o seguinte comando:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Note que as opções acima para zonas são apenas seleções válidas em regiões com [Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)

# <a name="standard-sku---no-zones"></a>[**SKU Padrão - Sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O seguinte comando funciona para a versão API 2020-08-01 ou posterior.  Para obter mais informações sobre a versão API atualmente utilizada, consulte os [Fornecedores e Tipos de Recursos.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público padrão como um recurso não-zonal chamado **myStandardPublicIP** no **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Esta seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público estático básico chamado **myBasicPublicIP** no **myResourceGroup**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Se for aceitável que o endereço IP se altere ao longo do tempo, a atribuição de IP **dinâmico** pode ser selecionada alterando o método de atribuição para 'Dynamic'.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [Gerir os endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Passos seguintes
- Associar um [endereço IP público a uma Máquina Virtual.](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
