---
title: Início Rápido - Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure | Microsoft Docs
description: Saiba como usar o CLI do Azure para criar um gateway de Aplicativo Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd68f4a565c28f1dbac7e94442a8f8231af01328
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314901"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início rápido: Tráfego direto da Web com Aplicativo Azure gateway-CLI do Azure

Este guia de início rápido mostra como usar CLI do Azure para criar um gateway de aplicativo.  Depois de criar o gateway de aplicativo, você o testará para verificar se ele está funcionando corretamente. Com o gateway de Aplicativo Azure, você direciona o tráfego da Web do aplicativo para recursos específicos atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end. Este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site no gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitação básica.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-cli"></a>CLI do Azure

Se você optar por instalar e usar a CLI localmente, execute CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute **AZ--Version**. Para obter informações sobre como instalar ou atualizar, consulte [instalar CLI do Azure]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Resource group

No Azure, você aloca recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando [AZ Group Create](/cli/azure/group#az-group-create). 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Recursos de rede necessários 

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual.  A sub-rede do gateway de aplicativo pode conter somente gateways de aplicativo. Nenhum outro recurso é permitido.  Você pode criar uma nova sub-rede para o gateway de aplicativo ou usar uma existente. Neste exemplo, você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end. Você pode configurar o IP de front-end do gateway de aplicativo para ser público ou privado de acordo com seu caso de uso. Neste exemplo, você escolherá um IP de front-end público.

Para criar a rede virtual e a sub-rede, use [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). Execute [AZ Network Public-IP Create](/cli/azure/network/public-ip) para criar o endereço IP público.

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

### <a name="backend-servers"></a>Servidores de back-end

Um back-end pode ter NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, você cria duas máquinas virtuais para usar como servidores de back-end para o gateway de aplicativo. Você também instala o IIS nas máquinas virtuais para testar o gateway de aplicativo.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Instale o [servidor Web Nginx](https://docs.nginx.com/nginx/) nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito. Você pode usar um arquivo de configuração Cloud-init para instalar o NGINX e executar um aplicativo node. js "Olá, Mundo" em uma máquina virtual Linux. Para obter mais informações sobre a inicialização de nuvem, consulte [suporte de Cloud-init para máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

Em seu Azure Cloud Shell, copie e cole a configuração a seguir em um arquivo chamado *Cloud-init. txt*. Insira o *Editor Cloud-init. txt* para criar o arquivo.

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

Crie as interfaces de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create). Para criar as máquinas virtuais, use [AZ VM Create](/cli/azure/vm#az-vm-create).

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

Crie um gateway de aplicativo usando [AZ Network Application-Gateway Create](/cli/azure/network/application-gateway). Ao criar um gateway de aplicativo com o CLI do Azure, você especifica informações de configuração, como capacidade, SKU e configurações de HTTP. Em seguida, o Azure adiciona os endereços IP privados das interfaces de rede como servidores no pool de back-end do gateway de aplicativo.

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

Pode levar até 30 minutos para que o Azure crie o gateway de aplicativo. Depois de criado, você pode exibir as seguintes configurações na seção **configurações** da página do **Gateway de aplicativo** :

- **appGatewayBackendPool**: Localizado na página **pools de back-end** . Ele especifica o pool de back-end necessário.
- **appGatewayBackendHttpSettings**: Localizado na página **configurações de http** . Ele especifica que o gateway de aplicativo usa a porta 80 e o protocolo HTTP para comunicação.
- **appGatewayHttpListener**: Localizado na **página ouvintes**. Ele especifica o ouvinte padrão associado ao **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Localizado na página **configurações de IP de front-end** . Ele atribui *myAGPublicIPAddress* ao **appGatewayHttpListener**.
- **rule1**: Localizado na página **regras** . Ele especifica a regra de roteamento padrão associada a **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o Azure não exija um servidor Web NGINX para criar o gateway de aplicativo, você o instalou neste guia de início rápido para verificar se o Azure criou com êxito o gateway de aplicativo. Para obter o endereço IP público do novo gateway de aplicativo, use [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copie e cole o endereço IP público na barra de endereços do seu navegador.
    
![Testar o gateway de aplicação](./media/quick-create-cli/application-gateway-nginxtest.png)

Ao atualizar o navegador, você deverá ver o nome da segunda VM. Isso indica que o gateway de aplicativo foi criado com êxito e pode se conectar com o back-end.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos criados com o gateway de aplicativo, use o comando [AZ Group Delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos. Removendo o grupo de recursos, você também remove o gateway de aplicativo e todos os seus recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

