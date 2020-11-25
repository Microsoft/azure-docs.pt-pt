---
title: 'Quickstart: Criar um equilibrador de carga pública - Azure CLI'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga público com a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: b00d0c83758d0349fd3926e0c263b65af2e4dc92
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96021185"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um balanceador de carga público para fazer o balanceamento de carga das VMs com a CLI do Azure

Começa com o Azure Load Balancer utilizando o Azure CLI para criar um equilibrador de carga pública e três máquinas virtuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](/cli/azure/group#az-group-create)

* Denominado **CreatePubLBQS-rg**. 
* No **local leste.**

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

## <a name="configure-virtual-network---standard"></a>Configure rede virtual - Standard

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando [a rede az vnet criar:](/cli/azure/network/vnet#az-network-vnet-createt)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **CreatePubLBQS-rg.**
* Localização do **eastus.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público para o anfitrião do bastião:

* Crie um endereço IP público redundante de zona padrão chamado **myBastionIP**.
* Em **CCreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Criar uma sub-rede de bastião

Utilize [a sub-rede vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) de rede az para criar uma sub-rede de bastião:

* Denominado **AzureBastionSubnet**.
* Prefixo de endereço de **10.1.1.0/24**.
* Na rede virtual **myVNet.**
* No grupo de recursos **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Criar hospedeiro de bastião

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az-network-bastion-create) para criar um hospedeiro de bastião:

* Chamado **myBastionHost.**
* In **CreatePubLBQs-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet.**
* Na localização **leste.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
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
* No grupo de recursos **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Criar uma regra de grupo de segurança de rede utilizando [a regra az network nsg criar:](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)

* Denominado **myNSGRuleHTTP**.
* No grupo de segurança de rede que criou no passo anterior, o **myNSG.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Protocolo **(*)**.
* Direção **Entrada**.
* Fonte **(*)**.
* Destino **(*)**.
* Porto de destino **Port 80**.
* **Acesso Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
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
* No grupo de recursos **CreatePubLBQS-rg**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm criar:](/cli/azure/vm#az-vm-create)

### <a name="vm1"></a>VM1
* Chamado **myVM1**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Anexado à interface de rede **myNicVM1**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 1.**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Chamado **myVM2**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Anexado à interface de rede **myNicVM2**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 2.**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamado **myVM3.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Anexado à interface de rede **myNicVM3**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 3.**

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
Pode levar alguns minutos para os VMs se implantarem.

## <a name="create-a-public-ip-address---standard"></a>Criar um endereço IP público - Standard

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. 

Utilizar [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para:

* Crie um endereço IP público redundante de zona padrão chamado **myPublicIP**.
* In **CreatePubLBQs-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Para criar um endereço IP público redundante zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

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
* Associado ao endereço IP público **myPublicIP** que criou no passo anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
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
    --resource-group CreatePubLBQS-rg \
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
    --resource-group CreatePubLBQS-rg \
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
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicione máquinas virtuais para carregar piscina de backend balancer

Adicione as máquinas virtuais à piscina de backend com [a az network nic ip-config endereço-pool add:](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)

* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Criar configuração de regras de saída
As regras de saída do balançador de carga configuram sNAT de saída para VMs na piscina de backend. 

Para obter mais informações sobre as ligações de saída, consulte [as ligações de saída em Azure](load-balancer-outbound-connections.md).

Um IP ou prefixo público pode ser usado para a configuração de saída.

### <a name="public-ip"></a>IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um único IP para a conectividade de saída.  

* Denominado **myPublicIPOutbound.**
* In **CreatePubLBQs-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Para criar um endereço IP público redundante zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Prefixo IP público

Utilize [o prefixo ip da rede az](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) para criar um prefixo IP público para a conectividade de saída.

* Denominado **myPublicIPPrefixOutbound**.
* In **CreatePubLBQs-rg**.
* Comprimento do prefixo de **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Para criar um prefixo IP público redundante zonal na Zona 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Para obter mais informações sobre a escala de ACESSO NAT e conectividade de saída, consulte [o SCALE Outbound NAT com vários endereços IP](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Criar configuração IP frontend de saída

Crie uma nova configuração IP frontend com [a criação de frontend-ip da rede Az ](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create)lb :

Selecione os comandos de prefixo IP públicos ou ip com base na decisão em passo anterior.

#### <a name="public-ip"></a>IP público

* Chama-se **myFrontEndOutbound.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado ao endereço IP público **myPublicIPOutbound**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefixo de IP público

* Chama-se **myFrontEndOutbound.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado ao prefixo IP público **myPublicIPPrefixOutbound**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Criar piscina de saída

Crie uma nova piscina de saída com [a criação de endereços lb de rede Az:](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create)

* Denominado **myBackEndPoolOutbound**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Criar regra de saída

Crie uma nova regra de saída para a piscina de backend de saída com [a az rede lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create):

* Chama-se **myOutboundRule.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado com o balanceador de carga **myLoadBalancer**
* Associado com frontend **myFrontEndOutbound**.
* Protocolo **Tudo**.
* Intervalo de tempo de **15.**
* **10.000** portos de saída.
* Associado com backend pool **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Adicione máquinas virtuais à piscina de saída

Adicione as máquinas virtuais à piscina de saída com [a az network nic ip-config endereço-pool add:](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)


* Na piscina de endereços **backend myEndEndPoolOutbound**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

## <a name="configure-virtual-network---basic"></a>Configure rede virtual - Básico

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando [a rede az vnet criar:](/cli/azure/network/vnet#az-network-vnet-create)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **CreatePubLBQS-rg.**
* Localização do **eastus.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público para o anfitrião do bastião:

* Crie um endereço IP público redundante de zona padrão chamado **myBastionIP**.
* In **CreatePubLBQs-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Criar uma sub-rede de bastião

Utilize [a sub-rede vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) de rede az para criar uma sub-rede de bastião:

* Denominado **AzureBastionSubnet**.
* Prefixo de endereço de **10.1.1.0/24**.
* Na rede virtual **myVNet.**
* No grupo de recursos **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Criar hospedeiro de bastião

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az-network-bastion-create) para criar um hospedeiro de bastião:

* Chamado **myBastionHost.**
* In **CreatePubLBQs-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet.**
* Na localização **leste.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
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
* No grupo de recursos **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Criar uma regra de grupo de segurança de rede utilizando [a regra az network nsg criar:](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)

* Denominado **myNSGRuleHTTP**.
* No grupo de segurança de rede que criou no passo anterior, o **myNSG.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Protocolo **(*)**.
* Direção **Entrada**.
* Fonte **(*)**.
* Destino **(*)**.
* Porto de destino **Port 80**.
* **Acesso Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
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
* No grupo de recursos **CreatePubLBQS-rg**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Crie o conjunto de disponibilidade com [az vm disponibilidade-set criar](/cli/azure/vm/availability-set#az-vm-availability-set-create):

* Chamado **myAvSet**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Localização **leste.**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm criar:](/cli/azure/vm#az-vm-create)

### <a name="vm1"></a>VM1
* Chamado **myVM1**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Anexado à interface de rede **myNicVM1**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 1.**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Chamado **myVM2**.
* No grupo de recursos **CreatePubLBQS-rg**.
* Anexado à interface de rede **myNicVM2**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 2.**

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamado **myVM3.**
* No grupo de recursos **CreatePubLBQS-rg**.
* Anexado à interface de rede **myNicVM3**.
* Imagem de máquina virtual **ganha centro de dados2019**.
* Na **Zona 3.**

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
Pode levar alguns minutos para os VMs se implantarem.

## <a name="create-a-public-ip-address---basic"></a>Criar um endereço IP público - Básico

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. 

Utilizar [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para:

* Crie um endereço IP público redundante de zona padrão chamado **myPublicIP**.
* In **CreatePubLBQs-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

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
* Associado ao endereço IP público **myPublicIP** que criou no passo anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
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
    --resource-group CreatePubLBQS-rg \
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
    --resource-group CreatePubLBQS-rg \
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
* No grupo de recursos **CreatePubLBQS-rg**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

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
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Para obter o endereço IP público do balanceador de carga, utilize [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

Copie o endereço IP público e cole-o na barra de endereço do browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testar o balanceador de carga" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize o comando de eliminação do [grupo AZ](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o equilibrador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido

* Criou um balanceador de carga padrão ou público
* Máquinas virtuais anexadas. 
* Configurado a regra de tráfego do balanceador de carga e a sonda de saúde.
* Testei o equilibrador de carga.

Para saber mais sobre o Azure Load Balancer, continue a:
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)