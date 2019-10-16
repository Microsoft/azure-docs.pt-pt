---
title: 'Início rápido: criar um gateway NAT-CLI do Azure'
titlesuffix: Azure NAT service
description: Este guia de início rápido mostra como criar um gateway NAT usando o CLI do Azure
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a NAT Gateway for outbound connectivity for my virtual network.
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: e7b78451ea6bf0a19eb358c8777ce59c07e32fd8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376400"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Início rápido: criar um gateway NAT usando CLI do Azure

Este guia de início rápido mostra como usar o serviço NAT do Azure e criar um gateway NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 

>[!NOTE] 
>O serviço NAT do Azure está disponível como visualização pública no momento e disponível em um conjunto limitado de [regiões](https://azure.microsoft.com/global-infrastructure/regions/). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms) para obter mais informações.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode concluir este tutorial usando o Azure cloud Shell ou executar os respectivos comandos localmente.  Se você nunca usou o Azure cloud Shell, [faça logon agora](https://shell.azure.com) para passar pela configuração inicial.

Se você optar por executar esses comandos localmente, precisará instalar a CLI.  Este tutorial requer que você esteja executando uma versão do CLI do Azure versão 2.0.71 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupNAT* no local *eastus2* :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o gateway NAT

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para acessar a Internet pública, você precisa de um ou mais endereços IP públicos para o gateway NAT. Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIP** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Você pode usar um ou mais recursos de endereço IP público ou um ou mais prefixos IP públicos ou ambos com o gateway NAT. Adicionaremos um recurso de prefixo de IP público a este cenário para demonstrar.   Use [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) para criar um recurso de prefixo de IP público chamado **myPublicIPprefix** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta seção fornece detalhes sobre como você pode criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway NAT:
  - Um pool IP público e o prefixo IP público a ser usado para fluxos de saída convertidos pelo recurso de gateway NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway de NAT global do Azure com [AZ Network NAT gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) chamado **myNATgateway** que usa o endereço IP público **myPublicIP** e o prefixo IP público **myPublicIPprefix** e altere o tempo limite de ociosidade para 10 minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Neste ponto, o gateway de NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-la.

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implantar uma VM e usar seu gateway NAT, precisamos criar a rede virtual.

Crie uma rede virtual com o nome **myVnet**, com uma sub-rede de nome **mySubnet** em **myResourceGroup**, através de [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).  O espaço de endereço IP para a rede virtual é **192.168.0.0/16** e a sub-rede na rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurar o serviço NAT para a sub-rede de origem

Configuraremos a sub-rede de origem **mysubnet** na rede virtual **myVnet** para usar um recurso de gateway NAT específico **myNAT** com [AZ Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Esse comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para os destinos da Internet agora está usando o serviço NAT.  Não é necessário configurar um UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Criar uma VM para usar o serviço NAT

Agora, criaremos uma VM para usar o serviço NAT.  Essa VM tem um IP público para usar como um IP público em nível de instância para permitir que você acesse a VM.  O serviço NAT reconhece a direção do fluxo e substituirá o destino da Internet padrão em sua sub-rede. O endereço IP público da VM não será usado para conexões de saída.

### <a name="create-public-ip-for-source-vm"></a>Criar um IP público para a VM de origem

Criamos um IP público a ser usado para acessar a VM.  Use [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPVM** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Criar um NSG para a VM

Como os endereços IP públicos padrão são "seguros por padrão", precisamos criar um NSG para permitir o acesso de entrada para acesso SSH. Use [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) para criar um recurso do NSG chamado **myNSG** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor o ponto de extremidade SSH na VM de origem

Criamos uma regra no NSG para o acesso SSH à VM de origem. Use [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) para criar uma regra NSG chamada **SSH** no NSG chamado **myNSG** no **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Criar NIC para VM

Crie um adaptador de rede com [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) e associe com o endereço IP público e o grupo de segurança de rede. 

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

Crie a máquina virtual com [AZ VM Create](/cli/azure/vm#az-vm-create).  Geramos chaves SSH para essa VM e armazenamos a chave privada para uso posterior.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys \
```

Aguarde até que a VM conclua a implantação e continue com o restante das etapas.

## <a name="discover-the-ip-address-of-the-vm"></a>Descobrir o endereço IP da VM

Primeiro, precisamos descobrir o endereço IP da VM que você criou. Para recuperar o endereço IP público da VM, use [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para que você possa usá-lo para acessar a VM.

### <a name="sign-in-to-vm"></a>Entrar na VM

As credenciais SSH devem ser armazenadas em seu Cloud Shell a partir da operação anterior.  Abra um [Azure cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para SSH para a máquina virtual.

```bash
ssh <ip-address-destination>
```

Agora você está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o comando [AZ Group Delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos nele.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou um gateway NAT e uma VM para usar o serviço NAT. Para saber mais sobre o serviço NAT do Azure, continue para outros tutoriais do serviço NAT do Azure.

Você também pode examinar as métricas em Azure Monitor para ver o serviço NAT operando e diagnosticar problemas como esgotamento de recursos de portas SNAT disponíveis.  O esgotamento de recursos das portas SNAT é facilmente resolvido com a adição de recursos de endereço IP público adicionais ou recursos de prefixo IP público ou ambos.

> [!div class="nextstepaction"]

