---
title: 'Quickstart: Criar um equilibrador de carga interno - Azure CLI'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga interno utilizando o Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788840"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Quickstart: Criar um equilibrador de carga interno utilizando o Azure CLI

Começa com o Azure Load Balancer utilizando o CLI Azure para criar um equilibrador de carga interno e três máquinas virtuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Se estiver a utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos Azure é um recipiente lógico no qual implanta e gere os seus recursos Azure.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Nomeie o grupo de recursos **CreateIntLBQS-rg**, e especifique a localização como **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. Quando cria um equilibrador de carga interno, uma rede virtual é configurada como a rede para o equilibrador de carga. O diagrama a seguir mostra os recursos criados neste arranque rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Recursos padrão do balanceador de carga criados para o arranque rápido." border="false":::

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Antes de implementar VMs e implementar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

#### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual utilizando [a rede Az vnet create](/cli/azure/network/vnet#az_network_vnet_create). Especifique o seguinte:

* Nomeado **myVNet**
* Prefixo de endereço de **10.1.0.0/16**
* Sub-rede chamada **myBackendSubnet**
* Prefixo de sub-rede de **10.1.0.0/24**
* No grupo de recursos **CreateIntLBQS-rg**
* Localização do **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o anfitrião Azure Bastion. Especifique o seguinte:

* Criar um endereço IP público redundante de zona padrão chamado **myBastionIP**
* Em **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Criar uma sub-rede Azure Bastion

Utilize [a sub-rede de rede az para](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) criar uma sub-rede. Especifique o seguinte:

* Nomeado **AzureBastionSubnet**
* Prefixo de endereço de **10.1.1.0/24**
* Na rede virtual **myVNet**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Criar um anfitrião do Azure Bastion

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az_network_bastion_create) para criar um hospedeiro. Especifique o seguinte:

