---
title: Criar uma VM do Linux no Azure com várias NICs | Documentos da Microsoft
description: Saiba como criar uma VM do Linux com vários NICs ligados ao mesmo com os modelos da CLI do Azure ou do Resource Manager.
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
ms.openlocfilehash: 04aaa1da304657ac3cc305b8939ac4fcce126145
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671170"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual Linux no Azure com rede várias placas de interface


Este artigo fornece detalhes sobre como criar uma VM com vários NICs com a CLI do Azure.

## <a name="create-supporting-resources"></a>Criar recursos de suporte
Instalar a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) e para iniciar sessão no Azure através da conta [início de sessão az](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *mystorageaccount*, e *myVM*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Criar a rede virtual com [vnet de rede de az criar](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual denominada *myVnet* e a sub-rede com o nome *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com [criar a sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet). O exemplo seguinte cria uma sub-rede denominada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Crie um grupo de segurança de rede com [nsg de rede de az criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar vários NICs
Criar dois NICs com [nic da rede de az criar](/cli/azure/network/nic). O exemplo seguinte cria dois NICs, com o nome *myNic1* e *myNic2*, ligado o grupo de segurança de rede, com uma NIC para ligar a cada sub-rede:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e ligar as NICs
Ao criar a VM, especifique os NICs que criou com `--nics`. Terá também de ter cuidado ao selecionar o tamanho da VM. Existem limites para o número total de NICs que podem ser adicionados a uma VM. Leia mais sobre [tamanhos de VM do Linux](sizes.md).

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

Adicionar tabelas de roteamento para o SO convidado, concluindo os passos em [configurar o sistema operacional convidado para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adicionar uma NIC a uma VM
Os passos anteriores criaram uma VM com várias NICs. Também pode adicionar NICs a uma VM existente com a CLI do Azure. Diferentes [tamanhos de VM](sizes.md) suporta um número variável de NICs, então, da mesma forma a dimensionar sua VM. Se for necessário, pode [redimensionar uma VM](change-vm-size.md).

Crie outro NIC com [nic da rede de az criar](/cli/azure/network/nic). O exemplo seguinte cria um NIC com o nome *myNic3* ligada ao back-end sub-rede e de rede de grupo de segurança criado nos passos anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar um NIC a uma VM existente, desaloque a VM com [az vm deallocate](/cli/azure/vm). O exemplo seguinte desaloca a VM com o nome *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicionar a NIC com o [az vm nic adicionar](/cli/azure/vm/nic). O exemplo seguinte adiciona *myNic3* ao *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Iniciar a VM com [início de vm de az](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adicionar tabelas de roteamento para o SO convidado, concluindo os passos em [configurar o sistema operacional convidado para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Remover um NIC de uma VM
Para remover um NIC de uma VM existente, desaloque a VM com [az vm deallocate](/cli/azure/vm). O exemplo seguinte desaloca a VM com o nome *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Remover a NIC com o [az vm nic remover](/cli/azure/vm/nic). O exemplo seguinte remove *myNic3* partir *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Iniciar a VM com [início de vm de az](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar vários NICs com modelos do Resource Manager
Modelos Azure Resource Manager utilizam ficheiros declarativos do JSON para definir o seu ambiente. Pode ler uma [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Modelos do Resource Manager fornecem uma forma de criar várias instâncias de um recurso durante a implementação, como a criação de várias NICs. Utilizar *cópia* para especificar o número de instâncias para criar:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre [criar várias instâncias usando *cópia*](../../resource-group-create-multiple.md). 

Também pode utilizar um `copyIndex()` para, em seguida, anexar um número para um nome de recurso, o que permite que crie `myNic1`, `myNic2`, etc. O código a seguir mostra um exemplo de acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de [criando vários NICs com modelos do Resource Manager](../../virtual-network/template-samples.md).

Adicionar tabelas de roteamento para o SO convidado, concluindo os passos em [configurar o sistema operacional convidado para vários NICs](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o sistema operacional convidado para vários NICs

Os passos anteriores criaram uma rede virtual e uma sub-rede, anexado NICs e criado uma VM. Uma pública IP endereço e a rede grupo regras de segurança que permitam o tráfego SSH não foram criadas. Para configurar o sistema operacional convidado para vários NICs, terá de permitir ligações remotas e executar comandos localmente na VM.

Para permitir o tráfego SSH, crie uma regra de grupo de segurança de rede com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) da seguinte forma:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Criar um endereço IP público [criar a rede de az public-ip](/cli/azure/network/public-ip#az-network-public-ip-create) e atribua-ao NIC com o primeiro [atualização de configuração de ip de nic de rede de az](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Para ver o endereço IP público da VM, utilize [show de vm de az](/cli/azure/vm#az-vm-show) da seguinte forma::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Agora SSH para o endereço IP público da sua VM. O nome de utilizador predefinido fornecido num passo anterior foi *azureuser*. Fornece seu próprio nome de utilizador e o endereço IP público:

```bash
ssh azureuser@137.117.58.232
```

Para enviar para ou a partir de uma interface de rede secundária, terá de adicionar manualmente as rotas persistentes para o sistema operativo para cada interface de rede secundária. Neste artigo, *eth1* é a interface secundária. Instruções para adicionar rotas persistentes para o sistema operativo variam consoante a distribuição. Consulte a documentação para sua distribuição para obter instruções.

Ao adicionar a rota para o sistema operativo, o endereço de gateway é *.1* para qualquer sub-rede a interface de rede está em. Por exemplo, se a interface de rede é atribuída o endereço *10.0.2.4*, o gateway que especificar para a rota é *10.0.2.1*. Pode definir uma rede específica para o destino da rota ou especifique um destino de *0.0.0.0*, se pretender que todo o tráfego para a interface de percorrer o gateway especificado. O gateway para cada sub-rede é gerido através da rede virtual.

Depois de adicionar a rota para uma interface de rede secundária, certifique-se de que a rota está na sua tabela de rotas com `route -n`. O resultado de exemplo seguinte é para a tabela de rota que tem as interfaces de rede de duas adicionadas à VM neste artigo:

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

Confirme que a rota adicionou persiste entre reinícios através da verificação da sua tabela de rotas novamente após um reinício. Para testar a conectividade, pode introduzir o seguinte comando, por exemplo, onde *eth1* é o nome de uma interface de rede secundária:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Passos seguintes
Revisão [tamanhos de VM do Linux](sizes.md) ao tentar criar uma VM com várias NICs. Preste atenção para o número máximo de NICs oferece suporte a cada tamanho de VM.

Para ainda mais segura as suas VMs, utilize just-in-acesso à VM do tempo. Esta funcionalidade é aberta a regras de grupo para segurança de rede para tráfego SSH, quando necessário e para um período de tempo definido. Para obter mais informações, veja [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Gerir o acesso da máquina virtual através do just in time).
