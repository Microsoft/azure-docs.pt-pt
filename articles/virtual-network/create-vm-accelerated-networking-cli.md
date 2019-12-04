---
title: Criar uma VM do Azure com rede acelerada usando o CLI do Azure
description: Saiba como criar uma máquina virtual Linux com rede acelerada habilitada.
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
ms.openlocfilehash: eb44163922e318d17d675143ca2d6a3a1fa4ed75
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793317"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Criar uma máquina virtual Linux com rede acelerada usando o CLI do Azure

Neste tutorial, você aprenderá a criar uma VM (máquina virtual) do Linux com rede acelerada. Para criar uma VM do Windows com rede acelerada, consulte [criar uma VM do Windows com rede acelerada](create-vm-accelerated-networking-powershell.md). A rede acelerada habilita o SR-IOV (virtualização de e/s de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de DataPath, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos de VM com suporte. A figura a seguir mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem a rede acelerada, todo o tráfego de rede de entrada e saída da VM deve atravessar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre os comutadores virtuais, leia o artigo [virtualização de rede Hyper-V e comutador virtual](https://technet.microsoft.com/library/jj945275.aspx) .

Com a rede acelerada, o tráfego de rede chega à NIC (interface de rede) da máquina virtual e, em seguida, é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica agora são descarregadas e aplicadas no hardware. A aplicação da política em hardware permite que a NIC encaminhe o tráfego de rede diretamente para a VM, ignorando o host e o comutador virtual, mantendo toda a política aplicada no host.

Os benefícios da rede acelerada só se aplicam à VM em que ela está habilitada. Para obter os melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou conectar-se localmente, esse recurso tem um impacto mínimo sobre a latência geral.

## <a name="benefits"></a>Vantagens
* **Latência mais baixa/pacotes maiores por segundo (PPS):** Remover o comutador virtual do caminho de data remove o tempo que os pacotes gastam no host para processamento de política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Variação reduzida:** O processamento do comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade fornecendo pacotes diretamente para a VM, removendo o host para comunicação de VM e todas as interrupções de software e alternâncias de contexto.
* **Utilização de CPU reduzida:** Ignorar o comutador virtual no host leva a menos utilização de CPU para processar o tráfego de rede.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições a seguir têm suporte pronto para uso na galeria do Azure: 
* **Ubuntu 14, 4 com o kernel Linux-Azure**
* **Ubuntu 16, 4 ou posterior** 
* **SLES12 SP3 ou posterior** 
* **RHEL 7,4 ou posterior**
* **CentOS 7,4 ou posterior**
* **CoreOS Linux**
* **Debian "Stretch" com kernel de backports**
* **Oracle Linux 7,4 e posterior com kernel compatível com Red Hat (RHCK)**
* **Oracle Linux 7,5 e posterior com UEK versão 5**
* **FreeBSD 10,4, 11,1 & 12,0**

## <a name="limitations-and-constraints"></a>Limitações e restrições

### <a name="supported-vm-instances"></a>Instâncias de VM com suporte
A rede acelerada tem suporte na maioria dos tamanhos de instância de uso geral e otimizado para computação com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/FS

Em instâncias que dão suporte a hyperthreading, a rede acelerada tem suporte em instâncias de VM com 4 ou mais vCPUs. As séries com suporte são: D/Dsv3, E/Esv3, Fsv2, Lsv2, MS/MMS e MS/Mmsv2.

Para obter mais informações sobre instâncias de VM, consulte [tamanhos de VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Imagens Personalizadas
Se você estiver usando uma imagem personalizada e sua imagem der suporte à rede acelerada, certifique-se de ter os drivers necessários para trabalhar com NICs Mellanox ConnectX-3 e ConnectX-4 LX no Azure.

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas do Azure, bem como nas nuvens do Azure governamental.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitando a rede acelerada em uma VM em execução
Um tamanho de VM com suporte sem rede acelerada habilitada só pode ter o recurso habilitado quando é interrompido e desalocado.  
### <a name="deployment-through-azure-resource-manager"></a>Implantação por meio do Azure Resource Manager
As máquinas virtuais (clássicas) não podem ser implantadas com rede acelerada.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Criar uma VM do Linux com a rede acelerada do Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo forneça etapas para criar uma máquina virtual com rede acelerada usando o CLI do Azure, você também pode [criar uma máquina virtual com rede acelerada usando o portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, na folha **criar uma máquina virtual** , escolha a guia **rede** .  Nessa guia, há uma opção para **rede acelerada**.  Se você tiver escolhido um [sistema operacional com suporte](#supported-operating-systems) e um [tamanho de VM](#supported-vm-instances), essa opção será preenchida automaticamente como "ativada".  Caso contrário, ele preencherá a opção "desativado" para rede acelerada e dará ao usuário um motivo pelo qual ele não está habilitado.   

* *Observação:* Somente sistemas operacionais com suporte podem ser habilitados por meio do Portal.  Se você estiver usando uma imagem personalizada e sua imagem der suporte à rede acelerada, crie sua VM usando a CLI ou o PowerShell. 

Depois que a máquina virtual for criada, você poderá confirmar se a rede acelerada está habilitada seguindo as instruções em [confirmar se a rede acelerada está habilitada](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Criação de CLI
### <a name="create-a-virtual-network"></a>Criar rede virtual

Instale o [CLI do Azure](/cli/azure/install-azure-cli) mais recente e faça logon em uma conta do Azure usando [AZ login](/cli/azure/reference-index). Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Exemplos de nomes de parâmetro incluem *MyResource*, *myNic*e *myVm*.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group no local *centralus* :

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecione uma região do Linux com suporte listada em [rede acelerada do Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

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
Crie um grupo de segurança de rede com [AZ Network NSG Create](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

O grupo de segurança de rede contém várias regras padrão, uma das quais desabilita todo o acesso de entrada da Internet. Abra uma porta para permitir o acesso SSH à máquina virtual com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule):

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

Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). Um endereço IP público não é necessário se você não planeja acessar a máquina virtual pela Internet, mas para concluir as etapas neste artigo, ele é necessário.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Crie um adaptador de rede com [AZ Network NIC Create](/cli/azure/network/nic) com rede acelerada habilitada. O exemplo a seguir cria uma interface de rede chamada *myNic* na sub-rede *mysubnet* da rede virtual *myVnet* e associa o grupo de segurança de rede *myNetworkSecurityGroup* à interface de rede:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Criar uma VM e anexar a NIC
Ao criar a VM, especifique a NIC que você criou com `--nics`. Selecione um tamanho e uma distribuição listados em [rede acelerada do Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada *myVM* com a imagem UbuntuLTS e um tamanho que dá suporte à rede acelerada (*Standard_DS4_v2*):

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

Para obter uma lista de todos os tamanhos e características de VM, consulte [tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Depois que a VM é criada, uma saída semelhante à seguinte saída de exemplo é retornada. Anote o **publicIpAddress**. Esse endereço é usado para acessar a VM nas etapas subsequentes.

```azurecli
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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirmar se a rede acelerada está habilitada

Utilize o seguinte comando para criar uma sessão SSH com a VM. Substitua `<your-public-ip-address>` pelo endereço IP público atribuído à máquina virtual que você criou e substitua *azureuser* se você usou um valor diferente para `--admin-username` quando criou a VM.

```bash
ssh azureuser@<your-public-ip-address>
```

No shell bash, insira `uname -r` e confirme se a versão do kernel é uma das seguintes versões ou mais:

* **Ubuntu 16, 4**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Confirme se o dispositivo Mellanox VF está exposto à VM com o comando `lspci`. A saída retornada é semelhante à seguinte saída:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Verifique se há atividade no FV (função virtual) com o comando `ethtool -S eth0 | grep vf_`. Se você receber uma saída semelhante à saída de exemplo a seguir, a rede acelerada estará habilitada e funcionando.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
A rede acelerada agora está habilitada para sua VM.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Manipular associação dinâmica e revogação da função virtual 
Os aplicativos devem ser executados sobre a NIC sintética que é exposta na VM. Se o aplicativo for executado diretamente pela NIC VF, ele não receberá **todos os** pacotes destinados à VM, já que alguns pacotes aparecem na interface sintética.
Se você executar um aplicativo na NIC sintética, ele garante que o aplicativo receba **todos os** pacotes destinados a ele. Ele também garante que o aplicativo continue em execução, mesmo que o FV seja revogado quando o host estiver sendo atendido. A associação de aplicativos à NIC sintética é um requisito **obrigatório** para todos os aplicativos que aproveitam a **rede acelerada**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Habilitar rede acelerada em VMs existentes
Se você tiver criado uma VM sem rede acelerada, será possível habilitar esse recurso em uma VM existente.  A VM deve dar suporte à rede acelerada ao atender aos seguintes pré-requisitos que também são descritos acima:

* A VM deve ter um tamanho com suporte para rede acelerada
* A VM deve ser uma imagem da galeria do Azure com suporte (e a versão do kernel para Linux)
* Todas as VMs em um conjunto de disponibilidade ou VMSS devem ser interrompidas/desalocadas antes de habilitar a rede acelerada em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs individuais & VMs em um conjunto de disponibilidade
Primeiro pare/Desaloque a VM ou, se houver um conjunto de disponibilidade, todas as VMs no conjunto:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Importante. Observe que, se a VM tiver sido criada individualmente, sem um conjunto de disponibilidade, você só precisará parar/desalocar a VM individual para habilitar a rede acelerada.  Se sua VM foi criada com um conjunto de disponibilidade, todas as VMs contidas no conjunto de disponibilidade precisarão ser interrompidas/desalocadas antes de habilitar a rede acelerada em qualquer uma das NICs. 

Depois de parar, habilite a rede acelerada na NIC da VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Reinicie sua VM ou, se estiver em um conjunto de disponibilidade, todas as VMs no conjunto e confirme se a rede acelerada está habilitada: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS é um pouco diferente, mas segue o mesmo fluxo de trabalho.  Primeiro, pare as VMs:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Depois que as VMs forem interrompidas, atualize a propriedade de rede acelerada na interface de rede:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Observe que um VMSS tem atualizações de VM que aplicam atualizações usando três configurações diferentes, automática, sem interrupção e manual.  Nessas instruções, a política é definida como automática para que o VMSS selecione as alterações imediatamente após a reinicialização.  Para defini-lo como automático para que as alterações sejam imediatamente selecionadas: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Por fim, reinicie o VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Depois de reiniciar, aguarde a conclusão das atualizações, mas depois de concluída, o FV aparecerá dentro da VM.  (Verifique se você está usando um sistema operacional e tamanho de VM com suporte.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionando VMs existentes com rede acelerada

As VMs com rede acelerada habilitada só podem ser redimensionadas para VMs que dão suporte à rede acelerada.  

Uma VM com rede acelerada habilitada não pode ser redimensionada para uma instância de VM que não dá suporte à rede acelerada usando a operação de redimensionamento.  Em vez disso, para redimensionar uma dessas VMs: 

* Pare/Desaloque a VM ou se estiver em um conjunto de disponibilidade/VMSS, pare/desaloque todas as VMs no conjunto/VMSS.
* A rede acelerada deve ser desabilitada na NIC da VM ou se estiver em um conjunto de disponibilidade/VMSS, todas as VMs no conjunto/VMSS.
* Quando a rede acelerada está desabilitada, a VM/conjunto de disponibilidade/VMSS pode ser movida para um novo tamanho que não dá suporte à rede acelerada e reiniciada.  

