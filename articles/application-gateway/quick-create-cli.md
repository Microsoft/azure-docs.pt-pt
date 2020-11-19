---
title: 'Quickstart: Tráfego web direto usando OCli'
titleSuffix: Azure Application Gateway
description: Neste arranque rápido, aprende-se a usar o CLI Azure para criar um Gateway de aplicação Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: d4b6fc296ae41667d271e243e9aca9b594e4a5b6
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886707"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure

Neste arranque rápido, você usa Azure CLI para criar um gateway de aplicação. Em seguida, teste-o para se certificar de que funciona corretamente. 

O gateway de aplicações direciona o tráfego web da aplicação para recursos específicos numa piscina de backend. Você atribui os ouvintes aos portos, cria regras e adiciona recursos a uma piscina de backend. Por uma questão de simplicidade, este artigo usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site no gateway de aplicação, uma regra de encaminhamento de pedido básico, e duas máquinas virtuais na piscina de backend.

Também pode completar este quickstart utilizando [o Azure PowerShell](quick-create-powershell.md) ou o [portal Azure](quick-create-portal.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-resource-group"></a>Criar grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Criar um grupo de recursos utilizando `az group create` . 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede 

Para que o Azure comunique entre os recursos que cria, precisa de uma rede virtual.  A sub-rede de gateway de aplicação pode conter apenas portais de aplicação. Não são permitidos outros recursos.  Pode criar uma nova sub-rede para o Gateway de Aplicação ou utilizar uma existente. Neste exemplo, cria-se duas sub-redes: uma para o gateway de aplicações e outra para os servidores backend. Pode configurar o Frontend IP do Gateway de aplicação para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um endereço IP de Frontend Público.

Para criar a rede virtual e a sub-rede, `az network vnet create` utilize. Corra `az network public-ip create` para criar o endereço IP público.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>Criar os servidores backend

Um backend pode ter NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como O Azure App Service. Neste exemplo, cria-se duas máquinas virtuais para utilizar como servidores de backend para o gateway de aplicações. Também instala iIS nas máquinas virtuais para testar o gateway de aplicação.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Instale o servidor web NGINX nas máquinas virtuais para verificar se o gateway de aplicações foi criado com sucesso. Pode utilizar um ficheiro de configuração cloud-init para instalar o NGINX e executar uma aplicação de Node.js "Hello World" numa máquina virtual Linux. Para obter mais informações sobre cloud-init, consulte [o suporte cloud-init para máquinas virtuais em Azure](../virtual-machines/linux/using-cloud-init.md).

Na sua Azure Cloud Shell, copie e cole a seguinte configuração num ficheiro denominado *cloud-init.txt*. Insira *o editor cloud-init.txt* para criar o ficheiro.

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

Criar as interfaces de rede com `az network nic create` . Para criar as máquinas virtuais, `az vm create` utiliza-se.

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

Crie um gateway de aplicação utilizando `az network application-gateway create` . Quando cria um gateway de aplicação com o CLI Azure, especifica informações de configuração, tais como capacidade, definições SKU e HTTP. O Azure adiciona então os endereços IP privados das interfaces de rede como servidores no pool de backend do gateway de aplicações.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Pode levar até 30 minutos para a Azure criar o gateway de aplicação. Depois de criado, pode ver as seguintes definições na secção **Definições** da página **'Gateway' de aplicação:**

- **appGatewayBackendPool**: Localizado na página **de piscinas backend.** Especifica a piscina de backend necessária.
- **appGatewayBackendHttpSettings**: Localizado na página de **definições HTTP.** Especifica que o gateway de aplicações utiliza a porta 80 e o protocolo HTTP para comunicação.
- **appGatewayHttpListener**: Localizado na **página dos Ouvintes**. Especifica o ouvinte predefinido associado ao **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Localizado na página de **configurações IP frontend.** Atribui *o myAGPublicIPAddress* à **appGatewayHttpListener**.
- **regra1**: Localizado na página **Regras.** Especifica a regra de encaminhamento padrão que está associada ao **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o Azure não necessite de um servidor web NGINX para criar o gateway de aplicações, instalou-o neste quickstart para verificar se o Azure criou com sucesso o gateway da aplicação. Para obter o endereço IP público do novo gateway de aplicações, use `az network public-ip show` . 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copie e cole o endereço IP público na barra de endereço do seu navegador.
    
![Testar o gateway de aplicação](./media/quick-create-cli/application-gateway-nginxtest.png)

Ao refrescar o navegador, deverá ver o nome do segundo VM. Isto indica que o gateway de aplicação foi criado com sucesso e pode ligar-se com o backend.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, utilize o `az group delete` comando para eliminar o grupo de recursos. Quando elimina o grupo de recursos, também elimina o gateway de aplicações e todos os seus recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

