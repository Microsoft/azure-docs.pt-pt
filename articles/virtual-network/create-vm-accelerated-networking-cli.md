---
title: Criar um VM Azure com Networking Acelerado utilizando o Azure CLI
description: Aprenda a criar uma máquina virtual Linux com Networking Acelerado ativado.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 54c4a673e654a0244183a84ffa841d553ae6db51
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106258"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Crie uma máquina virtual Linux com Networking Acelerado usando o Azure CLI

Neste tutorial, aprende-se a criar uma máquina virtual Linux (VM) com Networking Acelerado. Para criar um VM Windows com Networking Acelerado, consulte [Criar um VM Windows com Networking Acelerado](create-vm-accelerated-networking-powershell.md). A ligação acelerada em rede permite a virtualização de I/O de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este caminho de alto desempenho contorna o hospedeiro a partir da trajetória de dados, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A imagem seguinte mostra a comunicação entre dois VMs com e sem ligação acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem uma rede acelerada, todo o tráfego em rede dentro e fora do VM deve atravessar o hospedeiro e o interruptor virtual. O interruptor virtual fornece todas as aplicações políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços virtualizados da rede para o tráfego de rede. Para saber mais sobre os interruptores virtuais, leia a virtualização da rede Hyper-V e o artigo [de comutação virtual.](https://technet.microsoft.com/library/jj945275.aspx)

Com uma rede acelerada, o tráfego de rede chega à interface de rede da máquina virtual (NIC), e é depois encaminhado para o VM. Todas as políticas de rede que o interruptor virtual aplica são agora descarregadas e aplicadas em hardware. A aplicação da política em hardware permite ao NIC encaminhar o tráfego de rede diretamente para o VM, contornando o anfitrião e o interruptor virtual, mantendo ao mesmo tempo toda a política aplicada no hospedeiro.

Os benefícios da ligação acelerada em rede aplicam-se apenas ao VM em que está ativado. Para obter os melhores resultados, é ideal permitir esta funcionalidade em pelo menos dois VMs ligados à mesma rede virtual Azure (VNet). Ao comunicar através de VNets ou ligar-se no local, esta funcionalidade tem um impacto mínimo na latência geral.

## <a name="benefits"></a>Benefícios
* **Latência inferior / Pacotes mais altos por segundo (pps):** Remover o interruptor virtual da pata de dados remove o tempo que os pacotes gastam no hospedeiro para processamento de políticas e aumenta o número de pacotes que podem ser processados dentro do VM.
* **Nervosismo reduzido:** O processamento de interruptores virtuais depende da quantidade de política que precisa de ser aplicada e da carga de trabalho da CPU que está a fazer o processamento. Descarregar a aplicação da política para o hardware remove essa variabilidade entregando pacotes diretamente ao VM, removendo o anfitrião para a comunicação VM e todas as interrupções de software e interruptores de contexto.
* **Diminuição da utilização do CPU:** Contornar o interruptor virtual no hospedeiro leva a uma menor utilização de CPU para o tráfego da rede de processamento.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure: 
* **Ubuntu 14.04 com o núcleo linux-azure**
* **Ubuntu 16.04 ou mais tarde** 
* **SLES12 SP3 ou mais tarde** 
* **RHEL 7.4 ou mais tarde**
* **CentOS 7.4 ou mais tarde**
* **CoreOS Linux**
* **Debian "Esticar" com núcleo de backports**
* **Oracle Linux 7.4 e mais tarde com Kernel Compatível com Chapéu Vermelho (RHCK)**
* **Oracle Linux 7.5 e mais tarde com versão UEK 5**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Limitações e Constrangimentos

### <a name="supported-vm-instances"></a>Casos de VM suportados
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para a computação com 2 ou mais vCPUs.  Estas séries apoiadas são: D/DSv2 e F/Fs

Em casos que suportam hiperthreading, o Networking Acelerado é suportado em casos vM com 4 ou mais vCPUs. As séries suportadas são: D/Dsv3, D/Dsv4, E/Esv3, Ea/Easv4, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre os casos de VM, consulte os [tamanhos de VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Imagens Personalizadas
Se estiver a utilizar uma imagem personalizada e os suportes de imagem Para a Rede Acelerada, certifique-se de que os condutores necessários trabalham com os NICs Mellanox ConnectX-3 e ConnectX-4 Lx no Azure.

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas de Azure, bem como em Nuvens do Governo Azure.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitar a rede acelerada num VM em execução
Um tamanho VM suportado sem ligação acelerada de rede só pode ter a funcionalidade ativada quando é parada e negociada.  
### <a name="deployment-through-azure-resource-manager"></a>Implantação através do Gestor de Recursos Azure
As máquinas virtuais (clássicas) não podem ser implantadas com networking acelerado.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Crie um VM Linux com Rede Acelerada Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo forneça passos para criar uma máquina virtual com networking acelerado utilizando o Azure CLI, também pode [criar uma máquina virtual com networking acelerado utilizando o portal Azure.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ao criar uma máquina virtual no portal, na **Create a virtual machine** blade, escolha o separador **Networking.**  Neste separador, existe uma opção para **a rede Acelerada**.  Se escolheu um [sistema operativo suportado](#supported-operating-systems) e [o tamanho vm,](#supported-vm-instances)esta opção irá automaticamente povoar para "On".  Caso contrário, irá povoar a opção "Off" para a Rede Acelerada e dar ao utilizador uma razão para não estar ativado.   

* *Nota:* Apenas sistemas operativos suportados podem ser ativados através do portal.  Se estiver a utilizar uma imagem personalizada e a sua imagem suportar o Networking Acelerado, por favor crie o seu VM utilizando CLI ou PowerShell. 

Após a criação da máquina virtual, pode confirmar que a Rede Acelerada está ativada seguindo as instruções no [Confirmo de que a rede acelerada está ativada](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Criação cli
### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Instale o mais recente [Azure CLI](/cli/azure/install-azure-cli) e inicie sessão numa conta Azure utilizando [login az](/cli/azure/reference-index). Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myNic*, e *myVm*.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *central:*

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecione uma região linux apoiada listada em [rede acelerada linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual chamada *myVnet* com uma subnet:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Criar um grupo de segurança de rede com [a rede az nsg criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

O grupo de segurança da rede contém várias regras predefinidas, uma das quais desativa todos os acessos de entrada a partir da Internet. Abra uma porta para permitir o acesso sSH à máquina virtual com [a regra nsg da rede Az criar:](/cli/azure/network/nsg/rule)

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com networking acelerado

Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). Não é necessário um endereço IP público se não pretender aceder à máquina virtual a partir da Internet, mas para completar os passos deste artigo, é necessário.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Criar uma interface de rede com [a z network nic criar](/cli/azure/network/nic) com rede acelerada ativada. O exemplo seguinte cria uma interface de rede chamada *myNic* na subnet *mySubnet* da rede virtual *myVnet* e associa o grupo de segurança de rede *myNetworkSecurityGroup* à interface de rede:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Crie um VM e prenda o NIC
Quando criar o VM, especifique o NIC com o qual criou `--nics`. Selecione um tamanho e distribuição listados em [rede acelerada linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria um VM chamado *myVM* com a imagem UbuntuLTS e um tamanho que suporta networking acelerado *(Standard_DS4_v2):*

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

Para obter uma lista de todos os tamanhos e características vm, consulte os [tamanhos de VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Uma vez criado o VM, a saída semelhante à seguinte saída de exemplo é devolvida. Anote o **publicIpAddress**. Este endereço é utilizado para aceder ao VM em etapas posteriores.

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

Utilize o seguinte comando para criar uma sessão SSH com a VM. Substitua `<your-public-ip-address>` pelo endereço IP público atribuído à máquina virtual que criou e substitua `--admin-username` o *azureuser* se utilizou um valor diferente para quando criou o VM.

```bash
ssh azureuser@<your-public-ip-address>
```

A partir da `uname -r` concha bash, insira e confirme que a versão kernel é uma das seguintes versões, ou maior:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **Centos**: 7.4.20171206


Confirme que o dispositivo VF Mellanox `lspci` está exposto ao VM com o comando. A saída devolvida é semelhante à seguinte saída:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Verifique a atividade no VF (função virtual) com o `ethtool -S eth0 | grep vf_` comando. Se receber uma saída semelhante à seguinte saída de amostra, a ligação acelerada está ativada e a funcionar.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
O Networking Acelerado está agora ativado para o seu VM.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Manuseie a ligação dinâmica e a revogação da função virtual 
As aplicações devem passar por cima do NIC sintético que está exposto em VM. Se a aplicação passar diretamente sobre o VF NIC, não recebe **todos os** pacotes que estão destinados ao VM, uma vez que alguns pacotes aparecem sobre a interface sintética.
Se executar uma aplicação sobre o NIC sintético, garante que a aplicação recebe **todos os** pacotes que estão destinados a ele. Também garante que a aplicação continua a funcionar, mesmo que o VF seja revogado quando o hospedeiro está a ser reparado. Aplicações vinculativas ao NIC sintético é um requisito **obrigatório** para todas as aplicações que tirem partido da **Rede Acelerada**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Ativar a rede acelerada nos VMs existentes
Se criou um VM sem Networking Acelerado, é possível ativar esta funcionalidade num VM existente.  O VM deve apoiar o Networking Acelerado, cumprindo os seguintes pré-requisitos que também estão descritos acima:

* O VM deve ser um tamanho suportado para a rede acelerada
* O VM deve ser uma imagem suportada da Galeria Azure (e versão kernel para Linux)
* Todos os VMs num conjunto de disponibilidade ou VMSS devem ser parados/deallocalizados antes de permitir a Rede Acelerada em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs individuais & VMs em conjunto de disponibilidade
Primeira paragem/deslocar o VM ou, se um Conjunto de Disponibilidade, todos os VMs do conjunto:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Importante, por favor note que se o seu VM foi criado individualmente, sem um conjunto de disponibilidade, só precisa de parar/desalojar o VM individual para permitir a Ligação acelerada.  Se o seu VM foi criado com um conjunto de disponibilidade, todos os VMs contidos no conjunto de disponibilidade terão de ser parados/deallocalizados antes de permitir a Rede Acelerada em qualquer um dos NICs. 

Uma vez parado, ative a Rede Acelerada no NIC do seu VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Reinicie o seu VM ou, se num Conjunto de Disponibilidade, todos os VMs do Conjunto e confirme que o Networking Acelerado está ativado: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
O VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Primeiro, parem os VMs:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Assim que os VMs estiverem parados, atualize a propriedade De Rede Acelerada sob a interface de rede:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Tenha em anote que um VMSS tem atualizações VM que aplicam atualizações utilizando três configurações diferentes, automáticas, rolantes e manuais.  Nestas instruções, a política é definida como automática para que o VMSS reacione as alterações imediatamente após o reinício.  Para o colocar em automático de modo a que as alterações sejam imediatamente captadas: 

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

Assim que reiniciar, aguarde que as atualizações terminem, mas uma vez concluída, o VF aparecerá dentro do VM.  (Certifique-se de que está a utilizar um osso e tamanho VM suportados.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar os VMexistentes existentes com Networking Acelerado

Os VMs com Networking Acelerado ativado só podem ser redimensionados para VMs que suportam o Networking Acelerado.  

Um VM com rede acelerada ativado não pode ser redimensionado para uma instância VM que não suporta a rede acelerada utilizando a operação de redimensionamento.  Em vez disso, para redimensionar um destes VMs: 

* Stop/Deallocate o VM ou se num conjunto de disponibilidade/VMSS, pare/deslocar todos os VMs no conjunto/VMSS.
* A ligação acelerada deve ser desativada no NIC do VM ou se num conjunto de disponibilidade/VMSS, todos os VMs no conjunto/VMSS.
* Uma vez desativada a rede acelerada, o conjunto VM/disponibilidade/VMSS pode ser movido para um novo tamanho que não suporta o Networking Acelerado e reiniciado.  