* Nomeado **myBastionHost**
* Em **CreateIntLBQS-rg**
* Associado ao IP **público myBastionIP**
* Associado com rede virtual **myVNet**
* Na localização **leste**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, certifique-se de que os seus VMs têm interfaces de rede que pertencem a um grupo de segurança de rede. Criar um grupo de segurança de rede utilizando [a rede az nsg criar](/cli/azure/network/nsg#az_network_nsg_create). Especifique o seguinte:

* Nomeado **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede utilizando [a regra nsg de rede az criar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Especifique o seguinte:

* Nomeado **myNSGRuleHTTP**
* No grupo de segurança de rede que criou no passo anterior, o **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**
* Protocolo **(*)**
* Entrada **de direção**
* Fonte **(*)**
* Destino **(*)**
* Porto de destino **Porto 80**
* **Acesso Permitir**
* Prioridade **200**

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

### <a name="create-back-end-servers"></a>Criar servidores de back-end

Nesta secção, cria-se:

* Três interfaces de rede para as máquinas virtuais.
* Três máquinas virtuais a serem utilizadas como servidores para o equilibrador de carga.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Criar três interfaces de rede com [a az network nic criar.](/cli/azure/network/nic#az_network_nic_create) Especifique o seguinte:

* Nomeado **myNicVM1**, **myNicVM2,** e **myNicVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Na rede virtual **myVNet**
* Na sub-rede **myBackendSubnet**
* No grupo de segurança de rede **myNSG**

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

#### <a name="create-the-virtual-machines"></a>Criar as máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az_vm_create). Especifique o seguinte:

* Nomeado **myVM1**, **myVM2**, e **myVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Ligado à interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**
* Imagem de máquina virtual **ganha centro de dados de 2019**
* Na **Zona 1**, **Zona 2** e **Zona 3**

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

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

* Um pool IP que recebe o tráfego de rede de entrada no equilibrador de carga.
* Uma segunda piscina IP, onde a primeira piscina envia o tráfego de rede equilibrado de carga.
* Uma sonda de saúde que determina a saúde dos casos de VM.
* Uma regra do balançador de carga que define como o tráfego é distribuído para os VMs.

#### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um equilibrador de carga pública com [a az network lb create](/cli/azure/network/lb#az_network_lb_create). Especifique o seguinte:

* Nomeado **myLoadBalancer**
* Uma piscina chamada **myFrontEnd**
* Uma piscina chamada **myBackEndPool**
* Associado à rede virtual **myVNet**
* Associado com a sub-rede **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [sonda LB de rede az criar](/cli/azure/network/lb/probe#az_network_lb_probe_create). Especifique o seguinte:

* Monitoriza a saúde das máquinas virtuais
* Nomeado **myHealthProbe**
* **Protocolo TCP**
* Porto de monitorização **80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balançador de carga define:

* A configuração IP para o tráfego de entrada.
* A piscina IP para receber o tráfego.
* A fonte necessária e o porto de destino. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Especifique o seguinte:

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina **myFrontEnd**
* Envio de tráfego de rede equilibrado para a piscina de **endereços myBackEndPool** utilizando o **Porto 80** 
* Usando sonda de saúde **myHealthProbe**
* **Protocolo TCP**
* Tempo de **15 minutos** inativo
* Ativar o reset do TCP

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
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>Adicione VMs à piscina do balançador de carga

Adicione as máquinas virtuais à piscina traseira com [a az rede nic ip-config endereço-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Especifique o seguinte:

* Na piscina **de endereços myBackEndPool**
* No grupo de recursos **CreateIntLBQS-rg**
* Associado com interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**
* Associado com o balanceador de carga **myLoadBalancer**

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
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. Quando cria um equilibrador de carga interno, uma rede virtual é configurada como a rede para o equilibrador de carga. O diagrama a seguir mostra os recursos criados neste arranque rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Recursos básicos do balanceador de carga criados para o arranque rápido." border="false":::

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Antes de implementar VMs e implementar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

#### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual utilizando [a rede Az vnet create](/cli/azure/network/vnet#az_network_vnet_createt). Especifique o seguinte:

* Nomeado **myVNet**
* Prefixo de endereço de **10.1.0.0/16**
* Sub-rede chamada **myBackendSubnet**
* Prefixo de sub-rede de **10.1.0.0/24**
* No grupo de recursos **CreateIntLBQS-rg**
* Localização do **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o anfitrião Azure Bastion. Especifique o seguinte:

* Criar um endereço IP público redundante de zona padrão chamado **myBastionIP**
* Em **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Criar uma sub-rede Azure Bastion

Utilize [a sub-rede de rede az para](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) criar uma sub-rede. Especifique o seguinte:

* Nomeado **AzureBastionSubnet**
* Prefixo de endereço de **10.1.1.0/24**
* Na rede virtual **myVNet**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Criar um anfitrião do Azure Bastion

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az_network_bastion_create) para criar um hospedeiro. Especifique o seguinte:

* Nomeado **myBastionHost**
* Em **CreateIntLBQS-rg**
* Associado ao IP **público myBastionIP**
* Associado com rede virtual **myVNet**
* Na localização **leste**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, certifique-se de que os seus VMs têm interfaces de rede que pertencem a um grupo de segurança de rede. Criar um grupo de segurança de rede utilizando [a rede az nsg criar](/cli/azure/network/nsg#az_network_nsg_create). Especifique o seguinte:

* Nomeado **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede utilizando [a regra nsg de rede az criar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Especifique o seguinte:

* Nomeado **myNSGRuleHTTP**
* No grupo de segurança de rede que criou no passo anterior, o **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**
* Protocolo **(*)**
* Entrada **de direção**
* Fonte **(*)**
* Destino **(*)**
* Porto de destino **Porto 80**
* **Acesso Permitir**
* Prioridade **200**

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

### <a name="create-back-end-servers"></a>Criar servidores de back-end

Nesta secção, cria-se:

* Três interfaces de rede para as máquinas virtuais.
* A disponibilidade definida para as máquinas virtuais.
* Três máquinas virtuais a serem utilizadas como servidores para o equilibrador de carga.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Criar três interfaces de rede com [a az network nic criar.](/cli/azure/network/nic#az_network_nic_create) Especifique o seguinte:

* Nomeado **myNicVM1**, **myNicVM2,** e **myNicVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Na rede virtual **myVNet**
* Na sub-rede **myBackendSubnet**
* No grupo de segurança de rede **myNSG**

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Crie o conjunto de disponibilidade para as máquinas virtuais

Crie o conjunto de disponibilidade com [a criação de disponibilidade az vm](/cli/azure/vm/availability-set#az_vm_availability_set_create). Especifique o seguinte:

* Nomeado **mySIlabilitySet**
* No grupo de recursos **CreateIntLBQS-rg**
* Localização **leste**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Criar as máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az_vm_create). Especifique o seguinte:

* Nomeado **myVM1**, **myVM2**, e **myVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Ligado à interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**
* Imagem de máquina virtual **ganha centro de dados de 2019**
* No **myDilabilitySet**


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

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

* Um pool IP que recebe o tráfego de rede de entrada no equilibrador de carga.
* Uma segunda piscina IP, onde a primeira piscina envia o tráfego de rede equilibrado de carga.
* Uma sonda de saúde que determina a saúde dos casos de VM.
* Uma regra do balançador de carga que define como o tráfego é distribuído para os VMs.

#### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um equilibrador de carga pública com [a az network lb create](/cli/azure/network/lb#az_network_lb_create). Especifique o seguinte:

* Nomeado **myLoadBalancer**
* Uma piscina chamada **myFrontEnd**
* Uma piscina chamada **myBackEndPool**
* Associado à rede virtual **myVNet**
* Associado com a sub-rede **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [sonda LB de rede az criar](/cli/azure/network/lb/probe#az_network_lb_probe_create). Especifique o seguinte:

* Monitoriza a saúde das máquinas virtuais
* Nomeado **myHealthProbe**
* **Protocolo TCP**
* Porto de monitorização **80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balançador de carga define:

* A configuração IP para o tráfego de entrada.
* A piscina IP para receber o tráfego.
* A fonte necessária e o porto de destino. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Especifique o seguinte:

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina **myFrontEnd**
* Envio de tráfego de rede equilibrado para a piscina de **endereços myBackEndPool** utilizando o **Porto 80** 
* Usando sonda de saúde **myHealthProbe**
* **Protocolo TCP**
* Tempo de **15 minutos** inativo

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Adicione VMs à piscina do balançador de carga

Adicione as máquinas virtuais à piscina traseira com [a az rede nic ip-config endereço-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Especifique o seguinte:

* Na piscina **de endereços myBackEndPool**
* No grupo de recursos **CreateIntLBQS-rg**
* Associado com interface de rede **myNicVM1,** **myNicVM2,** e **myNicVM3**
* Associado com o balanceador de carga **myLoadBalancer**

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

Crie a interface de rede com [a criação de um nic de rede az.](/cli/azure/network/nic#az_network_nic_create) Especifique o seguinte:

* Nomeado **myNicTestVM**
* No grupo de recursos **CreateIntLBQS-rg**
* Na rede virtual **myVNet**
* Na sub-rede **myBackendSubnet**
* No grupo de segurança de rede **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Crie a máquina virtual com [a criação de az vm](/cli/azure/vm#az_vm_create). Especifique o seguinte:

* Nomeado **myTestVM**
* No grupo de recursos **CreateIntLBQS-rg**
* Anexado à interface de rede **myNicTestVM**
* Imagem de máquina virtual **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Pode ter de esperar alguns minutos para que a máquina virtual se desloque.

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

2. Na página **'Vista Geral',** encontre o endereço IP privado para o balançador de carga. No menu à esquerda, selecione **Todos os serviços**  >    >  **MyLoadBalancer**.

3. Na visão geral do **myLoadBalancer,** copie o endereço ao lado **do Endereço IP Privado**.

4. No menu à esquerda, selecione **Todos os serviços**  >  **Todos os recursos**. A partir da lista de recursos, no grupo de recursos **CreateIntLBQS-rg,** selecione **myTestVM**.

5. Na página **'Vista Geral',** selecione **Connect**  >  **Bastion**.

6. Introduza o nome de utilizador e a palavra-passe que introduziu quando criou o VM.

7. No **myTestVM,** abra **o Internet Explorer.**

8. Introduza o endereço IP a partir do passo anterior na barra de endereços do navegador. A página predefinição do servidor web IIS é mostrada no navegador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Screenshot do endereço IP na barra de endereço do navegador." border="true":::
   
Para ver o balanceador de carga distribuir tráfego através dos três VMs, pode personalizar a página predefinição de cada servidor web IIS de cada VM. Em seguida, refresque manualmente o seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os seus recursos já não forem necessários, utilize o comando de eliminação do [grupo az](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o equilibrador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Passos seguintes

Obtenha uma descrição geral do Balanceador de Carga do Azure.
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)
