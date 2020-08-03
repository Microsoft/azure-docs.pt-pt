---
title: Criar um Balanceador de Carga interno - Azure CLI
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a criar um equilibrador de carga interno usando O Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2020
ms.author: allensu
ms.openlocfilehash: 9a1587efc07cf852fe9389cc9bf39e693373ef89
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502329"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Criar um balanceador de carga interno para balanceamento de carga de VMs através da CLI do Azure

Este artigo mostra como criar um balanceador de carga interno para efetuar o balanceamento de carga de VMs. Para testar o balanceador de carga, implemente duas máquinas virtuais (VMs) com o servidor Ubuntu para fazer o balanceamento de carga de uma aplicação Web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupILB* na localização *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com o nome *myVnet*, com uma sub-rede de nome *mySubnet* em *myResourceGroup*, através de [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```

## <a name="create-standard-load-balancer"></a>Criar o Balanceador de Carga Standard

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
  - uma configuração de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - um conjunto de IPs de back-end no qual o conjunto do front-end envia o tráfego de rede com balanceamento de carga.
  - uma sonda de estado de funcionamento que determina o estado de funcionamento das instâncias de VM de back-end.
  - uma regra de balanceador de carga que define como o tráfego é distribuído pelas VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Crie um Balancer de Carga interno com [a az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) chamado **myLoadBalancer** que inclui uma configuração IP frontend chamada **myFrontEnd**, uma piscina traseira chamada **myBackEndPool** que está associada a um endereço IP privado **10.0.0.7**. 

Utilize `--sku basic` para criar um Balanceador de Carga Básico. A Microsoft recomenda o Standard SKU para cargas de trabalho de produção.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --sku standard \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem receber o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma sonda de estado de funcionamento com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorizar o estado de funcionamento das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myHTTPRule* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no conjunto de front-end *myFrontEnd* e enviar o tráfego de rede com balanceamento de carga para o conjunto de endereços back-end *myBackEndPool* também através da porta 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

Também pode criar uma regra de balançador de carga [de portas HA](load-balancer-ha-ports-overview.md) utilizando a configuração abaixo com o Balanceador de Carga Padrão.

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupILB --lb-name myLoadBalancer --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name myFrontEnd --backend-address-pool-name myBackEndPool
```

## <a name="create-servers-for-the-backend-address-pool"></a>Criar servidores para o conjunto de endereços de back-end

Antes de implementar algumas VMs e testar o balanceador de carga, crie os recursos de rede virtual de apoio.

### <a name="create-nics"></a>Criar NICs

Crie duas interfaces de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe as mesmas ao endereço IP privado. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, o utilizador cria duas máquinas virtuais para serem utilizadas como servidores de back-end para o balanceador de carga. Para verificar se um balanceador de carga foi criado com êxito, instale também o NGINX nas máquinas virtuais.

### <a name="create-an-availability-set"></a>Criar um Conjunto de disponibilidade

Crie um conjunto de disponibilidade com [az vm availabilityset create](/cli/azure/network/nic)

```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

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
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```

Poderão ser necessários alguns minutos para que as VMs sejam implementadas.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Criar uma VM para testar o balanceador de carga

Para testar o balanceador de carga, crie uma máquina virtual, *myVMTest* e associe-a a *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Testar o balanceador de carga interno

Para testar o balanceador de carga, tem de começar por obter o endereço IP privado do balanceador de carga. De seguida, inicie sessão na máquina virtual myVMTest e escreva o endereço IP privado na barra de endereço do respetivo browser.

Para obter o endereço IP privado do balanceador de carga, utilize [az network lb show](/cli/azure/network/public-ip). Copie o endereço IP privado e, em seguida, cole-o na barra de endereço de um browser da sua máquina virtual *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
```

![Testar o balanceador de carga](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um Balanceador de Carga Básico interno, anexou VMs ao mesmo, configurou a regra de tráfego do balanceador de carga, a sonda de estado de funcionamento e, em seguida, testou o balanceador de carga. Para saber mais sobre balanceadores de carga e recursos associados, avance para os artigos de instruções.
