---
title: Criar uma porta de entrada NAT - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar um gateway NAT usando o Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: a44a3af7be214aa2ed000eb824b63c0bf7a95aee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88054030"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Criar um portal NAT usando O Azure CLI

Este tutorial mostra-lhe como utilizar o serviço Azure Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode completar este tutorial usando a Azure Cloud Shell ou executar os respetivos comandos localmente.  Se nunca usou a Azure Cloud Shell, [inscreva-se agora](https://shell.azure.com) para analisar a configuração inicial.
Se optar por executar estes comandos localmente, tem de instalar o CLI.  Este tutorial requer que esteja a executar uma versão da versão 2.0.71 do Azure CLI ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o NAT Gateway

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à Internet pública, precisa de um ou mais endereços IP públicos para o gateway NAT. Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIP** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Você pode usar um ou mais recursos de endereço IP público, prefixos IP públicos, ou ambos com gateway NAT. Vamos adicionar um recurso de prefixo IP público a este cenário para demonstrar.   Utilize [o prefixo ip da rede az](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) para criar um recurso de prefixo IP público chamado **myPublicIPprefix** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool IP público e prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Altere o tempo de inatividade do padrão de 4 minutos para 10 minutos.

Crie uma porta de entrada Azure NAT global com [a az network nat gateway criar](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) chamado **myNATgateway**. O comando utiliza o endereço IP público **myPublicIP** e o prefixo ip público **myPpo.** O comando muda o tempo de inatividade para **10** minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Neste momento, o gateway NAT está funcional e tudo o que falta é configurar quais as sub-redes de uma rede virtual que devem usá-lo.

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar um VM e poder usar o seu gateway NAT, precisamos de criar a rede virtual.

Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** no **myResourceGroupNAT** usando [a rede az vnet create](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configure o serviço NAT para a sub-rede de origem

Configuraremos a sub-rede de origem **mySubnet** na rede virtual **myVnet** para usar uma **myNATgateway** de recurso de gateway NAT específica com [a atualização da sub-rede de rede az.](https://docs.microsoft.com/cli/azure/network/vnet/subnet)  Este comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para destinos de Internet está agora usando o gateway NAT.  Não é necessário configurar uma UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Criar um VM para utilizar o serviço NAT

Vamos agora criar um VM para usar o serviço NAT.  Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM.  O serviço NAT está consciente da direção do fluxo e substituirá o destino de Internet predefinido na sua sub-rede. O endereço IP público do VM não será utilizado para ligações de saída.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para origem VM

Criamos um IP público para ser usado para aceder ao VM.  Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPVM** no **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Criar um NSG para VM

Como os endereços IP públicos standard são "seguros por padrão", precisamos de criar um NSG para permitir o acesso à entrada para acesso à SSH. Utilize [a rede az nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso NSG chamado **myNSG** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor o ponto final do SSH na fonte VM

Criamos uma regra no NSG para acesso SSH à fonte vm. Utilize [a regra nsg de rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh** no NSG nomeado **myNSG** no **myResourceGroupNAT**.

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

Crie uma interface de rede com [o nic de rede Az criar](/cli/azure/network/nic#az-network-nic-create) e associar-se ao endereço IP público e ao grupo de segurança da rede. 

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

Crie a máquina virtual com [a criação de az vm](/cli/azure/vm#az-vm-create).  Geramos chaves ssh para este VM e armazenamos a chave privada para usar mais tarde.

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

Primeiro temos de descobrir o endereço IP do VM que criaste. Para recuperar o endereço IP público do VM, utilize [o programa público-ip da rede Az](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo para aceder ao VM.

### <a name="sign-in-to-vm"></a>Inscreva-se em VM

As credenciais SSH devem ser armazenadas na sua Cloud Shell da operação anterior.  Abra uma [Azure Cloud Shell](https://shell.azure.com) no seu navegador. Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual.

```bash
ssh <ip-address-destination>
```

Está agora pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma porta DE NAT e um VM para usá-lo. 

Reveja as métricas no Azure Monitor para ver o seu serviço NAT a funcionar. Diagnosticar problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é abordado adicionando recursos adicionais de endereços IP públicos ou recursos prefixos IP públicos ou ambos.


- Saiba mais sobre [a Rede Virtual Azure NAT](./nat-overview.md)
- Saiba mais sobre [o recurso nat gateway.](./nat-gateway-resource.md)
- Quickstart para implantar [recurso de gateway NAT utilizando O Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart para implantar [recurso de gateway NAT utilizando Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart para implantar [recurso de gateway NAT utilizando o portal Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

