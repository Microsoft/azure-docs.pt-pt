---
title: Tutorial - Load balance máquinas virtuais Linux em Azure
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar um balanceador de carga para uma aplicação segura de elevada disponibilidade em três máquinas virtuais do Linux
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 433bbd51618cfb5624c8ed2c549e1793488f0e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553770"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Tutorial: Fazer o balanceamento de carga de máquinas virtuais do Linux no Azure para criar uma aplicação de elevada disponibilidade com a CLI do Azure

O balanceamento de carga fornece um nível mais elevado de disponibilidade ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, vai conhecer os diferentes componentes do balanceador de carga do Azure que distribuem o tráfego e oferecem elevada disponibilidade. Saiba como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do balanceador de carga
> * Criar regras de tráfego do balanceador de carga
> * Utilizar a inicialização da cloud para criar uma aplicação Node.js básica
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover VMs de um balanceador de carga

Este tutorial utiliza o CLI dentro da [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente-a** a partir da parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Descrição geral do balanceador de carga do Azure
Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP) que fornece elevada disponibilidade ao distribuir o tráfego de entrada entre VMs em bom estado de funcionamento. Uma sonda de estado de funcionamento do balanceador de carga monitoriza uma porta especificada em cada VM e apenas distribui o tráfego para uma VM operacional.

Pode definir uma configuração de IP de front-end com um ou mais endereços IP públicos. Esta configuração de IP de front-end permite ao balanceador de carga e às aplicações estarem acessíveis através da Internet. 

As máquinas virtuais ligam a um balanceador de carga através da respetiva placa de interface de rede virtual (NIC). Para distribuir o tráfego pelas VMs, um conjunto de endereços de back-end contém os endereços IP das placas virtuais (NICs) ligadas ao balanceador de carga.

Para controlar o fluxo de tráfego, pode definir regras de balanceador de carga para portas específicas e protocolos que mapeiam para as suas VMs.

Se seguiu o tutorial anterior para [criar um conjunto de dimensionamento de máquinas virtuais](tutorial-create-vmss.md), foi criado um balanceador de carga. Todos estes componentes foram configurados como parte do conjunto de dimensionamento.


## <a name="create-azure-load-balancer"></a>Criar um balanceador de carga do Azure
Esta secção descreve como pode criar e configurar cada componente do balanceador de carga. Antes de poder criar o balanceador de carga, tem de criar um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupLoadBalancer* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Para aceder à sua aplicação na Internet, precisa de um endereço IP público para o balanceador de carga. Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo seguinte cria um endereço IP público designado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer*:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie um balanceador de carga com [az network lb create](/cli/azure/network/lb). O exemplo seguinte cria um balanceador de carga com o nome *myLoadBalancer* e atribui o endereço *myPublicIP* à configuração de IP de front-end:

