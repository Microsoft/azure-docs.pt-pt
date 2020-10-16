---
title: Criar um Linux VM em Azure com vários NICs
description: Saiba como criar um Linux VM com vários NICs ligados a ele utilizando os modelos Azure CLI ou Resource Manager.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: 86910ece57d8fb72ade0c67a9e6787023c4283f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836926"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual Linux em Azure com vários cartões de interface de rede


Este artigo detalha como criar um VM com vários NICs com o Azure CLI.

## <a name="create-supporting-resources"></a>Criar recursos de apoio
Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e faça login numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *mystorageaccount*e *myVM*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [a criação de vnet de rede Az.](/cli/azure/network/vnet) O exemplo a seguir cria uma rede virtual chamada *myVnet* e sub-rede chamada *mySubnetFrontEnd:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Crie uma sub-rede para o tráfego back-end com [a sub-rede de rede Az.](/cli/azure/network/vnet/subnet) O exemplo a seguir cria uma sub-rede chamada *mySubnetBackEnd:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Criar um grupo de segurança de rede com [a criação de nsg de rede Az](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar vários NICs
Crie dois NICs com [a az network nic create](/cli/azure/network/nic). O exemplo a seguir cria dois NICs, chamados *myNic1* e *myNic2,* ligados ao grupo de segurança da rede, com um NIC ligado a cada sub-rede:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Crie um VM e anexe os NICs
Quando criar o VM, especifique os NICs com os quais criou `--nics` . Também tem de ter cuidado ao selecionar o tamanho VM. Existem limites para o número total de NICs que pode adicionar a um VM. Leia mais sobre [os tamanhos linux VM](../sizes.md).

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria um VM chamado *myVM:*

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

Adicione mesas de encaminhamento ao so convidado, completando os passos em [Configurar o SO convidado para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adicione um NIC a um VM
Os passos anteriores criaram um VM com vários NICs. Também pode adicionar NICs a um VM existente com o Azure CLI. Diferentes [tamanhos VM](../sizes.md) suportam um número variado de NICs, por isso dimensione o seu VM em conformidade. Se necessário, pode [redimensionar um VM](change-vm-size.md).

Crie outro NIC com [a criação de nic de rede az.](/cli/azure/network/nic) O exemplo a seguir cria um NIC chamado *myNic3* ligado à sub-rede de back-end e ao grupo de segurança da rede criado nos passos anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar um NIC a um VM existente, primeiro translocar o VM com [az vm deallocate](/cli/azure/vm). O exemplo a seguir é o VM denominado *myVM:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicione o NIC com [az vm nic add](/cli/azure/vm/nic). O exemplo a seguir adiciona *myNic3* ao *myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie o VM com [início az vm](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adicione mesas de encaminhamento ao so convidado, completando os passos em [Configurar o SO convidado para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Remova um NIC de um VM
Para remover um NIC de um VM existente, primeiro translocar o VM com [az vm deallocate](/cli/azure/vm). O exemplo a seguir é o VM denominado *myVM:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Remova o NIC com [az vm nic remover](/cli/azure/vm/nic). O exemplo a seguir remove *o myNic3* do *myVM:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie o VM com [início az vm](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar vários NICs usando modelos de Gestor de Recursos
Os modelos do Gestor de Recursos Azure utilizam ficheiros JSON declarativos para definir o seu ambiente. Pode ler uma [visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md). Os modelos de Gestor de Recursos fornecem uma forma de criar múltiplas instâncias de um recurso durante a implementação, tais como a criação de múltiplos NICs. Utiliza *cópia* para especificar o número de casos para criar:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre [a criação de múltiplas instâncias utilizando *a cópia.*](../../azure-resource-manager/templates/copy-resources.md) 

Também pode utilizar um `copyIndex()` para anexar um número a um nome de recurso, que lhe permite `myNic1` `myNic2` criar, etc. O seguinte mostra um exemplo de anexação do valor do índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de criação de [vários NICs utilizando modelos de Gestor de Recursos.](../../virtual-network/template-samples.md)

Adicione mesas de encaminhamento ao so convidado, completando os passos em [Configurar o SO convidado para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o SO convidado para vários NICs

Os passos anteriores criaram uma rede virtual e sub-rede, anexados NICs, e depois criaram um VM. Não foi criado um endereço IP público e regras de grupo de segurança de rede que permitem o tráfego de SSH. Para configurar o SO convidado para vários NICs, é necessário permitir ligações remotas e executar comandos localmente no VM.

Para permitir o tráfego SSH, crie uma regra de grupo de segurança de rede com [a regra nsg de rede az criar](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) da seguinte forma:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Crie um endereço IP público com [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) e atribuí-lo ao primeiro NIC com a [atualização ip-config da rede Az:](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update)

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Para visualizar o endereço IP público do VM, use [o show az vm](/cli/azure/vm#az-vm-show) da seguinte forma::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Agora SSH para o endereço IP público do seu VM. O nome de utilizador predefinido fornecido num passo anterior foi *azureuser*. Forneça o seu próprio nome de utilizador e endereço IP público:

```bash
ssh azureuser@137.117.58.232
```

Para enviar para ou a partir de uma interface de rede secundária, tem de adicionar manualmente rotas persistentes ao sistema operativo para cada interface de rede secundária. Neste artigo, *a eth1* é a interface secundária. As instruções para a adição de rotas persistentes ao sistema operativo variam por distro. Consulte a documentação para o seu distro para obter instruções.

Ao adicionar a rota para o sistema operativo, o endereço gateway é *.1* para qualquer sub-rede em que a interface de rede se encontra. Por exemplo, se a interface de rede for atribuída ao endereço *10.0.2.4,* o portal que especifica para a rota é *10.0.2.1*. Pode definir uma rede específica para o destino da rota, ou especificar um destino de *0.0.0.0*, se quiser que todo o tráfego para a interface passe pelo gateway especificado. O portal para cada sub-rede é gerido pela rede virtual.

Depois de adicionar a rota para uma interface secundária, verifique se a rota está na sua tabela de rotas com `route -n` . O exemplo seguinte é para a tabela de rotas que tem as duas interfaces de rede adicionadas ao VM neste artigo:

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

Confirme que a rota que adicionou persiste através de reboots, verificando novamente a sua tabela de rotas após um reboot. Para testar a conectividade, pode introduzir o seguinte comando, por exemplo, onde *a eth1* é o nome de uma interface de rede secundária:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Passos seguintes
Reveja [os tamanhos linux VM](../sizes.md) ao tentar criar um VM com vários NICs. Preste atenção ao número máximo de NICs que cada tamanho VM suporta.

Para proteger ainda mais os seus VMs, utilize o acesso vm mesmo a tempo. Esta funcionalidade abre as regras do grupo de segurança de rede para o tráfego de SSH quando necessário, e por um período de tempo definido. Para obter mais informações, veja [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Gerir o acesso da máquina virtual através do just in time).