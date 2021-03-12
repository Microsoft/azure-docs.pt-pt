---
title: 'Tutorial: Criar uma porta de entrada NAT - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Começar a criar um portal NAT usando o Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102663395"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Tutorial: Criar uma porta de entrada NAT utilizando o Azure CLI

Este tutorial mostra-lhe como utilizar o serviço Azure Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual
> * Cria uma máquina virtual.
> * Crie um gateway NAT e associe-se à rede virtual.
> * Ligue-se à máquina virtual e verifique o endereço IP DO NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o portal NAT

Nesta secção criamos a porta de entrada DA NAT e recursos de apoio.

### <a name="create-public-ip-address"></a>Criar endereço IP público

Para aceder à Internet, precisa de um ou mais endereços IP públicos para o gateway NAT. Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um recurso de endereço IP público chamado **myPublicIP** no **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Criar recurso de gateway NAT

Crie uma porta de entrada Azure NAT global com [a az network nat gateway criar.](/cli/azure/network/nat#az_network_nat_gateway_create) O resultado deste comando criará um recurso de gateway chamado **myNATgateway** que utiliza o endereço IP público **myPublicIP**. O tempo limite de marcha lenta está definido para 10 minutos.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Criar a rede virtual

Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) in the **myResourceGroup** resource group. O espaço de endereço IP para a rede virtual é **10.1.0.0/16**. A sub-rede dentro da rede virtual é **de 10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Criar hospedeiro de bastião

Crie um anfitrião Azure Bastion chamado **myBastionHost** para aceder à máquina virtual. 

Utilize [a sub-rede de rede az para](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) criar uma sub-rede Azure Bastion.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Crie um endereço IP público para o anfitrião do bastião com [a criação de ip público da rede Az.](/cli/azure/network/public-ip#az_network_public_ip_create) 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az-network-bastion-create) para criar o hospedeiro de bastião. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Configure o serviço NAT para a sub-rede de origem

Configuraremos a sub-rede de origem **mySubnet** na rede virtual **myVnet** para usar uma **myNATgateway** de recurso de gateway NAT específica com [a atualização da sub-rede de rede az.](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) Este comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para destinos de Internet está agora usando o gateway NAT.  Não é necessário configurar uma UDR.


## <a name="virtual-machine"></a>Máquina virtual

Nesta secção, irá criar uma máquina virtual para testar o gateway NAT para verificar o endereço IP público da ligação de saída.

Crie a máquina virtual com [a criação de az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Aguarde que a criação da máquina virtual esteja concluída antes de passar para a secção seguinte.

## <a name="test-nat-gateway"></a>Portal DE TESTE NAT

Nesta secção, vamos testar o portal NAT. Primeiro descobriremos o IP público do portal NAT. Ligaremos-nos então à máquina virtual de teste e verificaremos a ligação de saída através do gateway NAT.
    
1. Inicie sessão no [portal do Azure](https://portal.azure.com)

1. Encontre o endereço IP público para o gateway NAT no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicIP**.

2. Tome nota do endereço IP público:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Descubra o endereço IP público do gateway NAT" border="true":::

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupNAT.**

4. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myTestVM**.

8. **https://whatsmyip.com** Insira na barra de endereços.

9. Verifique se o endereço IP apresentado corresponde ao endereço de gateway NAT indicado no passo anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer mostrando IP externo de saída" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e o gateway NAT com os seguintes passos:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Visão geral do NAT da rede virtual](nat-overview.md)
