---
title: Balancear a carga de VMs com redundância de zona usando CLI do Azure
titleSuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer público com frontend com redundância de zona usando CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: af327f751a0af67b6d17330dbaeb717df8660bfd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225274"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>VMs de balanceamento de carga em todas as zonas de disponibilidade usando CLI do Azure

Este artigo percorre como criar um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com um front-end com redundância de zona para obter redundância de zona sem dependência em vários registros DNS. Um único endereço IP de front-end tem redundância de zona automaticamente.  Usando um front-end com redundância de zona para o balanceador de carga, com um único endereço IP, agora você pode acessar qualquer VM em uma rede virtual dentro de uma região que esteja em todos os Zonas de Disponibilidade. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para obter mais informações sobre a utilização das Zonas de disponibilidade com o Balanceador de Carga Standard, veja [Balanceador de Carga Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando CLI do Azure versão 2.0.17 ou superior.  Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Suporte para zonas de disponibilidade está disponível para os recursos do Azure selecionados e regiões e famílias de tamanhos VM. Para obter mais informações sobre como começar e quais recursos do Azure, regiões e famílias de tamanho de VM você pode experimentar zonas de disponibilidade, consulte [visão geral do zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupSLB* no local *westeurope* :

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Criar um IP público com redundância de zona padrão
Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Um front-end com redundância de zona é servido por todas as zonas de disponibilidade em uma região simultaneamente. Crie um endereço IP público com redundância de zona com [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Quando você cria um endereço IP público padrão, ele tem redundância de zona por padrão.

O exemplo a seguir cria um endereço IP público com redundância de zona chamado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer* .

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Criar Standard Load Balancer do Azure
Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
- Um conjunto de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
- um conjunto de IPs de back-end no qual o conjunto do front-end envia o tráfego de rede com balanceamento de carga.
- uma sonda de estado de funcionamento que determina o estado de funcionamento das instâncias de VM de back-end.
- uma regra de balanceador de carga que define como o tráfego é distribuído pelas VMs.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga
Crie um balanceador de carga padrão com [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). O exemplo a seguir cria um balanceador de carga chamado *myLoadBalancer* e atribui o endereço *myPublicIP* à configuração de IP de front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Criar investigação de integridade na porta 80

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma investigação de integridade com AZ Network lb Probe Create para monitorar a integridade das máquinas virtuais. Para criar uma sonda de estado de funcionamento TCP, utilize [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). O exemplo seguinte cria uma sonda de estado de funcionamento com o nome *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Criar regra de balanceador de carga para a porta 80
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

Crie uma rede virtual chamada *myVnet* com uma sub-rede denominada *mysubnet* no MyResource, usando [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede chamado *myNetworkSecurityGroup* para definir conexões de entrada para sua rede virtual com [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Crie uma regra de grupo de segurança de rede chamada *myNetworkSecurityGroupRule* para a porta 80 com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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
Crie três NICs virtuais com [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) e associe-as ao endereço IP público e ao grupo de segurança de rede. O exemplo a seguir cria seis NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

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
Neste exemplo, você cria três máquinas virtuais localizadas na zona 1, zona 2 e zona 3 a serem usadas como servidores de back-end para o balanceador de carga. Você também instala o NGINX nas máquinas virtuais para verificar se o balanceador de carga foi criado com êxito.

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
Crie as VMs com [AZ VM Create](/cli/azure/vm#az-vm-create) na zona 1, zona 2 e zona 3. O exemplo a seguir cria uma VM em cada zona e gera chaves SSH, se elas ainda não existirem:

Crie uma VM em cada zona (zona 1, zona 1 e zona 3) do local *westeurope* .

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

Obtenha o endereço IP público do balanceador de carga usando [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Em seguida, pode introduzir o endereço IP público num browser. Não se esqueça de que demora alguns minutos até as VMs estarem prontas antes de o balanceador de carga começar a distribuir o tráfego pelas mesmas. A aplicação é apresentada, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Executar aplicação Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Para ver o balanceador de carga distribuir o tráfego entre as VMs em todas as três zonas de disponibilidade que executam seu aplicativo, você pode interromper uma VM em uma zona específica e atualizar o navegador.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [Balanceador de Carga Standard](./load-balancer-standard-overview.md)



