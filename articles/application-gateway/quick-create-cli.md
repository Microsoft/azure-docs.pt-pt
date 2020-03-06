---
title: 'Quickstart: Tráfego web direto utilizando CLI'
titleSuffix: Azure Application Gateway
description: Aprenda a usar o Azure CLI para criar um Portal de Aplicações Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5512e44ab52a3c3d957bbc0d0a07a7a1e7b6f50e
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399572"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure

Neste arranque rápido, utiliza o Azure CLI para criar um portal de aplicação. Depois testa-o para se certificar de que funciona corretamente. 

O gateway da aplicação direciona o tráfego web da aplicação para recursos específicos em um pool backend. Você atribui os ouvintes aos portos, cria regras e adiciona recursos a uma piscina de backend. Por uma questão de simplicidade, este artigo usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site na porta de aplicação, uma regra básica de encaminhamento de pedidos, e duas máquinas virtuais na piscina de backend.

Também pode completar este quickstart utilizando o [Azure PowerShell](quick-create-powershell.md) ou o [portal Azure](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Versão Azure CLI 2.0.4 ou posterior](/cli/azure/install-azure-cli) (se executar o Azure CLI localmente).

## <a name="create-resource-group"></a>Criar grupo de recursos

Em Azure, você aloca recursos relacionados a um grupo de recursos. Crie um grupo de recursos utilizando `az group create`. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede 

Para que o Azure se comunique entre os recursos que cria, precisa de uma rede virtual.  A sub-rede de gateway de aplicação só pode conter gateways de aplicação. Não são permitidos outros recursos.  Pode criar uma nova sub-rede para o Application Gateway ou utilizar uma existente. Neste exemplo, cria-se duas subredes: uma para o portal da aplicação e outra para os servidores de backend. Pode configurar o IP Frontend do Gateway de Aplicação para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um endereço IP Frontend Público.

Para criar a rede virtual e a sub-rede, utilize `az network vnet create`. Executar `az network public-ip create` para criar o endereço IP público.

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

## <a name="create-the-backend-servers"></a>Criar os servidores de backend

Um backend pode ter NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como o Azure App Service. Neste exemplo, cria-se duas máquinas virtuais para utilizar como servidores de backend para o gateway da aplicação. Também instala o IIS nas máquinas virtuais para testar o gateway da aplicação.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Instale o servidor web NGINX nas máquinas virtuais para verificar se o gateway da aplicação foi criado com sucesso. Pode utilizar um ficheiro de configuração de cloud-init para instalar o NGINX e executar uma aplicação "Hello World" Node.js numa máquina virtual Linux. Para obter mais informações sobre cloud-init, consulte [o suporte cloud-init para máquinas virtuais em Azure](../virtual-machines/linux/using-cloud-init.md).

Na sua Concha de Nuvem Azure, copie e cole a seguinte configuração num ficheiro chamado *cloud-init.txt*. Insira *o editor cloud-init.txt* para criar o ficheiro.

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

Crie as interfaces de rede com `az network nic create`. Para criar as máquinas virtuais, utiliza-se `az vm create`.

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

Criar um portal de aplicação utilizando `az network application-gateway create`. Quando cria um portal de aplicação com o Azure CLI, especifica informações de configuração, tais como capacidade, definições de SKU e HTTP. O Azure adiciona então os endereços IP privados das interfaces de rede como servidores no pool de backend do gateway da aplicação.

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

Pode levar até 30 minutos para o Azure criar o portal de aplicação. Depois de criado, pode visualizar as seguintes definições na secção **Definições** da página gateway da **Aplicação:**

- **appGatewayBackendPool**: Localizado na página de **piscinas Backend.** Especifica a reserva necessária.
- **appGatewayBackendHttpSettings**: Localizado na página de **definições http.** Especifica que o gateway de aplicação utiliza a porta 80 e o protocolo HTTP para comunicação.
- **appGatewayHttpListener**: Localizado na **página dos Ouvintes**. Especifica o ouvinte predefinido associado à **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Localizado na página de **configurações IP Frontend.** Atribui *o myAGPublicIPAddress* ao **appGatewayHttpListener**.
- **regra1**: Localizado na página **Regras.** Especifica a regra de encaminhamento padrão que está associada com a **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o Azure não exija um servidor web NGINX para criar o portal de aplicações, instalou-o neste quickstart para verificar se o Azure criou com sucesso o portal da aplicação. Para obter o endereço IP público da nova porta de entrada de aplicações, utilize `az network public-ip show`. 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copie e cole o endereço IP público na barra de endereços do seu navegador.
    
![Testar o gateway de aplicação](./media/quick-create-cli/application-gateway-nginxtest.png)

Ao atualizar o navegador, deverá ver o nome do segundo VM. Isto indica que o gateway da aplicação foi criado com sucesso e pode ligar-se com o backend.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o portal de aplicação, utilize o comando `az group delete` para eliminar o grupo de recursos. Ao eliminar o grupo de recursos, também elimina o gateway da aplicação e todos os seus recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

