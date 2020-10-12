---
title: Criar um serviço de Ligação Privada Azure utilizando o Azure CLI
description: Saiba como criar um serviço de Ligação Privada Azure utilizando o Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2cfc746d883b565fe7a082a316ce314f385225df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358180"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Criar um serviço de ligação privada utilizando o Azure CLI
Este artigo mostra-lhe como criar um serviço de Ligação Privada em Azure usando o Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão mais recente do Azure CLI. Para encontrar a sua versão instalada, corra `az --version` . Consulte [o Azure CLI](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.
## <a name="create-a-private-link-service"></a>Criar um serviço de Ligação Privada
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar uma rede virtual, tem de criar um grupo de recursos para acolher a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Criar uma sub-rede
Crie uma sub-rede para a rede virtual com [a criação da sub-rede de rede Az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) Este exemplo cria uma sub-rede chamada *mySubnet* na rede virtual *myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Criar um equilibrador de carga interna 
Crie um equilibrador de carga interno com [a az rede lb criar](/cli/azure/network/lb#az-network-lb-create). Este exemplo cria um balanceador de carga interno chamado *myILB* no grupo de recursos chamado *myResourceGroup*. 

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

Neste exemplo, não cobrimos a criação de máquinas virtuais. Pode seguir os passos na [Criar um equilibrador de carga interno para carregar vMs de equilíbrio utilizando O Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) para criar duas máquinas virtuais para serem usadas como servidores de backend para o balançador de carga. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Desativar as políticas de rede de serviços private link na sub-rede 
O serviço Private Link requer um IP a partir de qualquer sub-rede à sua escolha dentro de uma rede virtual. Atualmente, não apoiamos políticas de rede nestes IPs.  Por conseguinte, temos de desativar as políticas de rede na sub-rede. Atualize a sub-rede para desativar as políticas de rede de serviços private link com [a atualização da sub-rede de rede Az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>Criar um serviço de ligação privada utilizando o Balanceador de Carga Padrão 
 
Crie um serviço de ligação privada utilizando a configuração IP do frontend standard balancer com [a criação de serviço de ligação privada da rede Az](/cli/azure/network/private-link-service#az-network-private-link-service-create). Este exemplo cria um serviço de Ligação Privada chamado *myPLS* usando o Balancer de Carga Padrão chamado *myLoadBalancer* no grupo de recursos chamado *myResourceGroup*. 
 
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
Uma vez criado, tome nota do ID do Serviço de Ligação Privada. Mais tarde, necessitará disso para solicitar a ligação a este serviço.  
 
Nesta fase, o seu serviço Private Link é criado com sucesso e está pronto para receber o tráfego. Note que acima do exemplo é apenas para demonstrar a criação de um serviço de Ligação Privada utilizando o Azure CLI.  Não configuramos as piscinas de backend do balançador de carga ou qualquer aplicação nas piscinas de backend para ouvir o tráfego. Se quiser ver fluxos de tráfego de ponta a ponta, é fortemente aconselhável configurar a sua aplicação por trás do seu Balancer de Carga Padrão.  
 
Em seguida, vamos demonstrar como mapear este serviço para um ponto final privado em diferentes redes virtuais usando Azure CLI. Mais uma vez, o exemplo limita-se a criar o ponto final privado e a ligar-se ao serviço Private Link criado acima usando o Azure CLI. Além disso, pode criar máquinas virtuais na rede virtual para enviar/receber tráfego para o ponto final privado.        
 
## <a name="private-endpoints"></a>Pontos finais privados

### <a name="create-the-virtual-network"></a>Criar a rede virtual 
Criar uma rede virtual com [a criação de vnet de rede Az.](/cli/azure/network/vnet#az-network-vnet-create) Este exemplo cria uma rede virtual chamada *myPEVNet*   em grupo de recursos chamado *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Criar a sub-rede 
Criar uma sub-rede em rede virtual com [a sub-rede de rede Az criar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Este exemplo cria uma sub-rede chamada *mySubnet*   em rede virtual chamada *myPEVnet* em grupo de recursos chamado *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Desativar as políticas de rede de pontos finais privadas na sub-rede 
O ponto final privado pode ser criado em qualquer sub-rede da sua escolha dentro de uma rede virtual. Atualmente, não apoiamos políticas de rede em pontos finais privados.  Por conseguinte, temos de desativar as políticas de rede na sub-rede. Atualize a sub-rede para desativar as políticas privadas de rede de pontos finais com [a atualização da sub-rede de rede Az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Crie o ponto final privado e ligue-se ao serviço de ligações privadas 
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
Você pode obter o *id de recursos de ligação privada* `az network private-link-service show` no serviço Private Link. A ID vai parecer:   
/subscrições/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Mostrar ligações de serviço private link 
 
Consulte os pedidos de ligação no seu serviço Private Link utilizando [o programa de serviço de ligação privada da rede Az](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o serviço Azure Private Link](private-link-service-overview.md)
 
