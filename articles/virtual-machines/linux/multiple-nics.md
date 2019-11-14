---
title: Criar uma VM do Linux no Azure com várias NICs
description: Saiba como criar uma VM do Linux com várias NICs anexadas a ela usando os modelos CLI do Azure ou Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b4ab46a59bd83bf2d1c08e3a238df3c59797f3e7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035600"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual do Linux no Azure com várias placas de interface de rede


Este artigo fornece detalhes sobre como criar uma VM com várias NICs com a CLI do Azure.

## <a name="create-supporting-resources"></a>Criar recursos de suporte
Instale o [CLI do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Exemplos de nomes de parâmetro incluem *MyResource*, *mystorageaccount*e *myVM*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [AZ Network vnet Create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada *myVnet* e sub-rede denominada *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet). O exemplo a seguir cria uma sub-rede chamada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Crie um grupo de segurança de rede com [AZ Network NSG Create](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar várias NICs
Crie duas NICs com [AZ Network NIC Create](/cli/azure/network/nic). O exemplo a seguir cria duas NICs, chamadas *myNic1* e *myNic2*, conectadas ao grupo de segurança de rede, com uma NIC conectando-se a cada sub-rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e anexar as NICs
Ao criar a VM, especifique as NICs que você criou com `--nics`. Você também precisa tomar cuidado ao selecionar o tamanho da VM. Há limites para o número total de NICs que você pode adicionar a uma VM. Leia mais sobre os [tamanhos de VM do Linux](sizes.md).

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Adicione tabelas de roteamento ao sistema operacional convidado concluindo as etapas em [Configurar o sistema operacional convidado para várias NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adicionar uma NIC a uma VM
As etapas anteriores criaram uma VM com várias NICs. Você também pode adicionar NICs a uma VM existente com o CLI do Azure. Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, portanto, dimensione sua VM adequadamente. Se necessário, você pode [redimensionar uma VM](change-vm-size.md).

Crie outra NIC com [AZ Network NIC Create](/cli/azure/network/nic). O exemplo a seguir cria uma NIC chamada *myNic3* conectada à sub-rede de back-end e ao grupo de segurança de rede criado nas etapas anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar uma NIC a uma VM existente, primeiro Desaloque a VM com [AZ VM DEALLOCATE](/cli/azure/vm). O exemplo a seguir Desaloca a VM chamada *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicione a NIC com [AZ VM NIC Add](/cli/azure/vm/nic). O exemplo a seguir adiciona *myNic3* a *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie a VM com [AZ VM Start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adicione tabelas de roteamento ao sistema operacional convidado concluindo as etapas em [Configurar o sistema operacional convidado para várias NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Remover uma NIC de uma VM
Para remover uma NIC de uma VM existente, primeiro Desaloque a VM com [AZ VM DEALLOCATE](/cli/azure/vm). O exemplo a seguir Desaloca a VM chamada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Remova a NIC com [AZ VM NIC remove](/cli/azure/vm/nic). O exemplo a seguir remove *myNic3* de *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie a VM com [AZ VM Start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar várias NICs usando modelos do Resource Manager
Azure Resource Manager modelos usam arquivos JSON declarativos para definir seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Os modelos do Resource Manager fornecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre como [criar várias instâncias usando *copiar*](../../resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` para, em seguida, acrescentar um número a um nome de recurso, que permite criar `myNic1`, `myNic2`, etc. Veja a seguir um exemplo de acréscimo do valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo de como [criar várias NICs usando modelos do Resource Manager](../../virtual-network/template-samples.md).

Adicione tabelas de roteamento ao sistema operacional convidado concluindo as etapas em [Configurar o sistema operacional convidado para várias NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o sistema operacional convidado para várias NICs

As etapas anteriores criaram uma rede virtual e uma sub-rede, NICs conectadas e, em seguida, criaram uma VM. Um endereço IP público e regras de grupo de segurança de rede que permitem o tráfego SSH não foram criadas. Para configurar o sistema operacional convidado para várias NICs, você precisa permitir conexões remotas e executar comandos localmente na VM.

Para permitir o tráfego SSH, crie uma regra de grupo de segurança de rede com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) da seguinte maneira:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Crie um endereço IP público com [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) e atribua-o à primeira NIC com [AZ Network NIC IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Para exibir o endereço IP público da VM, use [AZ VM show](/cli/azure/vm#az-vm-show) da seguinte maneira:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Agora, SSH para o endereço IP público da sua VM. O nome de usuário padrão fornecido em uma etapa anterior foi *azureuser*. Forneça seu próprio nome de usuário e endereço IP público:

```bash
ssh azureuser@137.117.58.232
```

Para enviar de ou para uma interface de rede secundária, você precisa adicionar manualmente rotas persistentes ao sistema operacional para cada interface de rede secundária. Neste artigo, *eth1* é a interface secundária. As instruções para adicionar rotas persistentes ao sistema operacional variam de acordo com o distribuição. Consulte a documentação do seu distribuição para obter instruções.

Ao adicionar a rota ao sistema operacional, o endereço do gateway é *0,1* para qualquer sub-rede em que a interface de rede esteja. Por exemplo, se a interface de rede for atribuída ao endereço *10.0.2.4*, o gateway que você especificar para a rota será *10.0.2.1*. Você pode definir uma rede específica para o destino da rota ou especificar um destino de *0.0.0.0*, se desejar que todo o tráfego para a interface passe pelo gateway especificado. O gateway para cada sub-rede é gerenciado pela rede virtual.

Depois de adicionar a rota para uma interface secundária, verifique se a rota está em sua tabela de rotas com `route -n`. A saída de exemplo a seguir é para a tabela de rotas que tem as duas interfaces de rede adicionadas à VM neste artigo:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Confirme se a rota que você adicionou persiste entre reinicializações verificando sua tabela de rotas novamente após uma reinicialização. Para testar a conectividade, você pode inserir o comando a seguir, por exemplo, em que *eth1* é o nome de uma interface de rede secundária:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Passos seguintes
Examine os [tamanhos de VM do Linux](sizes.md) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs às quais cada tamanho de VM dá suporte.

Para proteger ainda mais suas VMs, use o acesso just in time à VM. Esse recurso abre as regras do grupo de segurança de rede para o tráfego SSH quando necessário e, por um período de tempo definido. Para obter mais informações, veja [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Gerir o acesso da máquina virtual através do just in time).
