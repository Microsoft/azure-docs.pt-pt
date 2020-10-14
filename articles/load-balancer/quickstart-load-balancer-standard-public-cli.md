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
ms.date: 08/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: d78b67cbd811ae0f3b7cea8aec119d05464c124a
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047799"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um balanceador de carga público para fazer o balanceamento de carga das VMs com a CLI do Azure

Começa com o Azure Load Balancer utilizando o Azure CLI para criar um equilibrador de carga pública e três máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI instalado localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

* Denominado **myResourceGroupLB**. 
* No **local leste.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando [a rede az vnet criar:](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **myResourceGroupLB.**
* Localização do **eastus.**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, os VMs no endereço backend para os fins de trás são obrigados a ter interfaces de rede que pertencem a um grupo de segurança de rede. 

Criar um grupo de segurança de rede utilizando [a rede az nsg criar:](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)

* Chamado **myNSG.**
* No grupo de recursos **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Criar uma regra de grupo de segurança de rede utilizando [a regra az network nsg criar:](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)

* Denominado **myNSGRuleHTTP**.
* No grupo de segurança de rede que criou no passo anterior, o **myNSG.**
* No grupo de recursos **myResourceGroupLB**.
* Protocolo **(*)**.
* Direção **Entrada**.
* Fonte **(*)**.
* Destino **(*)**.
* Porto de destino **Port 80**.
* **Acesso Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Criar três interfaces de rede com [a az network nic criar:](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)

#### <a name="vm1"></a>VM1

* Chamado **myNicVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Chamado **myNicVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Chamado **myNicVM3.**
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, cria-se:

* Um ficheiro de configuração em nuvem nomeado **cloud-init.txt** para a configuração do servidor.
* Três máquinas virtuais a serem utilizadas como servidores de backend para o equilibrador de carga.

### <a name="create-cloud-init-configuration-file"></a>Criar ficheiro de configuração de inimento de nuvem

Utilize um ficheiro de configuração cloud-init para instalar o NGINX e executar uma aplicação de Node.js 'Hello World' numa máquina virtual Linux. 

Na sua concha atual, crie um ficheiro chamado cloud-init.txt. Copie e cole a seguinte configuração na concha. Certifique-se de que copia corretamente todo o ficheiro cloud-init, especialmente a primeira linha:

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

Crie as máquinas virtuais com [az vm criar:](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)

#### <a name="vm1"></a>VM1
* Chamado **myVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Anexado à interface de rede **myNicVM1**.
* Imagem de máquina virtual **UbuntuLTS**.
* O ficheiro de configuração **cloud-init.txt** que criou no passo acima.
* Na **Zona 1.**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Chamado **myVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Anexado à interface de rede **myNicVM2**.
* Imagem de máquina virtual **UbuntuLTS**.
* O ficheiro de configuração **cloud-init.txt** que criou no passo acima.
* Na **Zona 2.**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamado **myVM3.**
* No grupo de recursos **myResourceGroupLB**.
* Anexado à interface de rede **myNicVM3**.
* Imagem de máquina virtual **UbuntuLTS**.
* O ficheiro de configuração **cloud-init.txt** que criou no passo acima.
* Na **Zona 3.**

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Pode levar alguns minutos para os VMs se implantarem.

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. 

Utilizar [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para:

* Crie um endereço IP público redundante de zona padrão chamado **myPublicIP**.
* No **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Para criar um endereço IP público redundante zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
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

Crie um equilibrador de carga pública com [a az network lb create:](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)

* Chamado **myLoadBalancer.**
* Uma piscina frontal chamada **myFrontEnd.**
* Uma piscina de backend chamada **myBackEndPool**.
* Associado ao endereço IP público **myPublicIP** que criou no passo anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. 

Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [sonda LB de rede az criar:](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)

* Monitoriza a saúde das máquinas virtuais.
* Chama-se **MyHealthProbe.**
* Protocolo **TCP**.
* Porta de monitorização **80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
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

Crie uma regra de balançador de carga com [regra az rede lb criar:](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina frontal **myFrontEnd**.
* Envio de tráfego de rede equilibrado de carga para a piscina de endereço de backend **myBackEndPool** usando **a porta 80**. 
* Utilizando a sonda de saúde **myHealthProbe**.
* Protocolo **TCP**.
* Ativar a tradução de endereços de rede de saída (SNAT) utilizando o endereço IP frontend.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicione máquinas virtuais para carregar piscina de backend balancer

Adicione as máquinas virtuais à piscina de backend com [a az network nic ip-config endereço-pool add:](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)

#### <a name="vm1"></a>VM1
* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM1** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM2** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM3** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Criar configuração de regras de saída
As regras de saída do balançador de carga configuram sNAT de saída para VMs na piscina de backend. 

Para obter mais informações sobre as ligações de saída, consulte [as ligações de saída em Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Crie um endereço IP público de saída ou prefixo IP público.

Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um único IP para a conectividade de saída.  

Utilize [o prefixo ip da rede az](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) para criar um prefixo IP público para a conectividade de saída.

Para obter mais informações sobre a escala de ACESSO NAT e conectividade de saída, consulte [o SCALE Outbound NAT com vários endereços IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>IP público

* Denominado **myPublicIPOutbound.**
* No **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Para criar um endereço IP público redundante zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Prefixo de IP público

* Denominado **myPublicIPPrefixOutbound**.
* No **myResourceGroupLB**.
* Comprimento do prefixo de **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Para criar um prefixo IP público redundante zonal na Zona 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Criar configuração IP frontend de saída

Crie uma nova configuração IP frontend com [a criação de frontend-ip da rede Az ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)lb :

Selecione os comandos de prefixo IP públicos ou ip com base na decisão em passo anterior.

#### <a name="public-ip"></a>IP público

* Chama-se **myFrontEndOutbound.**
* No grupo de recursos **myResourceGroupLB**.
* Associado ao endereço IP público **myPublicIPOutbound**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefixo de IP público

* Chama-se **myFrontEndOutbound.**
* No grupo de recursos **myResourceGroupLB**.
* Associado ao prefixo IP público **myPublicIPPrefixOutbound**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Criar piscina de saída

Crie uma nova piscina de saída com [a criação de endereços lb de rede Az:](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)

* Denominado **myBackEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Criar regra de saída

Crie uma nova regra de saída para a piscina de backend de saída com [a az rede lb outbound-rule create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create):

* Chama-se **myOutboundRule.**
* No grupo de recursos **myResourceGroupLB**.
* Associado com o balanceador de carga **myLoadBalancer**
* Associado com frontend **myFrontEndOutbound**.
* Protocolo **Tudo**.
* Intervalo de tempo de **15.**
* **10.000** portos de saída.
* Associado com backend pool **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Adicione máquinas virtuais à piscina de saída

Adicione as máquinas virtuais à piscina de saída com [a az network nic ip-config endereço-pool add:](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)


#### <a name="vm1"></a>VM1
* Na piscina de endereços **backend myEndEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM1** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Na piscina de endereços **backend myEndEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM2** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Na piscina de endereços **backend myEndEndPoolOutbound**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM3** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando [a rede az vnet criar:](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)

* Chamado **myVNet.**
* Prefixo de endereço de **10.1.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.1.0.0/24**.
* No grupo de recursos **myResourceGroupLB.**
* Localização do **eastus.**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, os VMs no endereço backend para os fins de trás são obrigados a ter interfaces de rede que pertencem a um grupo de segurança de rede. 

Criar um grupo de segurança de rede utilizando [a rede az nsg criar:](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)

* Chamado **myNSG.**
* No grupo de recursos **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Criar uma regra de grupo de segurança de rede utilizando [a regra az network nsg criar:](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)

* Denominado **myNSGRuleHTTP**.
* No grupo de segurança de rede que criou no passo anterior, o **myNSG.**
* No grupo de recursos **myResourceGroupLB**.
* Protocolo **(*)**.
* Direção **Entrada**.
* Fonte **(*)**.
* Destino **(*)**.
* Porto de destino **Port 80**.
* **Acesso Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Criar três interfaces de rede com [a az network nic criar:](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)

#### <a name="vm1"></a>VM1

* Chamado **myNicVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Chamado **myNicVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Chamado **myNicVM3.**
* No grupo de recursos **myResourceGroupLB**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, cria-se:

* Um ficheiro de configuração em nuvem nomeado **cloud-init.txt** para a configuração do servidor. 
* Disponibilidade definida para as máquinas virtuais
* Três máquinas virtuais a serem utilizadas como servidores de backend para o equilibrador de carga.


Para verificar se o balançador de carga foi criado com sucesso, instale o NGINX nas máquinas virtuais.

### <a name="create-cloud-init-configuration-file"></a>Criar ficheiro de configuração de inimento de nuvem

Utilize um ficheiro de configuração cloud-init para instalar o NGINX e executar uma aplicação de Node.js 'Hello World' numa máquina virtual Linux. 

Na sua concha atual, crie um ficheiro chamado cloud-init.txt. Copie e cole a seguinte configuração na concha. Certifique-se de que copia corretamente todo o ficheiro cloud-init, especialmente a primeira linha:

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
### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Crie o conjunto de disponibilidade com [az vm disponibilidade-set criar](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* Chamado **myAvSet**.
* No grupo de recursos **myResourceGroupLB**.
* Localização **leste.**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm criar:](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)

#### <a name="vm1"></a>VM1
* Chamado **myVM1**.
* No grupo de recursos **myResourceGroupLB**.
* Anexado à interface de rede **myNicVM1**.
* Imagem de máquina virtual **UbuntuLTS**.
* O ficheiro de configuração **cloud-init.txt** que criou no passo acima.
* Na disponibilidade definir **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>VM2
* Chamado **myVM2**.
* No grupo de recursos **myResourceGroupLB**.
* Anexado à interface de rede **myNicVM2**.
* Imagem de máquina virtual **UbuntuLTS**.
* O ficheiro de configuração **cloud-init.txt** que criou no passo acima.
* Na **Zona 2.**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamado **myVM3.**
* No grupo de recursos **myResourceGroupLB**.
* Anexado à interface de rede **myNicVM3**.
* Imagem de máquina virtual **UbuntuLTS**.
* O ficheiro de configuração **cloud-init.txt** que criou no passo acima.
* Na **Zona 3.**

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Pode levar alguns minutos para os VMs se implantarem.


## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. 

Utilizar [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para:

* Crie um endereço IP público redundante de zona padrão chamado **myPublicIP**.
* No **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
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

Crie um equilibrador de carga pública com [a az network lb create:](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)

* Chamado **myLoadBalancer.**
* Uma piscina frontal chamada **myFrontEnd.**
* Uma piscina de backend chamada **myBackEndPool**.
* Associado ao endereço IP público **myPublicIP** que criou no passo anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. 

Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [sonda LB de rede az criar:](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)

* Monitoriza a saúde das máquinas virtuais.
* Chama-se **MyHealthProbe.**
* Protocolo **TCP**.
* Porta de monitorização **80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
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

Crie uma regra de balançador de carga com [regra az rede lb criar:](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina frontal **myFrontEnd**.
* Envio de tráfego de rede equilibrado de carga para a piscina de endereço de backend **myBackEndPool** usando **a porta 80**. 
* Utilizando a sonda de saúde **myHealthProbe**.
* Protocolo **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicione máquinas virtuais para carregar piscina de backend balancer

Adicione as máquinas virtuais à piscina de backend com [a az network nic ip-config endereço-pool add:](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)


#### <a name="vm1"></a>VM1
* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM1** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM2** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Na piscina de endereços **backend myBackEndPool**.
* No grupo de recursos **myResourceGroupLB**.
* Associado à interface de rede **myNicVM3** e **ipconfig1**.
* Associado ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Para obter o endereço IP público do balanceador de carga, utilize [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Copie o endereço IP público e cole-o na barra de endereço do browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testar o balanceador de carga" border="true":::

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o comando de eliminação do [grupo AZ](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) para remover o grupo de recursos, o equilibrador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido

* Criou um balanceador de carga padrão ou público
* Máquinas virtuais anexadas. 
* Configurado a regra de tráfego do balanceador de carga e a sonda de saúde.
* Testei o equilibrador de carga.

Para saber mais sobre o Azure Load Balancer, continue a.
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)
