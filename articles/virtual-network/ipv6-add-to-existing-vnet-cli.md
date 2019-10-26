---
title: Adicionar IPv6 a um aplicativo IPv4 na rede virtual do Azure-CLI do Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 em um aplicativo existente na rede virtual do Azure usando CLI do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 0631ea51894e7e0642a55cedee54422fddab623b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952111"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>Adicionar IPv6 a um aplicativo IPv4 na rede virtual do Azure-CLI do Azure (versão prévia)

Este artigo mostra como adicionar endereços IPv6 a um aplicativo que está usando o endereço IP público IPv4 em uma rede virtual do Azure para um Standard Load Balancer usando CLI do Azure. A atualização in-loco inclui uma rede virtual e uma sub-rede, uma Standard Load Balancer com configurações de front-end IPv4 + IPV6, VMs com NICs que têm configurações IPv4 + IPv6, grupo de segurança de rede e IPs públicos.

> [!Important]
> O suporte a IPv6 para a rede virtual do Azure está atualmente em visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar CLI do Azure localmente, este guia de início rápido exigirá que você use CLI do Azure versão 2.0.28 ou posterior. Para localizar sua versão instalada, execute `az --version`. Consulte [instalar CLI do Azure](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="register-the-service"></a>Registrar o serviço

Antes de implantar um aplicativo de pilha dupla no Azure, você deve configurar sua assinatura para esse recurso de visualização usando as seguintes CLI do Azure:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Demora até 30 minutos para que o registro do recurso seja concluído. Você pode verificar o status do Registro executando o seguinte comando de CLI do Azure:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Após a conclusão do registro, execute o seguinte comando:

```azurelci
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard
Este artigo pressupõe que você implantou um Standard Load Balancer conforme descrito em [início rápido: criar um Standard Load Balancer-CLI do Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Criar endereços IPv6

Crie um endereço IPv6 público com [AZ Network Public-IP Create](/cli/azure/network/public-ip) para seu Standard Load Balancer. O exemplo a seguir cria um endereço IP público IPv6 chamado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB* :

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configurar front-end do balanceador de carga IPv6

COnfigure o balanceador de carga com o novo endereço IP IPv6 usando [AZ Network lb frontend-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) da seguinte maneira:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configurar pool de back-end do balanceador de carga IPv6

Crie o pool de back-end para NICs com endereços IPv6 usando [AZ Network lb address-pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) da seguinte maneira:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configurar regras do balanceador de carga IPv6

Crie regras de balanceador de carga IPv6 com [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Adicionar intervalos de endereços IPv6

Adicione intervalos de endereços IPv6 à rede virtual e à sub-rede que hospeda o balanceador de carga da seguinte maneira:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Adicionar configuração IPv6 a NICs

Configure as NICs da VM com um endereço IPv6 usando [AZ Network NIC IP-config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) da seguinte maneira:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal do Azure
Você pode exibir a rede virtual de pilha dupla IPv6 em portal do Azure da seguinte maneira:
1. Na barra de pesquisa do portal, insira *myVnet*.
2. Quando **myVnet** aparecer nos resultados da pesquisa, selecione-o. Isso inicia a página **visão geral** da rede virtual de pilha dupla chamada *myVNet*. A rede virtual de pilha dupla mostra as três NICs com as configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mysubnet*.

  ![Rede virtual de pilha dupla IPv6 no Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> O IPv6 para rede virtual do Azure está disponível na portal do Azure em somente leitura para esta versão de visualização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você atualizou um Standard Load Balancer existente com uma configuração de IP de front-end IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Você também adicionou configurações de IPv6 às NICs das VMs no pool de back-end. Para saber mais sobre o suporte a IPv6 em redes virtuais do Azure, consulte [o que é IPv6 para a rede virtual do Azure?](ipv6-overview.md)
