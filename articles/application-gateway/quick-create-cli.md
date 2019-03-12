---
title: Início Rápido - Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure | Microsoft Docs
description: Saiba como utilizar a CLI do Azure para criar um Gateway de Aplicação do Azure que direciona o tráfego da Web para máquinas virtuais num conjunto de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: eb0f73d31abced8decbed31e5604a2056584eb98
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549430"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início rápido: Tráfego da web direto com o Gateway de aplicação do Azure - CLI do Azure

Este guia de introdução mostra-lhe como utilizar o portal do Azure para criar um gateway de aplicação.  Depois de criar o gateway de aplicação, teste-lo para se certificar de que está a funcionar corretamente. Com o Gateway de aplicação do Azure, direcionar o tráfego da web de aplicação a recursos específicos, a atribuição de serviços de escuta a portas, criação de regras e adicionar recursos a um agrupamento de back-end. Para simplificar, este artigo utiliza uma configuração simples com um IP público de front-end, um serviço de escuta básico para alojar um site único neste gateway de aplicação, duas máquinas virtuais utilizadas para o conjunto de back-end e uma regra de encaminhamento de pedido básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se optar por instalar e utilizar a CLI localmente, execute a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute **az - versão**. Para obter informações sobre a instalação ou atualização, consulte [instalar a CLI do Azure]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Grupo de recursos

No Azure, alocar recursos relacionados a um grupo de recursos. Criar um grupo de recursos com [criar grupo az](/cli/azure/group#az-group-create). 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Recursos de rede necessários 

Para o Azure comunicar entre os recursos que criar, ele precisa de uma rede virtual.  A sub-rede do gateway de aplicação pode conter apenas os gateways de aplicação. Não existem outros recursos são permitidos.  Pode criar uma nova sub-rede de Gateway de aplicação ou utilize um já existente. Neste exemplo, vai criar duas sub-redes, neste exemplo: um para o gateway de aplicação e outro para os servidores de back-end. Pode configurar o IP de front-end do Gateway de aplicação para ser públicas ou privadas, de acordo com o seu caso de utilização. Neste exemplo, vamos escolher um IP público de front-end.

Para criar a rede virtual e sub-rede, utilize [vnet de rede de az criar](/cli/azure/network/vnet#az-network-vnet-create). Execute [criar a rede de az public-ip](/cli/azure/network/public-ip) para criar o endereço IP público.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

### <a name="backend-servers"></a>Servidores de back-end

Back-end pode ser composto de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, nomes de IPs interno, de domínio completamente qualificado (FQDN) e back-ends de multi-inquilino, como o serviço de aplicações do Azure. Neste exemplo, vai criar duas máquinas virtuais do Azure utilizar como servidores de back-end para o gateway de aplicação. Também é instalar o IIS nas máquinas virtuais para verificar se o Azure criada com êxito o gateway de aplicação.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Instalar o [servidor web NGINX](https://docs.nginx.com/nginx/) nas máquinas virtuais para verificar a aplicação gateway foi criado com êxito. Pode utilizar um ficheiro de configuração cloud-init para instalar o NGINX e executar uma aplicação de node. js de "Hello World" numa máquina virtual Linux. Para obter mais informações sobre o cloud-init, veja [suporte de Cloud-init para máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

No seu Azure Cloud Shell, copie e cole a seguinte configuração para um ficheiro denominado *cloud-Init*. Introduza *editor cloud-Init* para criar o ficheiro.

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

Crie as interfaces de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create). Para criar as máquinas virtuais, utilize [az vm criar](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Criar um gateway de aplicação utilizando [criar gateway de aplicação do rede az](/cli/azure/network/application-gateway). Quando cria um gateway de aplicação com a CLI do Azure, especifique as informações de configuração, como a capacidade, SKU e HTTP definições. Em seguida, o Azure adiciona os endereços IP privados das interfaces de rede como servidores no agrupamento de back-end do gateway de aplicação.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Pode demorar até 30 minutos para o Azure criar o gateway de aplicação. Depois de criado, pode ver as seguintes definições na **definições** secção a **gateway de aplicação** página:

- **appGatewayBackendPool**: Localizado no **conjuntos de back-end** página. Especifica o conjunto de back-end necessários.
- **appGatewayBackendHttpSettings**: Localizado no **definições de HTTP** página. Especifica que o gateway de aplicação utiliza a porta 80 e o protocolo HTTP para a comunicação.
- **appGatewayHttpListener**: Localizado no **página de serviços de escuta**. Especifica o serviço de escuta do padrão associado **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Localizado no **configurações de IP de front-end** página. Atribui *myAGPublicIPAddress* ao **appGatewayHttpListener**.
- **rule1**: Localizado no **regras** página. Especifica a regra de encaminhamento predefinido que é associada **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o Azure não requer um servidor de web NGINX para criar o gateway de aplicação, a instalou neste início rápido para verificar se o Azure criada com êxito o gateway de aplicação. Para obter o endereço IP público do gateway de aplicação nova, utilize [show de public-ip de rede de az](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copie e cole o endereço IP público na barra de endereço do seu browser.
    
![Testar o gateway de aplicação](./media/quick-create-cli/application-gateway-nginxtest.png)

Quando atualizar o navegador, deverá ver o nome da segunda VM. Uma resposta válida verifica se o gateway de aplicação foi criado com êxito e é capaz de ligar com êxito com o back-end.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, utilize o [eliminação do grupo de az](/cli/azure/group#az-group-delete) comando para remover o grupo de recursos. Ao remover o grupo de recursos, também remover o gateway de aplicação e todos os respetivos recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