```azurecli-interactive
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento
Para permitir ao balanceador de carga monitorizar o estado da aplicação, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. Por predefinição, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Pode criar uma sonda de estado de funcionamento com base num protocolo ou numa página de verificação de estado de funcionamento específica da aplicação. 

O exemplo seguinte cria uma sonda TCP. Também pode criar sondas HTTP personalizadas para obter verificações de estado de funcionamento mais detalhadas. Quando utilizar uma sonda HTTP personalizada, tem de criar a página de verificação de estado de funcionamento, tal como *healthcheck.js*. A sonda tem de devolver uma resposta **HTTP 200 OK** para o balanceador de carga manter o anfitrião na rotação.

Para criar uma sonda de estado de funcionamento TCP, utilize [az network lb probe create](/cli/azure/network/lb/probe). O exemplo seguinte cria uma sonda de estado de funcionamento com o nome *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Para garantir que apenas as VMs em bom estado de funcionamento recebem o tráfego, também pode definir a sonda de estado de funcionamento a utilizar.

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRule*, utiliza a sonda de estado de funcionamento *myHealthProbe* e faz o balanceamento de carga do tráfego na porta *80*:

```azurecli-interactive
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Antes de implementar algumas VMs e testar o balanceador, crie os recursos de rede virtual. Para obter mais informações sobre redes virtuais, veja o tutorial [Gerir Redes Virtuais do Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Criar recursos de rede
Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual com o nome *myVnet* com uma sub-rede denominada *mySubnet*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Para adicionar um grupo de segurança de rede, utilize [az network nsg create](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli-interactive
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Crie uma regra de grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule). O exemplo seguinte cria uma regra de grupo de segurança de rede com o nome *myNetworkSecurityGroupRule*:

```azurecli-interactive
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

As NICs virtuais são criadas com [az network nic create](/cli/azure/network/nic). O exemplo seguinte cria três NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar NICs virtuais e VMs adicionais em qualquer altura e adicioná-las ao balanceador de carga:

```azurecli
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Quando as três NICs virtuais estiverem criadas, avance para o passo seguinte


## <a name="create-virtual-machines"></a>Criar máquinas virtuais

### <a name="create-cloud-init-config"></a>Criar configuração de inicialização da cloud
Num tutorial anterior sobre [Como personalizar uma máquina virtual do Linux no primeiro arranque](tutorial-automate-vm-deployment.md), aprendeu a automatizar a personalização de VMs com inicialização da cloud. Pode utilizar o mesmo ficheiro de configuração de inicialização da cloud para instalar o NGINX e executar uma aplicação Node.js "Hello World" simples no próximo passo. Para ver o balanceador de carga em ação, no final do tutorial, pode aceder a esta aplicação simples num browser.

Na shell atual, crie um ficheiro com o nome *cloud-init.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

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

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a elevada disponibilidade da aplicação, coloque as VMs num conjunto de disponibilidade. Para obter mais informações sobre conjuntos de disponibilidade, veja o tutorial anterior [Como criar máquinas virtuais de elevada disponibilidade](tutorial-availability-sets.md).

Crie um conjunto de disponibilidade com [a criação de disponibilidade de az vm](/cli/azure/vm/availability-set). O exemplo seguinte cria um conjunto de disponibilidade com o nome *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Agora, pode criar as VMs com [az vm create](/cli/azure/vm). O exemplo seguinte cria três VMs e gera chaves SSH, caso não existam:

```azurecli
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. O parâmetro `--no-wait` não aguarda a conclusão de todas as tarefas. Poderão ser necessários mais alguns minutos antes de poder aceder à aplicação. A sonda de estado de funcionamento do balanceador de carga deteta automaticamente quando a aplicação está em execução em cada VM. Assim que a aplicação estiver em execução, a regra de balanceador de carga começa a distribuir o tráfego.


## <a name="test-load-balancer"></a>Testar o balanceador de carga
Pode obter o endereço IP público do balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Em seguida, pode introduzir o endereço IP público num browser. Não se esqueça de que demora alguns minutos até as VMs estarem prontas antes de o balanceador de carga começar a distribuir o tráfego pelas mesmas. A aplicação é apresentada, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Executar aplicação Node.js](./media/tutorial-load-balancer/running-nodejs-app.png)

Para ver o balanceador de carga a distribuir tráfego pelas três VMs que estão a executar a aplicação, pode forçar a atualização do browser.


## <a name="add-and-remove-vms"></a>Adicionar e remover VMs
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta secção mostra como adicionar ou remover uma VM do balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga
Pode remover uma VM do conjunto de endereços de back-end com [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool). O exemplo seguinte remove a NIC virtual para **myVM2** de *myLoadBalancer*:

```azurecli-interactive
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Para ver o balanceador de carga a distribuir tráfego pelas duas VMs restantes que estão a executar a aplicação, pode forçar a atualização do browser. Pode agora efetuar a manutenção da VM, como instalar atualizações do SO ou reiniciar.

Para ver uma lista de VMs com NICs virtuais ligadas ao balanceador de carga, utilize [az network lb address-pool show](/cli/azure/network/lb/address-pool). Consulte e filtre pelo ID da NIC virtual da seguinte forma:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

O resultado é semelhante ao exemplo seguinte, que mostra que a NIC virtual para a VM 2 já não faz parte do conjunto de endereços de back-end:

```output
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga
Depois de efetuar a manutenção da VM, ou se tiver de expandir a capacidade, pode adicionar uma VM ao conjunto de endereços de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool). O exemplo seguinte adiciona a NIC virtual para **myVM2** a *myLoadBalancer*:

```azurecli-interactive
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Para verificar se a NIC virtual está ligada ao conjunto de endereços de back-end, utilize novamente [az network lb address-pool show](/cli/azure/network/lb/address-pool) do passo anterior.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um balanceador de carga e anexou VMs ao mesmo. Aprendeu a:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do balanceador de carga
> * Criar regras de tráfego do balanceador de carga
> * Utilizar a inicialização da cloud para criar uma aplicação Node.js básica
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover VMs de um balanceador de carga

Avance para o próximo tutorial para saber mais sobre os componentes de rede virtual do Azure.

> [!div class="nextstepaction"]
> [Gerir VMs e redes virtuais](tutorial-virtual-network.md)
