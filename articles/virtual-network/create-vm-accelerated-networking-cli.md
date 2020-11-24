---
title: Criar um VM Azure com Rede Acelerada usando Azure CLI
description: Saiba como criar uma máquina virtual Linux com rede acelerada ativada.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: bccbfed96dd6cd87bdfe986baf4b52817a160ac0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533366"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Criar uma máquina virtual Linux com Rede Acelerada usando O Azure CLI

Neste tutorial, aprende-se a criar uma máquina virtual Linux (VM) com Rede Acelerada. Para criar um VM do Windows com rede acelerada, consulte [criar um VM do Windows com rede acelerada](create-vm-accelerated-networking-powershell.md). A rede acelerada permite a virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este percurso de alto desempenho contorna o hospedeiro do datapath, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A seguinte imagem mostra a comunicação entre dois VMs com e sem rede acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem uma rede acelerada, todo o tráfego de ligação em rede dentro e fora do VM deve atravessar o hospedeiro e o interruptor virtual. O interruptor virtual fornece toda a aplicação de políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços virtualizados de rede para o tráfego de rede. Para saber mais sobre os interruptores virtuais, leia a virtualização da rede Hyper-V e o artigo [de comutação virtual.](https://technet.microsoft.com/library/jj945275.aspx)

Com a rede acelerada, o tráfego de rede chega à interface de rede da máquina virtual (NIC), e é depois encaminhado para o VM. Todas as políticas de rede que o interruptor virtual aplica são agora descarregadas e aplicadas em hardware. A aplicação da política em hardware permite ao NIC encaminhar o tráfego da rede diretamente para o VM, contornando o hospedeiro e o comutador virtual, mantendo ao mesmo tempo toda a política que aplicava no anfitrião.

Os benefícios da rede acelerada aplicam-se apenas ao VM em que está ativado. Para obter os melhores resultados, é ideal para ativar esta funcionalidade em pelo menos dois VMs ligados à mesma rede virtual Azure (VNet). Ao comunicar através de VNets ou ligar no local, esta funcionalidade tem um impacto mínimo na latência geral.

## <a name="benefits"></a>Benefícios
* **Menor Latência / Pacotes mais altos por segundo (pps):** Remover o interruptor virtual do datapath remove o tempo que os pacotes de tempo passam no anfitrião para processamento de políticas e aumenta o número de pacotes que podem ser processados dentro do VM.
* **Nervosismo reduzido:** O processamento do comutação virtual depende da quantidade de política que precisa de ser aplicada e da carga de trabalho do CPU que está a fazer o processamento. O descarregamento da aplicação da política para o hardware remove essa variabilidade entregando pacotes diretamente ao VM, removendo o hospedeiro para a comunicação VM e todas as interrupções de software e interruptores de contexto.
* **Diminuição da utilização do CPU:** Contornar o interruptor virtual no hospedeiro leva a uma menor utilização do CPU para o tráfego da rede de processamento.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure: 
* **Ubuntu 14.04 com o kernel linux-azure**
* **Ubuntu 16.04 ou mais tarde** 
* **SLES12 SP3 ou mais tarde** 
* **RHEL 7.4 ou mais tarde**
* **CentOS 7.4 ou mais tarde**
* **CoreOS Linux**
* **Debian "Stretch" com kernel de backports, Debian "Buster" ou mais tarde**
* **Oracle Linux 7.4 e mais tarde com Chapéu Vermelho Compatível Kernel (RHCK)**
* **Oracle Linux 7.5 e mais tarde com a versão 5 da UEK**
* **FreeBSD 10.4, 11.1 & 12.0 ou mais tarde**

## <a name="limitations-and-constraints"></a>Limitações e Restrições

### <a name="supported-vm-instances"></a>Instâncias VM apoiadas
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para o cálculo com 2 ou mais vCPUs.  Estas séries suportadas são: D/DSv2 e F/Fs

Em casos que suportam a hiper-leitura, o Networking Acelerado é suportado em instâncias VM com 4 ou mais vCPUs. As séries apoiadas são: D/Dsv3, D/Dsv4, Dd/Ddv4, Da/Dasv4, E/Esv3, E/Esv4, Ed/Edsv4, Ea/Easv4, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre os casos de VM, consulte os [tamanhos do Linux VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Imagens Personalizadas
Se estiver a utilizar uma imagem personalizada e a sua imagem suportar rede acelerada, certifique-se de que tem os controladores necessários para trabalhar com mellanox ConnectX-3 e ConnectX-4 Lx NICs no Azure.

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas de Azure, bem como em Nuvens do Governo de Azure.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Ativar a rede acelerada num VM em execução
Um tamanho VM suportado sem rede acelerada ativado só pode ter a funcionalidade ativada quando esta é interrompida elocada.  
### <a name="deployment-through-azure-resource-manager"></a>Implementação através do Gestor de Recursos Azure
As máquinas virtuais (clássicas) não podem ser implantadas com rede acelerada.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Criar um Linux VM com rede acelerada Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo forneça passos para criar uma máquina virtual com rede acelerada utilizando o Azure CLI, também pode [criar uma máquina virtual com rede acelerada utilizando o portal Azure.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ao criar uma máquina virtual no portal, na **lâmina de máquina virtual,** escolha o **separador 'Rede'.**  Neste separador, existe uma opção para **a rede acelerada.**  Se tiver escolhido um [sistema operativo suportado](#supported-operating-systems) e tamanho [VM,](#supported-vm-instances)esta opção preencherá automaticamente para "On".  Caso contrário, preencherá a opção "Off" para a rede acelerada e dará ao utilizador uma razão pela qual não está ativada.   

* *Nota:* Apenas sistemas operativos suportados podem ser ativados através do portal.  Se estiver a utilizar uma imagem personalizada e a sua imagem suportar rede acelerada, por favor crie o seu VM utilizando CLI ou PowerShell. 

Após a criação da máquina virtual, pode confirmar que a rede acelerada está ativada seguindo as instruções no [Confirmo de que a rede acelerada está ativada](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Criação do CLI
### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Instale o mais recente [Azure CLI](/cli/azure/install-azure-cli) e faça login numa conta Azure utilizando [o login az](/cli/azure/reference-index). Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myNic* e *myVm*.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *central:*

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecione uma região de Linux apoiada listada em [rede acelerada linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Criar um grupo de segurança de rede com [a criação de nsg de rede Az](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

O grupo de segurança da rede contém várias regras predefinições, uma das quais desativa todo o acesso à entrada a partir da Internet. Abra uma porta para permitir o acesso do SSH à máquina virtual com [a regra nsg de rede az criar:](/cli/azure/network/nsg/rule)

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com rede acelerada

Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). Um endereço IP público não é necessário se você não planeja aceder à máquina virtual a partir da Internet, mas para completar os passos neste artigo, é necessário.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Crie uma interface de rede com [a az network nic criar](/cli/azure/network/nic) com rede acelerada ativada. O exemplo a seguir cria uma interface de rede chamada *myNic* na sub-rede *mySubnet* da rede virtual *myVnet* e associa o grupo de segurança da rede *myNetworkSecurityGroup* à interface de rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Crie um VM e anexe o NIC
Quando criar o VM, especifique o NIC que criou com `--nics` . Selecione um tamanho e distribuição listados em [rede acelerada Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria um VM chamado *myVM* com a imagem UbuntuLTS e um tamanho que suporta networking acelerado *(Standard_DS4_v2):*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Para obter uma lista de todos os tamanhos e características VM, consulte os [tamanhos linux VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Uma vez criado o VM, a saída semelhante à saída de exemplo a seguir é devolvida. Anote o **publicIpAddress**. Este endereço é utilizado para aceder ao VM nos passos seguintes.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirme que a rede acelerada está ativada

Utilize o seguinte comando para criar uma sessão SSH com a VM. Substitua `<your-public-ip-address>` o endereço IP público atribuído à máquina virtual que criou e substitua o *azureuser* se tiver usado um valor diferente para `--admin-username` quando criou o VM.

```bash
ssh azureuser@<your-public-ip-address>
```

A partir da concha Bash, insira `uname -r` e confirme que a versão kernel é uma das seguintes versões, ou maior:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Confirme que o dispositivo VF Mellanox está exposto ao VM com o `lspci` comando. A saída devolvida é semelhante à seguinte saída:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Verifique se há atividade no VF (função virtual) com o `ethtool -S eth0 | grep vf_` comando. Se receber uma saída semelhante à seguinte saída da amostra, a rede acelerada está ativada e funciona.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
A rede acelerada está agora ativada para o seu VM.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Lidar com a ligação dinâmica e revogação da função virtual 
As aplicações devem passar por cima do NIC sintético que está exposto em VM. Se a aplicação correr diretamente sobre o VF NIC, não recebe **todos os** pacotes que estão destinados ao VM, uma vez que alguns pacotes aparecem sobre a interface sintética.
Se executar uma aplicação sobre o NIC sintético, garante que a aplicação recebe **todos os** pacotes que estão destinados a ele. Também garante que a aplicação continua em funcionamento, mesmo que o VF seja revogado quando o hospedeiro está a ser reparado. Aplicações vinculativas ao NIC sintético é um requisito **obrigatório** para todas as aplicações que tirem partido da **Rede Acelerada.**

## <a name="enable-accelerated-networking-on-existing-vms"></a>Ativar a rede acelerada nos VM existentes
Se criou um VM sem Rede Acelerada, é possível ativar esta funcionalidade num VM existente.  O VM deve apoiar a Rede Acelerada, cumprindo os seguintes pré-requisitos acima indicados:

* O VM deve ser um tamanho suportado para networking acelerado
* O VM deve ser uma imagem suportada da Galeria Azure (e versão kernel para Linux)
* Todos os VMs de um conjunto de disponibilidade ou VMSS devem ser interrompidos/locados antes de permitir a rede acelerada em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs individuais & VMs em um conjunto de disponibilidade
Primeira paragem/translocação do VM ou, se um Conjunto de Disponibilidade, todos os VMs no Conjunto:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Importante, por favor, note que se o seu VM foi criado individualmente, sem um conjunto de disponibilidade, basta parar/negociar o VM individual para permitir a Rede Acelerada.  Se o seu VM foi criado com um conjunto de disponibilidade, todos os VM contidos no conjunto de disponibilidade terão de ser interrompidos/translocados antes de permitir a rede acelerada em qualquer um dos NICs. 

Uma vez parado, ative a rede acelerada no NIC do seu VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Reinicie o seu VM ou, se num Conjunto de Disponibilidade, todos os VMs do Conjunto e confirme que a rede acelerada está ativada: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
O VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Primeiro, pare os VMs:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Uma vez parados os VMs, atualize a propriedade de Rede Acelerada sob a interface de rede:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Por favor, note que um VMSS tem atualizações VM que aplicam atualizações usando três configurações diferentes, automáticas, rolantes e manuais.  Nestas instruções, a política é definida de forma automática para que o VMSS recolhe as alterações imediatamente após o reinício.  Para defini-lo para automático para que as alterações sejam imediatamente recolhidas: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Finalmente, reinicie o VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Uma vez reiniciado, aguarde que as atualizações terminem, mas uma vez concluídas, o VF aparecerá dentro do VM.  (Certifique-se de que está a utilizar um tamanho de SO e VM suportado.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionamento de VMs existentes com rede acelerada

VMs com Rede Acelerada ativada só podem ser redimensionados para VMs que suportam Rede Acelerada.  

Um VM com rede acelerada ativado não pode ser redimensionado para uma instância VM que não suporta rede acelerada utilizando a operação de redimensionamento.  Em vez disso, para redimensionar um destes VMs: 

* Parar/Negociar o VM ou se num conjunto de disponibilidade/VMSS, parar/negociar todos os VMs no set/VMSS.
* A rede acelerada deve ser desativada no NIC do VM ou se num conjunto de disponibilidade/VMSS, todos os VMs no conjunto/VMSS.
* Uma vez desativada a rede acelerada, o conjunto VM/disponibilidade/VMSS pode ser movido para um novo tamanho que não suporta rede acelerada e reiniciado.  

