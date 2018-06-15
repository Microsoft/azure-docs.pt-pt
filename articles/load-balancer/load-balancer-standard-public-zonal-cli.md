---
title: Criar um padrão de Balanceador de público de carga com zonal IP público endereço front-end utilizando a CLI do Azure | Microsoft Docs
description: Saiba como criar um padrão de Balanceador de público de carga com zonal IP público endereço front-end utilizando a CLI do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 0932195bb95ab9610f723245bfed7fedb01001f9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
ms.locfileid: "30324097"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Criar um padrão de Balanceador de público de carga com zonal front-end utilizando a CLI do Azure

Os passos neste artigo através da criação de um público [padrão de Balanceador de carga](https://aka.ms/azureloadbalancerstandard) com zonal front-end utilizando um endereço IP público padrão. Neste cenário, especifique uma zona específica para as instâncias de front-end e back-end, alinhar o caminho de dados e recursos com uma zona específica.

Para obter mais informações sobre a utilização de zonas de disponibilidade com o Balanceador de carga padrão, consulte [padrão Balanceador de carga e zonas de disponibilidade](load-balancer-standard-availability-zones.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, certifique-se de que instalou a versão mais recente [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e tem sessão iniciada numa conta do Azure com [início de sessão az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Suporte para as zonas de disponibilidade está disponível para famílias de tamanho VM, selecionados de recursos do Azure e regiões. Para obter mais informações sobre como começar a utilizar e os recursos do Azure, regiões e famílias de tamanho VM pode tentar zonas de disponibilidade com, consulte [zonas de descrição geral da disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupLB* no *westeurope* localização:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Criar um zonal pública IP padrão
Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Existe um endereço de IP público é sempre criado numa zona específica apenas em que zona. Não é possível alterar o horário de um endereço IP público.

Crie um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). O exemplo seguinte cria um endereço IP público zonal denominado *myPublicIP* no *myResourceGroupLoadBalancer* grupo de recursos na zona 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-load-balancer-standard"></a>Criar o padrão de Balanceador de carga do Azure
Esta secção fornece detalhes sobre como pode criar e configurar os seguintes componentes do Balanceador de carga:
- um conjunto IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
- tráfego de rede com balanceamento de um conjunto IP back-end em que o conjunto de front-end envia a carga.
- uma pesquisa de estado de funcionamento que determina o estado de funcionamento de instâncias de VM de back-end.
- regra de Balanceador de carga que define a forma como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer"></a>Criar o Balanceador de carga
Criar um balanceador de carga Standard com [az rede lb criar](/cli/azure/network/lb#az_network_lb_create). O exemplo seguinte cria um balanceador de carga com o nome *myLoadBalancer* e atribui o *myPublicIP* endereço para a configuração de IP Front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Criar a sonda de estado de funcionamento na porta 80

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Criar uma sonda do Estado de funcionamento com pesquisa de lb az rede criar para monitorizar o estado de funcionamento das máquinas virtuais. Para criar uma sonda de estado de funcionamento TCP, utilize [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). O exemplo seguinte cria uma sonda de estado de funcionamento com o nome *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Criar regra de Balanceador de carga para a porta 80
Uma regra de Balanceador de carga define a configuração de IP Front-end para o tráfego de entrada e o conjunto IP back-end para receber o tráfego, juntamente com a porta de origem e de destino necessário. Criar uma regra de Balanceador de carga *myLoadBalancerRuleWeb* com [criar regra de lb az rede](/cli/azure/network/lb/rule#az_network_lb_rule_create) para a escuta a porta 80 no conjunto de front-end *myFrontEndPool* e enviar tráfego de rede com balanceamento de carga para o conjunto de endereços de back-end *myBackEndPool* também utilizando a porta 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Antes de implementar algumas VMs e pode testar o seu Balanceador de carga, crie os suporte recursos de rede virtual.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual denominada *myVnet* com uma sub-rede denominada *mySubnet* em myResourceGroup utilizando [az rede vnet criar](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar o grupo de segurança de rede com o nome *myNetworkSecurityGroup* definir ligações de entrada à sua rede virtual com [az rede nsg criar](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Criar uma regra de grupo de segurança de rede com o nome *myNetworkSecurityGroupRule* para a porta 80 com [criar regras de nsg de rede az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
Criar o NICs virtuais três com [nic da rede az criar](/cli/azure/network/nic#az_network_nic_create) e associá-las com o endereço IP público e o grupo de segurança de rede. O exemplo seguinte cria três NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Criar servidores de back-end
Neste exemplo, crie três máquinas virtuais localizadas na zona 1 para ser utilizada como servidores de back-end de Balanceador de carga. Também instalar NGINX nas máquinas virtuais para verificar que o Balanceador de carga foi criado com êxito.

### <a name="create-cloud-init-config"></a>Criar configuração de inicialização da cloud

Pode utilizar um ficheiro de configuração de nuvem init para instalar NGINX e executar uma aplicação Node.js 'Olá, mundo' numa máquina virtual Linux. Na sua shell atual, crie um ficheiro denominado init.txt de nuvem e copie e cole a seguinte configuração o shell. Certifique-se que copie totalidade nuvem init ficheiro corretamente, especialmente a primeira linha:

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

### <a name="create-the-zonal-virtual-machines"></a>Criar as máquinas virtuais zonal
Criar as VMs com [az vm criar](/cli/azure/vm#az_vm_create). O exemplo seguinte cria três VMs na zona 1 e gera chaves SSH, caso ainda não existam:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
--custom-data cloud-init.txt
done
```

## <a name="test-the-load-balancer"></a>Testar o Balanceador de carga
Obter o endereço IP público da utilização de Balanceador de carga [mostrar de ip público de rede az](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

Em seguida, pode introduzir o endereço IP público num browser. Não se esqueça de que demora alguns minutos até as VMs estarem prontas antes de o balanceador de carga começar a distribuir o tráfego pelas mesmas. A aplicação é apresentada, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Executar aplicação Node.js](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Para ver o Balanceador de carga distribuir o tráfego para VMs dentro da zona 1 que estejam a executar a aplicação, pode forçar atualizar o browser.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [padrão Balanceador de carga](./load-balancer-standard-overview.md).



