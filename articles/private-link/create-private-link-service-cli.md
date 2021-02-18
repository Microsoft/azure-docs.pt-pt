---
title: Criar um serviço de Ligação Privada Azure utilizando o Azure CLI
description: Saiba como criar um serviço de Ligação Privada Azure utilizando o Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 27ce0b2646b6c380e86b377d3dba287f7791794e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653735"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Criar um serviço de ligação privada utilizando o Azure CLI

Começa a criar um serviço de Ligação Privada que se refere ao teu serviço.  Dê acesso ao Link Privado ao seu serviço ou recurso implantado atrás de um Equilibr de Carga Padrão Azure.  Os utilizadores do seu serviço têm acesso privado a partir da sua rede virtual.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](/cli/azure/group#az_group_create)

* Nomeado **CreatePrivLinkService-rg**. 
* No **local leste.**

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Nesta secção, irá criar uma rede virtual e um Equilibrador de Carga Azure interno.

### <a name="virtual-network"></a>Rede virtual

Nesta secção, cria uma rede virtual e uma sub-rede para hospedar o equilibrador de carga que acede ao seu serviço Private Link.

Criar uma rede virtual utilizando [a rede az vnet criar:](/cli/azure/network/vnet#az-network-vnet-create)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Sub-rede chamada **mySubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **CreatePrivLinkService-rg.**
* Localização de **Eastus2**.
* Desative a política de rede para o serviço de ligações privadas na sub-rede.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Para atualizar a sub-rede para desativar as políticas de rede de serviços de ligação privada, utilize [a atualização da sub-rede de rede az:](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Criar balanceador de carga padrão

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

  * Um pool IP frontend que recebe o tráfego de rede de entrada no equilibrador de carga.
  * Uma piscina IP de backend onde a piscina frontal envia o tráfego de rede equilibrado de carga.
  * Uma sonda de saúde que determina a saúde dos casos de VM backend.
  * Uma regra do balançador de carga que define como o tráfego é distribuído para os VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um equilibrador de carga pública com [a az network lb create:](/cli/azure/network/lb#az-network-lb-create)

* Chamado **myLoadBalancer.**
* Uma piscina frontal chamada **myFrontEnd.**
* Uma piscina de backend chamada **myBackEndPool**.
* Associado à rede virtual **myVNet.**
* Associado com a sub-rede de backend **mySubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. 

Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [sonda LB de rede az criar:](/cli/azure/network/lb/probe#az-network-lb-probe-create)

* Monitoriza a saúde das máquinas virtuais.
* Chama-se **MyHealthProbe.**
* Protocolo **TCP**.
* Porta de monitorização **80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra do balançador de carga define:

* Configuração IP frontend para o tráfego de entrada.
* O backend IP pool para receber o tráfego.
* A fonte necessária e o porto de destino. 

Crie uma regra de balançador de carga com [regra az rede lb criar:](/cli/azure/network/lb/rule#az-network-lb-rule-create)

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina frontal **myFrontEnd**.
* Envio de tráfego de rede equilibrado de carga para a piscina de endereço de backend **myBackEndPool** usando **a porta 80**. 
* Utilizando a sonda de saúde **myHealthProbe**.
* Protocolo **TCP**.
* Tempo de **15 minutos** inativo.
* Ativar o reset do TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Criar um serviço de ligação privada

Nesta secção, crie um serviço de ligação privada que utilize o Balançador de Carga Azure criado no passo anterior.

Crie um serviço de ligação privada utilizando uma configuração IP de frontend de suporte de carga padrão com [a criação de serviço de ligação privada da rede Az:](/cli/azure/network/private-link-service#az-network-private-link-service-create)

* Nomeado **myPrivateLinkService**.
* Na rede virtual **myVNet.**
* Associado com o balanceador de carga padrão **myLoadBalancer** e configuração frontend **myFrontEnd**.
* Na localização **leste2.**
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

O seu serviço de ligação privada foi criado e pode receber tráfego. Se quiser ver fluxos de tráfego, configuure a sua aplicação por trás do seu balanceador de carga padrão.


## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, você irá mapear o serviço de ligação privada para um ponto final privado. Uma rede virtual contém o ponto final privado para o serviço de ligação privada. Esta rede virtual contém os recursos que irão aceder ao seu serviço de ligação privada.

### <a name="create-private-endpoint-virtual-network"></a>Criar rede virtual de ponto final privado

Criar uma rede virtual utilizando [a rede az vnet criar:](/cli/azure/network/vnet#az-network-vnet-create)

* Chamado **myVNetPE.**
* Prefixo do endereço de **11.1.0.0/16**.
* Sub-rede chamada **mySubnetPE**.
* Prefixo de sub-rede de **11.1.0.0/24**.
* No grupo de recursos **CreatePrivLinkService-rg.**
* Localização de **Eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Para atualizar a sub-rede para desativar as políticas privadas de rede de pontos [finais, utilize a atualização da sub-rede de rede Az:](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Criar ponto final e conexão

* Utilize [o programa de serviço de ligação privada da rede Az](/cli/azure/network/private-link-service#az_network_private_link_service_show) para obter o ID de recursos do serviço de ligação privada. O comando coloca o ID do recurso numa variável para posterior utilização.

* Utilize [o ponto final privado da rede Az](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para criar o ponto final privado na rede virtual que criou anteriormente.

* Denominado **MyPrivateEndpoint**.
* No grupo de recursos **CreatePrivLinkService-rg.**
* Nome de conexão **myPEconnectiontoPLS**.
* Localização de **Eastus2**.
* Na rede virtual **myVNetPE** e sub-rede **mySubnetPE**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize o comando de eliminação do [grupo AZ](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de ligação privada, o equilibrador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, irá:

* Criei uma rede virtual e um Equilibrador de Carga Azure interno.
* Criei um serviço de ligação privada

Para saber mais sobre o ponto final da Azure Private, continue a:
> [!div class="nextstepaction"]
> [Quickstart: Criar um ponto final privado utilizando O Azure CLI](create-private-endpoint-cli.md)
