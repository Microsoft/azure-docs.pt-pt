---
title: Criar uma máquina virtual do Azure com o Accelerated Networking | Documentos da Microsoft
description: Saiba como criar uma máquina virtual Linux com redes aceleradas ativada.
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
ms.openlocfilehash: b06bc6b41081f05a7067f82f46affc37d21f50b1
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213301"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual Linux com redes aceleradas

Neste tutorial, saiba como criar uma máquina virtual (VM) do Linux com redes aceleradas. Para criar uma VM do Windows com redes aceleradas, consulte [criar uma VM do Windows com redes aceleradas](create-vm-accelerated-networking-powershell.md). Funcionamento em rede acelerado permite que a virtualização de e/s de raiz única (SR-IOV) para uma VM, melhorando em grande medida o desempenho de rede. Este caminho de alto desempenho ignora o anfitrião a partir de datapath, reduzindo a latência, distorção e a utilização da CPU, para utilização com as mais exigentes cargas de trabalho de rede sobre os tipos VM suportados. A imagem seguinte mostra a comunicação entre duas VMs com e sem redes aceleradas:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem redes aceleradas, todo o tráfego de rede e para a VM deve percorrer o anfitrião e o comutador virtual. O comutador virtual fornece todos os imposição de políticas, tais como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços de rede virtualizado para o tráfego de rede. Para obter mais informações sobre comutadores virtuais, leia os [Virtualização de rede do Hyper-V e o comutador virtual](https://technet.microsoft.com/library/jj945275.aspx) artigo.

Com redes aceleradas, o tráfego de rede chega à interface de rede da máquina virtual (NIC) e, em seguida, é reencaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica-se agora são descarregadas e aplicadas no hardware. Aplicando a diretiva de hardware, permite que o NIC para reencaminhar o tráfego de rede diretamente à VM, ignorando o anfitrião e o comutador virtual, enquanto mantém a diretiva de todos os que ele aplicado no anfitrião.

Os benefícios do funcionamento em rede acelerado só se aplicam à VM que está ativada no. Para obter melhores resultados, é ideal para ativar esta funcionalidade em, pelo menos, duas VMs ligadas à mesma rede virtual do Azure (VNet). Durante a comunicação entre VNets ou ligação no local, esta funcionalidade tem um impacto mínimo para latência geral.

## <a name="benefits"></a>Benefícios
* **Menor latência / superior pacotes por segundo (pps):** Remover o comutador virtual do datapath remove o tempo gasto de pacotes no anfitrião para processamento de diretiva e aumenta o número de pacotes que podem ser processadas dentro da VM.
* **Menor interferência:** Processamento de comutador virtual depende da quantidade de política que tem de ser aplicadas e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política para o hardware remove essa variabilidade fornecendo pacotes diretamente à VM, remover o anfitrião a comunicação de VM e todas as interrupções de software e Alternâncias de contexto.
* **Diminuição da utilização da CPU:** Ignorar o comutador virtual no anfitrião leva para menos de utilização da CPU para processar o tráfego de rede.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições seguintes são suportadas prontos a utilizar da galeria do Azure: 
* **Ubuntu 16.04+** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "esticar" com o kernel de backports**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Limitações e restrições

### <a name="supported-vm-instances"></a>Instâncias VM suportadas
Funcionamento em rede acelerado é suportado em fins mais gerais e tamanhos de instâncias com otimização de computação com vCPUs 2 ou mais.  Esta série suportado é: D/DSv2 e F/Fs

Redes aceleradas é suportada em instâncias que suportam o hyperthreading, nas instâncias VM com vCPUs 4 ou mais. Série suportado é: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre as instâncias de VM, consulte [tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões
Disponível em todas as regiões públicas do Azure, bem como Clouds de administração pública do Azure.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Ativar o Accelerated Networking numa VM em execução
Um tamanho de VM suportado sem aceleração de rede ativada só pode ter a funcionalidade ativada quando está parado e desalocado.  
### <a name="deployment-through-azure-resource-manager"></a>Implementação através do Azure Resource Manager
Não é possível implementar máquinas virtuais (clássico) com redes aceleradas.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Criar uma VM do Linux com redes aceleradas do Azure
## <a name="portal-creation"></a>Criação do portal
Embora este artigo fornece passos para criar uma máquina virtual com redes aceleradas com a CLI do Azure, também pode [criar uma máquina virtual com o funcionamento em rede acelerado com o portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, no **criar uma máquina virtual** painel, escolha a **Networking** separador.  Neste separador, é uma opção para **Accelerated networking**.  Se tiver optado por um [sistema operativo suportado](#supported-operating-systems) e [tamanho da VM](#supported-vm-instances), esta opção será preenchida automaticamente como "On".  Caso contrário, irá preencher a opção "Desativado" para redes aceleradas e dar ao utilizador um motivo por que ele não é possível ativar.   

* *Nota:* Apenas os sistemas operativos suportados, pode ser ativados através do portal.  Se estiver a utilizar uma imagem personalizada e sua imagem oferece suporte a redes aceleradas, crie a VM com a CLI ou Powershell. 

Depois da máquina virtual é criada, pode confirmar Accelerated Networking está ativada ao seguir as instruções no [confirmar que o funcionamento em rede acelerado é ativado](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Criação de CLI
### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Instalar a versão mais recente [CLI do Azure](/cli/azure/install-azure-cli) e para iniciar sessão no Azure através da conta [início de sessão az](/cli/azure/reference-index). Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *myNic*, e *myVm*.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *centralus* localização:

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecione uma região de Linux suportada listada na [Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Crie uma rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual denominada *myVnet* com uma sub-rede:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Crie um grupo de segurança de rede com [nsg de rede de az criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

O grupo de segurança de rede contém várias regras de predefinidas, um dos quais desativa todos os acessos de entrada da Internet. Abrir uma porta para permitir o acesso SSH para a máquina virtual com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar uma interface de rede com redes aceleradas

Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). Um endereço IP público não é necessário se não planeja para aceder à máquina virtual a partir da Internet, mas para concluir os passos neste artigo, é necessário.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Criar uma interface de rede com [nic da rede de az criar](/cli/azure/network/nic) com aceleração de rede ativada. O exemplo seguinte cria uma interface de rede com o nome *myNic* no *mySubnet* sub-rede do *myVnet* rede virtual e associa o  *myNetworkSecurityGroup* grupo de segurança de rede para a interface de rede:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Criar uma VM e anexar o NIC
Ao criar a VM, especifique o NIC que criou com `--nics`. Selecione um tamanho e a distribuição listados na [Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome *myVM* com a imagem UbuntuLTS e um tamanho que suporte redes aceleradas (*Standard_DS4_v2*):

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

Para obter uma lista de todos os tamanhos de VM e características, consulte [tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Assim que a VM é criada, é devolvido o resultado semelhante a seguinte saída de exemplo. Anote o **publicIpAddress**. Este endereço é utilizado para aceder à VM nos passos subsequentes.

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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirme que o funcionamento em rede acelerado está ativado

Utilize o seguinte comando para criar uma sessão SSH com a VM. Substitua `<your-public-ip-address>` com o endereço IP público atribuído ao virtual machine que criou e substitua *azureuser* se tiver utilizado um valor diferente para `--admin-username` quando criou a VM.

```bash
ssh azureuser@<your-public-ip-address>
```

A partir da shell de Bash, introduza `uname -r` e confirme que a versão de kernel é uma das seguintes versões, ou superior:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Confirme que o dispositivo de Mellanox VF é exposto à VM com o `lspci` comando. O resultado retornado é semelhante à seguinte saída:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Verifique para a atividade do VF (função virtual) com o `ethtool -S eth0 | grep vf_` comando. Se receber de saída semelhante ao seguinte exemplo de saída, funcionamento em rede acelerado está ativado e a funcionar.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Funcionamento em rede acelerado está agora ativado para a sua VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Ativar redes aceleradas em VMs existentes
Se tiver criado uma VM sem redes aceleradas, é possível ativar esta funcionalidade numa VM existente.  A VM tem de suportar redes aceleradas cumprindo os seguintes pré-requisitos também são descritos acima:

* A VM tem de ser um tamanho suportado para redes aceleradas
* A VM tem de ser uma imagem da galeria do Azure suportados (e a versão de kernel de Linux)
* Todas as VMs num conjunto de disponibilidade ou VMSS tem de ser parada/desalocada antes de ativar redes aceleradas em qualquer NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>Conjunto de VMs individuais e de VMs de disponibilidade
Primeiro parar/desalocar a VM ou, se um conjunto de disponibilidade, todas as VMs no conjunto de:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Importante,. tenha em atenção que se a VM foi criada individualmente, sem um conjunto de disponibilidade, só precisa parar/desalocar a VM individual para ativar a redes aceleradas.  Se a VM foi criada com um conjunto de disponibilidade, todas as VMs contidas no conjunto de disponibilidade terá de ser parada/desalocada antes de ativar redes aceleradas em qualquer uma das NICs. 

Depois de parado, ative a Accelerated Networking na NIC da VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Reinicie a VM ou, se num conjunto de disponibilidade, todas as VMs no conjunto e confirmar que o Accelerated Networking está ativado: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Em primeiro lugar, pare as VMs:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Assim que as VMs são paradas, atualize a propriedade redes aceleradas sob a interface de rede:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

. Tenha em atenção que um VMSS tem as atualizações VM que se aplicam a atualizações com três configurações diferentes, manuais, automática e sem interrupção.  Nestas instruções a política está definida como automático, para que a VMSS captará as alterações imediatamente após o reinício.  Defini-la como automático, para que as alterações são captadas imediatamente: 

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

Uma vez que reinicie, espere pelas atualizações concluir e depois de concluir, a VF aparecerá dentro da VM.  (Certifique-se que estiver a utilizar um tamanho VM e de SO suportado.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar VMs existentes com redes aceleradas

As VMs com redes aceleradas ativada apenas podem ser redimensionadas para VMs que suportam redes aceleradas.  

Uma VM com Accelerated Networking ativado não pode ser redimensionada para uma instância VM que não suporta redes aceleradas usando a operação de redimensionamento.  Em vez disso, para redimensionar um estas VMS: 

* Parar/Desalocação da VM ou em caso de um conjunto de disponibilidade/VMSS, parar/desalocar todas as VMs no conjunto/VMSS.
* Funcionamento em rede acelerado tem de ser desativado na NIC da VM ou se estiver numa disponibilidade conjunto/VMSS, todas as VMs no conjunto/VMSS.
* Assim que o Accelerated Networking está desativada, o conjunto de disponibilidade/da VM/VMSS podem ser movidos para um novo tamanho que não suporta redes aceleradas e reiniciado.  

