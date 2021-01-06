---
title: 'Quickstart: Criar um equilibrador de carga interno - Azure CLI'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga interno usando o Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: fd85021da36d0e5ef32ce3f42c72b83016d38749
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901356"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Quickstart: Criar um equilibrador de carga interno para carregar VMs de equilíbrio usando Azure CLI

Começa com o Azure Load Balancer utilizando o Azure CLI para criar um equilibrador de carga interno e três máquinas virtuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](/cli/azure/group#az_group_create)

* Denominado **CreateIntLBQS-rg**. 
* No **local leste.**

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[SKUs do Balancer de Carga Azure](skus.md)**.

## <a name="configure-virtual-network---standard"></a>Configure rede virtual - Standard

Antes de implementar VMs e implementar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando [a rede az vnet criar:](/cli/azure/network/vnet#az-network-vnet-create)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Localização do **eastus.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público para o anfitrião do bastião:

* Crie um endereço IP público redundante de zona padrão chamado **myBastionIP**.
* Em **CCreateIntLBQS-rg **.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Criar uma sub-rede de bastião

Utilize [a sub-rede vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) de rede az para criar uma sub-rede de bastião:

* Denominado **AzureBastionSubnet**.
* Prefixo de endereço de **10.1.1.0/24**.
* Na rede virtual **myVNet.**
* No grupo de recursos **CreateIntLBQS-rg **.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Criar hospedeiro de bastião

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az-network-bastion-create) para criar um hospedeiro de bastião:

* Chamado **myBastionHost.**
* Em **CreateIntLBQS-rg **.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet.**
* Na localização **leste.**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.


### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, os VMs no endereço backend para os fins de trás são obrigados a ter interfaces de rede que pertencem a um grupo de segurança de rede. 

Criar um grupo de segurança de rede utilizando [a rede az nsg criar:](/cli/azure/network/nsg#az-network-nsg-create)

* Chamado **myNSG.**
* No grupo de recursos **CreateIntLBQS-rg.**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Criar uma regra de grupo de segurança de rede utilizando [a regra az network nsg criar:](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)

* Denominado **myNSGRuleHTTP**.
* No grupo de segurança de rede que criou no passo anterior, o **myNSG.**
* No grupo de recursos **CreateIntLBQS-rg.**
* Protocolo **(*)**.
* Direção **Entrada**.
* Fonte **(*)**.
* Destino **(*)**.
* Porto de destino **Port 80**.
* **Acesso Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Criar servidores backend - Standard

Nesta secção, cria-se:

* Três interfaces de rede para as máquinas virtuais.
* Três máquinas virtuais a serem utilizadas como servidores de backend para o equilibrador de carga.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Criar três interfaces de rede com [a az network nic criar:](/cli/azure/network/nic#az-network-nic-create)

* Denominado **myNicVM1,** **myNicVM2,** e **myNicVM3**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm criar:](/cli/azure/vm#az-vm-create)

* Denominado **myVM1,** **myVM2,** e **myVM3**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Ligado à interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 1,** **Zona 2** e Zona **3.**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Pode levar alguns minutos para os VMs se implantarem.

## <a name="create-standard-load-balancer"></a>Criar balanceador de carga padrão

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
* Associado com a sub-rede de backend **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>As máquinas virtuais no pool de backend não terão conectividade de internet de saída com esta configuração. </br> Para obter mais informações sobre o fornecimento de conectividade de saída, consulte: </br> **[Ligações de saída no Azure](load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga só de saída](egress-only.md)** </br> **[O que é Virtual Network NAT?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicione máquinas virtuais para carregar piscina de backend balancer

Adicione as máquinas virtuais à piscina de backend com [a az network nic ip-config endereço-pool add:](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)

* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Associado à interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre o SKUS, consulte **[skus do balançador de carga Azure.](skus.md)**

## <a name="configure-virtual-network---basic"></a>Configure rede virtual - Básico

Antes de implementar VMs e implementar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando [a rede az vnet criar:](/cli/azure/network/vnet#az-network-vnet-createt)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Localização do **eastus.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público para o anfitrião do bastião:

* Crie um endereço IP público redundante de zona padrão chamado **myBastionIP**.
* Em **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Criar uma sub-rede de bastião

Utilize [a sub-rede vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) de rede az para criar uma sub-rede de bastião:

* Denominado **AzureBastionSubnet**.
* Prefixo de endereço de **10.1.1.0/24**.
* Na rede virtual **myVNet.**
* No grupo de recursos **CreateIntLBQS-rg.**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Criar hospedeiro de bastião

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az-network-bastion-create) para criar um hospedeiro de bastião:

* Chamado **myBastionHost.**
* Em **CreateIntLBQS-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet.**
* Na localização **leste.**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, os VMs no endereço backend para os fins de trás são obrigados a ter interfaces de rede que pertencem a um grupo de segurança de rede. 

Criar um grupo de segurança de rede utilizando [a rede az nsg criar:](/cli/azure/network/nsg#az-network-nsg-create)

* Chamado **myNSG.**
* No grupo de recursos **CreateIntLBQS-rg.**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Criar uma regra de grupo de segurança de rede utilizando [a regra az network nsg criar:](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)

* Denominado **myNSGRuleHTTP**.
* No grupo de segurança de rede que criou no passo anterior, o **myNSG.**
* No grupo de recursos **CreateIntLBQS-rg.**
* Protocolo **(*)**.
* Direção **Entrada**.
* Fonte **(*)**.
* Destino **(*)**.
* Porto de destino **Port 80**.
* **Acesso Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Criar servidores backend - Básico

Nesta secção, cria-se:

* Três interfaces de rede para as máquinas virtuais.
* Disponibilidade definida para as máquinas virtuais
* Três máquinas virtuais a serem utilizadas como servidores de backend para o equilibrador de carga.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Criar três interfaces de rede com [a az network nic criar:](/cli/azure/network/nic#az-network-nic-create)

* Denominado **myNicVM1,** **myNicVM2,** e **myNicVM3**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Crie o conjunto de disponibilidade com [az vm disponibilidade-set criar](/cli/azure/vm/availability-set#az-vm-availability-set-create):

* Denominado **mySilabilitySet**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Localização **leste.**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm criar:](/cli/azure/vm#az-vm-create)

* Denominado **myVM1,** **myVM2,** e **myVM3**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Ligado à interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* No **mySilabilitySet**.


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Pode levar alguns minutos para os VMs se implantarem.

## <a name="create-basic-load-balancer"></a>Criar balanceador de carga básico

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
* Associado com a sub-rede de backend **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicione máquinas virtuais para carregar piscina de backend balancer

Adicione as máquinas virtuais à piscina de backend com [a az network nic ip-config endereço-pool add:](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)

* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Associado à interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

### <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Crie a interface de rede com [a criação de um nic de rede az:](/cli/azure/network/nic#az-network-nic-create)

* Chamado **myNicTestVM**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Crie a máquina virtual com [a criação de az vm:](/cli/azure/vm#az-vm-create)

* Chamado **myTestVM**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Anexado à interface de rede **myNicTestVM**.
* Imagem de máquina virtual **Win2019Datacenter**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Pode levar alguns minutos para a máquina virtual ser implantada.

## <a name="install-iis"></a>Instalar o IIS

Utilize [o conjunto de extensão az vm](/cli/azure/vm/extension#az_vm_extension_set) para instalar o IIS nas máquinas virtuais e desajuste o website predefinido para o nome do computador.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Teste

1. [Inicie sessão](https://portal.azure.com) no portal do Azure.

2. Encontre o endereço IP privado para o balançador de carga no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer**.

3. Tome nota ou copie o endereço ao lado **do Endereço IP Privado** na **visão geral** do **myLoadBalancer**.

4. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, da lista de recursos, selecione **myTestVM** que está localizado no grupo de recursos **CreateIntLBQS-rg.**

5. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myTestVM**.

8. Introduza o endereço IP a partir do passo anterior na barra de endereços do navegador. A página predefinida do servidor Web do IIS é apresentada no browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Criar um equilibrador de carga interno padrão" border="true":::
   
Para ver o balanceador de carga distribuir tráfego através dos três VMs, pode personalizar a página padrão de cada servidor Web IIS de cada VM e, em seguida, refrescar o seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize o comando de eliminação do [grupo AZ](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o equilibrador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido:

* Criou um balanceador de carga padrão ou público
* Máquinas virtuais anexadas. 
* Configurado a regra de tráfego do balanceador de carga e a sonda de saúde.
* Testei o equilibrador de carga.

Para saber mais sobre o Azure Load Balancer, continue a:
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)