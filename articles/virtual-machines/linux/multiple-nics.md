---
title: Crie um Linux VM em Azure com vários NICs
description: Aprenda a criar um VM Linux com vários NICs ligados a ele usando os modelos Azure CLI ou Resource Manager.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: ecbff4beadd9d10a8489c89cc322c0bb67ec5f40
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79267186"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual Linux em Azure com vários cartões de interface de rede


Este artigo detalha como criar um VM com vários NICs com o Azure CLI.

## <a name="create-supporting-resources"></a>Criar recursos de apoio
Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e inicie sessão numa conta Azure utilizando [login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *mystorageaccount,* e *myVM*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Criar a rede virtual com a [rede az vnet criar](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual chamada *myVnet* e subnet chamada *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com a [rede az vnet criar](/cli/azure/network/vnet/subnet). O exemplo seguinte cria uma subnet chamada *mySubnetBackEnd:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Criar um grupo de segurança de rede com [a rede az nsg criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar vários NICs
Crie dois NICs com a [z network nic criar](/cli/azure/network/nic). O exemplo seguinte cria dois NICs, denominados *myNic1* e *myNic2,* ligados ao grupo de segurança da rede, com um NIC ligado a cada subnet:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Crie um VM e prenda os NICs
Quando criar o VM, especifique os NICs que criou com `--nics`. Também precisa de ter cuidado quando selecionar o tamanho VM. Existem limites para o número total de NICs que pode adicionar a um VM. Leia mais sobre [tamanhos de VM Linux](sizes.md).

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

Adicione as mesas de encaminhamento ao oss o hóspede, completando os passos em [Configurar o os so do hóspede para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adicione um NIC a um VM
Os passos anteriores criaram um VM com vários NICs. Também pode adicionar NICs a um VM existente com o ClI Azure. Diferentes tamanhos de [VM](sizes.md) suportam um número variado de NICs, de modo que o tamanho do seu VM em conformidade. Se necessário, pode [redimensionar um VM](change-vm-size.md).

Crie outro NIC com a [criação de nic de rede az.](/cli/azure/network/nic) O exemplo seguinte cria um NIC chamado *myNic3* ligado à subnet back-end e ao grupo de segurança da rede criado nos passos anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar um NIC a um VM existente, primeiro desalocar o VM com [az vm desalocado](/cli/azure/vm). O exemplo seguinte desafeta o VM denominado *myVM:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicione o NIC com [az vm nic add](/cli/azure/vm/nic). O exemplo seguinte adiciona *myNic3* ao *myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie o VM com [início az vm:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adicione as mesas de encaminhamento ao oss o hóspede, completando os passos em [Configurar o os so do hóspede para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Remova um NIC de um VM
Para remover um NIC de um VM existente, primeiro desalocar o VM com [az vm desalocado](/cli/azure/vm). O exemplo seguinte desafeta o VM denominado *myVM:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Retire o NIC com [az vm nic remove .](/cli/azure/vm/nic) O exemplo que se segue remove *o myNic3* do *myVM:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie o VM com [início az vm:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar vários NICs usando modelos de Gestor de Recursos
Os modelos do Gestor de Recursos Azure utilizam ficheiros JSON declarativos para definir o seu ambiente. Pode ler uma [visão geral do Gestor de Recursos Azure.](../../azure-resource-manager/management/overview.md) Os modelos do Gestor de Recursos fornecem uma forma de criar múltiplas instâncias de um recurso durante a implementação, tais como a criação de vários NICs. Utiliza *cópia* para especificar o número de instâncias para criar:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre [a criação de várias instâncias usando *cópia*](../../resource-group-create-multiple.md). 

Também pode utilizar uma `copyIndex()` para anexar um número a um nome de recurso, o que lhe permite criar `myNic1`, `myNic2`, etc. O seguinte mostra um exemplo de apreço ao valor do índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de criação de [múltiplos NICs usando modelos de Gestor de Recursos](../../virtual-network/template-samples.md).

Adicione as mesas de encaminhamento ao oss o hóspede, completando os passos em [Configurar o os so do hóspede para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configure o os convidado para vários NICs

Os passos anteriores criaram uma rede virtual e uma sub-rede, niCs anexados, e depois criaram um VM. Não foi criado um endereço IP público e regras do grupo de segurança da rede que permitem o tráfego de SSH. Para configurar o oss odeao do hóspede para vários NICs, você precisa permitir ligações remotas e executar comandos localmente no VM.

Para permitir o tráfego de SSH, crie uma regra de grupo de segurança de rede com [a regra nsg da rede Az criar](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) o seguinte:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Crie um endereço IP público com [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) e atribuí-lo ao primeiro NIC com a [az rede nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Para ver o endereço IP público do VM, utilize [az vm show](/cli/azure/vm#az-vm-show) da seguinte forma:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Agora SSH para o endereço IP público do seu VM. O nome de utilizador predefinido fornecido num passo anterior foi *azureuser*. Forneça o seu próprio nome de utilizador e endereço IP público:

```bash
ssh azureuser@137.117.58.232
```

Para enviar de ou para uma interface de rede secundária, tem de adicionar manualmente rotas persistentes ao sistema operativo para cada interface de rede secundária. Neste artigo, o *eth1* é a interface secundária. As instruções para adicionar rotas persistentes ao sistema operativo variam por distro. Consulte a documentação para obter instruções para a sua disção.

Ao adicionar a rota ao sistema operativo, o endereço gateway é *.1* para qualquer sub-rede em que a interface de rede esteja. Por exemplo, se a interface de rede for atribuída ao endereço *10.0.2.4,* o portal que especifica para a rota é *10.0.2.1*. Pode definir uma rede específica para o destino da rota, ou especificar um destino de *0.0.0.0,* se quiser que todo o tráfego para a interface passe pelo gateway especificado. A porta de entrada para cada subnet é gerida pela rede virtual.

Depois de ter adicionado a rota para uma interface secundária, verifique se a rota está na sua tabela de rotas com `route -n`. A saída de exemplo que se segue é para a tabela de rotas que tem as duas interfaces de rede adicionadas ao VM neste artigo:

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

Confirme que a rota que adicionou persiste em reboots, verificando novamente a sua tabela de rotas após um reboot. Para testar a conectividade, pode introduzir o seguinte comando, por exemplo, onde o *eth1* é o nome de uma interface de rede secundária:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Passos seguintes
Reveja os [tamanhos de VM Linux](sizes.md) ao tentar criar um VM com vários NICs. Preste atenção ao número máximo de NICs que cada tamanho VM suporta.

Para proteger ainda mais os seus VMs, utilize apenas a tempo o acesso vm. Esta funcionalidade abre as regras do grupo de segurança da rede para o tráfego de SSH quando necessário, e por um período de tempo definido. Para obter mais informações, veja [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Gerir o acesso da máquina virtual através do just in time).
