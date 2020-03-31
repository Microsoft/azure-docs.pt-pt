---
title: Criar um serviço de Link Privado Azure utilizando o Azure CLI
description: Saiba como criar um serviço Azure Private Link utilizando o Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350246"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Criar um serviço de Link Privado utilizando o Azure CLI
Este artigo mostra-lhe como criar um serviço de Link Privado em Azure utilizando o Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão mais recente do Azure CLI. Para encontrar a sua `az --version`versão instalada, corra. Consulte [o Azure CLI](/cli/azure/install-azure-cli) para instalar ou atualizar informações.
## <a name="create-a-private-link-service"></a>Criar um serviço de Ligação Privada
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar uma rede virtual, tem de criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *central-oeste:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma subnet chamada *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Criar uma sub-rede
Crie uma subrede para a rede virtual com a rede [az rede vnet criar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Este exemplo cria uma subnet chamada *mySubnet* na rede virtual *myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Criar um Balancer de Carga Interna 
Crie um equilibrador de carga interno com a criação de lb de [rede az](/cli/azure/network/lb#az-network-lb-create). Este exemplo cria um equilibrador de carga interno chamado *myILB* no grupo de recursos chamado *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Criar uma sonda de estado de funcionamento do balanceador de carga

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem receber o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma sonda de estado de funcionamento com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorizar o estado de funcionamento das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myHTTPRule* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no conjunto de front-end *myFrontEnd* e enviar o tráfego de rede com balanceamento de carga para o conjunto de endereços back-end *myBackEndPool* também através da porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, não cobrimos a criação de máquinas virtuais. Pode seguir os passos em [Criar um equilibror de carga interno para carregar VMs de equilíbrio utilizando o Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) para criar duas máquinas virtuais para serem usadas como servidores de backend para o equilibrador de carga. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Desativar as políticas de rede de serviços private link na subnet 
O serviço Private Link requer um IP de qualquer subnet da sua escolha dentro de uma rede virtual. Atualmente, não apoiamos políticas de rede nestes IPs.  Por conseguinte, temos de desativar as políticas de rede na subnet. Atualize a subnet para desativar as políticas de rede de serviços Private Link com a atualização de subnet da [rede Az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Criar um serviço de Ligação Privada  
 
Crie um serviço de Link Privado utilizando a configuração IP frontal do Balancer Standard Load Balancer com a [criação de serviço de ligação privada](/cli/azure/network/private-link-service#az-network-private-link-service-create)da rede Az . Este exemplo cria um serviço private link chamado *myPLS* usando O Equilíbrio de Carga Padrão chamado *myLoadBalancer* no grupo de recursos chamado *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Uma vez criado, tome nota do ID do Serviço de Link Privado. Precisará disso mais tarde para solicitar a ligação a este serviço.  
 
Nesta fase, o seu serviço Private Link é criado com sucesso e está pronto para receber o tráfego. Note que acima do exemplo é apenas para demonstrar a criação do serviço Private Link usando o Azure CLI.  Não configuramos as piscinas de backend do equilibrador de carga ou qualquer aplicação nas piscinas de backend para ouvir o tráfego. Se quiser ver fluxos de tráfego de ponta a ponta, é fortemente aconselhável configurar a sua aplicação por trás do seu Balancer De carga Standard.  
 
Em seguida, vamos demonstrar como mapear este serviço para um ponto final privado em diferentes redes virtuais usando o Azure CLI. Mais uma vez, o exemplo limita-se a criar o ponto final privado e a ligar-se ao serviço Private Link criado acima usando o Azure CLI. Além disso, pode criar máquinas virtuais na rede virtual para enviar/receber tráfego para o ponto final privado.        
 
## <a name="private-endpoints"></a>Pontos finais privados

### <a name="create-the-virtual-network"></a>Criar a rede virtual 
Criar uma rede virtual com a [z network vnet criar](/cli/azure/network/vnet#az-network-vnet-create). Este exemplo cria uma rede virtual chamada *myPEVNet* no grupo de recursos chamado *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Criar a sub-rede 
Criar uma subrede em rede virtual com a [rede az rede vnet criar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Este exemplo cria uma subnet chamada *mySubnet* em rede virtual chamada *myPEVnet* em grupo de recursos chamado *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Desativar as políticas de rede de pontos finais privados na subnet 
O ponto final privado pode ser criado em qualquer subnet da sua escolha dentro de uma rede virtual. Atualmente, não apoiamos políticas de rede em pontos finais privados.  Por conseguinte, temos de desativar as políticas de rede na subnet. Atualize a subnet para desativar as políticas de rede de pontos finais privados com a atualização de subnet da [rede Az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Criar ponto final privado e ligar-se ao serviço de ligação privada 
Crie um ponto final privado para consumir o serviço Private Link criado acima na sua rede virtual:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Você pode obter o *serviço private-connection-resource-i d* com `az network private-link-service show` o serviço Private Link. A identificação vai parecer:   
/subscrições/subID/recursosGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Mostrar ligações de serviço private link 
 
Consulte pedidos de ligação no seu serviço Private Link utilizando o [programa de serviços de ligação privada](/cli/azure/network/private-link-service#az-network-private-link-service-show)da rede Az .    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [serviço Azure Private Link](private-link-service-overview.md)
 
