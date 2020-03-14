---
title: 'Quickstart: Criar um portal NAT - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar um gateway NAT usando o Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 9402960927f56092e226ab81bd3e6ede0cf6a52d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79202201"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Quickstart: Criar um portal NAT usando o Azure CLI

Este quickstart mostra-lhe como utilizar o serviço AZURE Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode completar este tutorial utilizando a Azure Cloud Shell ou executar os respetivos comandos localmente.  Se nunca usou a Azure Cloud Shell, [inscreva-se agora](https://shell.azure.com) para passar pela configuração inicial.
Se optar por executar estes comandos localmente, tem de instalar o CLI.  Este tutorial requer que esteja a executar uma versão da versão Azure CLI 2.0.71 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o Portal naT

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à Internet pública, precisa de um ou mais endereços IP públicos para o portal NAT. Use a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIP** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Pode utilizar um ou mais recursos públicos de endereçoip, prefixos IP públicos ou ambos com gateway NAT. Vamos adicionar um recurso público de prefixo IP a este cenário para demonstrar.   Utilize a criação de [prefixo público-ip](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) da rede Az para criar um recurso público de prefixo IP denominado **myPublicIPprefix** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool ip público e um prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Mude o tempo de inatividade do padrão de 4 minutos para 10 minutos.

Crie um portal global Azure NAT com a [rede Az nat gateway criar](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) o nome **myNATgateway**. O comando utiliza tanto o endereço IP público **myPublicIP** como o IP público **prefixo myPublicIPprefix**. O comando muda o tempo de inatividade para **10** minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Neste ponto, o portal NAT está funcional e tudo o que falta é configurar quais as subredes de uma rede virtual que devem usá-lo.

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar um VM e poder usar o seu portal NAT, precisamos de criar a rede virtual.

Crie uma rede virtual chamada **myVnet** com uma subnet chamada **mySubnet** no **myResourceGroupNAT** usando a [z network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0.0/16**. A sub-rede dentro da rede virtual é **de 192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configure o serviço NAT para subnet de origem

Vamos configurar a sub-rede de origem **mySubnet** na rede virtual **myVnet** para usar um recurso de gateway NAT específico **myNATgateway** com [aaz rede vnet atualização .](https://docs.microsoft.com/cli/azure/network/vnet/subnet)  Este comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para destinos da Internet está agora a usar o portal NAT.  Não é necessário configurar um UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Criar um VM para usar o serviço NAT

Vamos agora criar um VM para usar o serviço NAT.  Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM.  O serviço NAT está consciente e substituirá o destino de Internet padrão na sua subnet. O endereço IP público da VM não será usado para ligações de saída.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para fonte VM

Criamos um IP público para ser usado para aceder ao VM.  Use a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPVM** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Criar um NSG para VM

Como os endereços IP públicos padrão são "seguros por padrão", precisamos de criar um NSG que permita o acesso à entrada para acesso ssh. Use a [rede az nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso NSG chamado **myNSG** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor ponto final sSH na fonte VM

Criamos uma regra no NSG para o acesso ssh à fonte vm. Use a regra nsg da [rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **sSH** no NSG nomeado **myNSG** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Criar NIC para VM

Criar uma interface de rede com a [rede az nic criar](/cli/azure/network/nic#az-network-nic-create) e associar-se ao endereço IP público e ao grupo de segurança da rede. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Criar VM

Crie a máquina virtual com [az vm criar.](/cli/azure/vm#az-vm-create)  Geramos chaves ssh para este VM e armazenamos a chave privada para usar mais tarde.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Aguarde que o VM se desloque e continue com o resto dos passos.

## <a name="discover-the-ip-address-of-the-vm"></a>Descubra o endereço IP do VM

Primeiro, temos de descobrir o endereço IP do VM que criaste. Para recuperar o endereço IP público do VM, utilize o [programa público-ip da rede Az.](/cli/azure/network/public-ip#az-network-public-ip-show) 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo para aceder ao VM.

### <a name="sign-in-to-vm"></a>Inscreva-se na VM

As credenciais SSH devem ser armazenadas na sua Cloud Shell da operação anterior.  Abra uma [Nuvem Azure](https://shell.azure.com) Shell no seu navegador. Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual.

```bash
ssh <ip-address-destination>
```

Está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [comando az group eliminar](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no interior.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um portal NAT e um VM para usá-lo. 

Reveja as métricas no Monitor Azure para ver o seu serviço NAT a funcionar. Diagnosticar problemas como a exaustão de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é abordado adicionando recursos de endereços IP públicos adicionais ou recursos públicos de prefixo IP ou ambos.


- Conheça a [Rede Virtual Azure NAT](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT.](./nat-gateway-resource.md)
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Arranque rápido para a implementação do [recurso de gateway NAT utilizando](./quickstart-create-nat-gateway-portal.md)o portal Azure .
> [!div class="nextstepaction"]

