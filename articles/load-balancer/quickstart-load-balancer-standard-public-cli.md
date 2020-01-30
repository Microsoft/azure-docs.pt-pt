---
title: Quickstart:Criar um Balancer de Carga pública - Azure CLI
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga público com a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 8ef24630d255876c45d9cbc072fc989288f2ac5f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837265"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Quickstart: Criar um Balancer de Carga Padrão para carregar VMs de equilíbrio utilizando o Azure CLI

Este arranque rápido mostra-lhe como criar um Equilibrador de Carga público. Para testar o balanceador de carga, implemente duas máquinas virtuais (VMs) com o servidor Ubuntu e faça o balanceamento de carga de uma aplicação Web entre as duas VMs.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupSLB* na localização *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. Use a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um endereço IP público redundante da zona Standard chamado *myPublicIP* no *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Para criar um endereço IP público zonal na utilização da zona 1:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

 Use ```--sku basic``` para criar um IP Público Básico. O básico não suporta zonas de disponibilidade. A Microsoft recomenda o Standard SKU para cargas de trabalho de produção.

## <a name="create-azure-load-balancer"></a>Criar um equilibrador de carga Azure

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
  - Um conjunto de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - um conjunto de IPs de back-end no qual o conjunto do front-end envia o tráfego de rede com balanceamento de carga.
  - uma sonda de estado de funcionamento que determina o estado de funcionamento das instâncias de VM de back-end.
  - uma regra de balanceador de carga que define como o tráfego é distribuído pelas VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Crie um Balanceador de Carga do Azure público com [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) com o nome **myLoadBalancer** que inclua um conjunto de front-end com o nome **myFrontEnd**, um conjunto de back-end com o nome **myBackEndPool** associado ao endereço IP público **myPublicIP** que criou no passo anterior. Use ```--sku basic``` para criar um IP Público Básico. A Microsoft recomenda o Standard SKU para cargas de trabalho de produção.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma sonda de estado de funcionamento com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorizar o estado de funcionamento das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myLoadBalancerRuleWeb* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no conjunto de front-end *myFrontEnd* e enviar o tráfego de rede com balanceamento de carga para o conjunto de endereços back-end *myBackEndPool* também através da porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar algumas VMs e testar o balanceador de carga, crie os recursos de rede virtual de apoio.

### <a name="create-a-virtual-network"></a>Criar rede virtual

Crie uma rede virtual com o nome *myVnet*, com uma sub-rede de nome *mySubnet* em *myResourceGroup*, através de [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Balanceador de Carga Standard, as VMs no endereço de back-end têm de ter NICs que pertençam a um grupo de Segurança de Rede. Crie um grupo de segurança de rede para definir ligações recebidas para a sua rede virtual.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra do grupo de segurança de rede para permitir ligações de entrada através da porta 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>Criar NICs

Crie três interfaces de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe as mesmas ao endereço IP público e ao grupo de segurança de rede. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, o utilizador cria três máquinas virtuais para serem utilizadas como servidores de back-end para o balanceador de carga. Para verificar se um balanceador de carga foi criado com êxito, instale também o NGINX nas máquinas virtuais.

Se estiver a criar um Balancer de Carga Básico com um IP Público Básico, terá de criar um Conjunto de Disponibilidade utilizando[(az vm disponibilidade cria](/cli/azure/network/nic) para adicionar as suas máquinas virtuais. Os equilibradores de carga padrão não requerem este passo adicional. A Microsoft recomenda a utilização do Standard.

### <a name="create-three-virtual-machines"></a>Criar três máquinas virtuais

Pode utilizar o ficheiro de configuração cloud-init para instalar o NGINX e executar uma aplicação Node.js "Hello World" numa máquina virtual Linux. Na shell atual, crie um ficheiro com o nome cloud-init.txt e copie e cole a seguinte configuração na shell. Certifique-se de que copia o ficheiro cloud-init completo corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az-vm-create).

 ```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```
Poderão ser necessários alguns minutos para que as VMs sejam implementadas.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Para obter o endereço IP público do balanceador de carga, utilize [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copie o endereço IP público e cole-o na barra de endereço do browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![Testar o balanceador de carga](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você criou um Standard Load Balancer, as VMs anexadas a ele, configurou a regra de tráfego Load Balancer, a investigação de integridade e testou a Load Balancer. Para saber mais sobre o Azure Load Balancer, continue a colocar [os tutoriais do Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

Saiba mais sobre as zonas de [Balancer de Carga e Disponibilidade.](load-balancer-standard-availability-zones.md)