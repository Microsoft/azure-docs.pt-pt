---
title: Balanceamento de carga de VMs com redundância de zona com a CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga Standard público com zona redundante front-end com a CLI do Azure
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: 8f1bf9b9070f2db2376de9cb0a0602eaea98b47e
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592932"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Balancear carga de VMs em todas as zonas de disponibilidade com a CLI do Azure

Este artigo mostra-se através da criação de um público [Balanceador de carga Standard](https://aka.ms/azureloadbalancerstandard) com um front-end com redundância de zona para obter redundância de zona sem dependência de vários registos DNS. Um único endereço IP de front-end é automaticamente com redundância de zona.  Com um front-end de com redundância de zona para o Balanceador de carga, com um único endereço IP agora pode aceder qualquer VM numa rede virtual numa região que está em todas as zonas de disponibilidade. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para obter mais informações sobre a utilização das Zonas de disponibilidade com o Balanceador de Carga Standard, veja [Balanceador de Carga Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da CLI do Azure versão 2.0.17 ou superior.  Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Suporte para zonas de disponibilidade está disponível para os recursos do Azure selecionados e regiões e famílias de tamanhos VM. Para obter mais informações sobre como começar a utilizar e quais recursos do Azure, regiões e famílias de tamanhos VM pode experimentar com as zonas de disponibilidade, consulte [descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroupSLB* no *westeurope* localização:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Criar uma zona Standard público com redundância IP
Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Um com redundância de zona front-end é atendido por todas as zonas de disponibilidade numa região ao mesmo tempo. Criar uma zona com redundância de endereço IP pública com [criar a rede de az public-ip](/cli/azure/network/public-ip#az-network-public-ip-create). Quando cria um endereço IP público Standard, é zona redundante por predefinição.

O exemplo seguinte cria um zona com redundância de endereço IP público com o nome *myPublicIP* no *myResourceGroupLoadBalancer* grupo de recursos.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Criar Balanceador de carga Standard do Azure
Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
- Um conjunto de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
- um conjunto de IPs de back-end no qual o conjunto do front-end envia o tráfego de rede com balanceamento de carga.
- uma sonda de estado de funcionamento que determina o estado de funcionamento das instâncias de VM de back-end.
- uma regra de balanceador de carga que define como o tráfego é distribuído pelas VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga
Crie um balanceador de carga Standard com [az rede lb criar](/cli/azure/network/lb#az-network-lb-create). O exemplo seguinte cria um balanceador de carga com o nome *myLoadBalancer* e atribui a *myPublicIP* endereço para a configuração de IP Front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Criar a sonda de estado de funcionamento na porta 80

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Criar uma sonda de estado de funcionamento com a sonda de lb de rede de az criar para monitorizar o estado de funcionamento das máquinas virtuais. Para criar uma sonda de estado de funcionamento TCP, utilize [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). O exemplo seguinte cria uma sonda de estado de funcionamento com o nome *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Criar regra de Balanceador de carga para a porta 80
Uma regra de balanceador de carga define a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myLoadBalancerRuleWeb* com [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) para escutar a porta 80 no conjunto de front-end *myFrontEndPool* e enviar o tráfego de rede com balanceamento de carga para o conjunto de endereços back-end *myBackEndPool* também através da porta 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Antes de implementar algumas VMs e testar o balanceador de carga, crie os recursos de rede virtual de apoio.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual denominada *myVnet* com uma sub-rede denominada *mySubnet* no myResourceGroup com [az rede vnet criar](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar grupo de segurança de rede com o nome *myNetworkSecurityGroup* para definir as conexões de entrada à sua rede virtual com [az rede nsg criar](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Criar uma regra de grupo de segurança de rede com o nome *myNetworkSecurityGroupRule* para a porta 80 com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
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
Criar três NICs virtuais com [nic da rede de az criar](/cli/azure/network/nic#az-network-nic-create) e associá-las com o endereço IP público e o grupo de segurança de rede. O exemplo seguinte cria seis NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Criar servidores de back-end
Neste exemplo, vai criar três máquinas de virtuais localizadas na zona 1, zona 2 e zona 3 para ser utilizada como servidores de back-end do Balanceador de carga. Instale também o NGINX nas máquinas virtuais para verificar se o Balanceador de carga foi criado com êxito.

### <a name="create-cloud-init-config"></a>Criar configuração de inicialização da cloud

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

### <a name="create-the-zonal-virtual-machines"></a>Criar as máquinas virtuais zonais
Criar as VMs com [az vm criar](/cli/azure/vm#az-vm-create) na zona 1, zona 2 e zona 3. O exemplo seguinte cria uma VM em cada zona e gera chaves SSH caso estas ainda não existam:

Criar uma VM em cada zona (zona 1, zona 2 e zona 3) do *westeurope* localização.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Obtenha o endereço IP público da utilização de Balanceador de carga [show de public-ip de rede de az](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Em seguida, pode introduzir o endereço IP público num browser. Não se esqueça de que demora alguns minutos até as VMs estarem prontas antes de o balanceador de carga começar a distribuir o tráfego pelas mesmas. A aplicação é apresentada, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Executar aplicação Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Para ver o Balanceador de carga a distribuir tráfego pelas VMs em todos os três zonas de disponibilidade com a sua aplicação, pode parar uma VM numa zona específica e atualize o browser.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [Balanceador de Carga Standard](./load-balancer-standard-overview.md)



