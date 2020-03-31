---
title: Adicione interfaces de rede para ou remover de VMs Azure
description: Aprenda a adicionar interfaces de rede ou remover interfaces de rede de máquinas virtuais.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060312"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Add network interfaces to or remove network interfaces from virtual machines (Adicionar interfaces de rede ou remover interfaces de rede de máquinas virtuais)

Saiba como adicionar uma interface de rede existente quando criar uma máquina virtual Azure (VM). Aprenda também a adicionar ou remover interfaces de rede de um VM existente no estado de paragem (deallocated). Uma interface de rede permite que um Azure VM comunique com recursos de internet, Azure e no local. Um VM tem uma ou mais interfaces de rede. 

Se precisar de adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [gerir os endereços IP](virtual-network-network-interface-addresses.md)da interface da rede . Para criar, alterar ou eliminar interfaces de rede, ver [Gerir interfaces de rede](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma, instale uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Complete uma destas tarefas antes de iniciar o restante deste artigo:

- **Utilizadores**do portal : Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

- **Utilizadores powerShell**: Ou executa os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/powershell)ou executa powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. No separador de navegador Azure Cloud Shell, encontre a lista de abandono ambiental **Select** e escolha **powerShell** se ainda não estiver selecionado.

    Se estiver a executar o PowerShell localmente, utilize a versão 1.0.0 do módulo PowerShell Do Mato Azure. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` para criar uma ligação com o Azure.

- Utilizadores da **interface da linha de comando Azure (CLI):** Ou executa os comandos na Membrana Nuvem [Azure,](https://shell.azure.com/bash)ou executa o CLI a partir do seu computador. Utilize a versão Azure CLI 2.0.26 ou mais tarde se estiver a executar o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Execute `az login` para criar uma ligação com o Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Adicione as interfaces de rede existentes a um novo VM

Quando cria uma máquina virtual através do portal, o portal cria uma interface de rede com definições predefinidas e liga a interface de rede ao VM para si. Não é possível utilizar o portal para adicionar interfaces de rede existentes a um novo VM, ou para criar um VM com múltiplas interfaces de rede. Pode fazer as duas coisas utilizando o CLI ou o PowerShell. Certifique-se de se familiarizar com os [constrangimentos.](#constraints) Se criar um VM com múltiplas interfaces de rede, também deve configurar o sistema operativo para os utilizar corretamente depois de criar o VM. Saiba como configurar [o Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos

Antes de criar o VM, [crie uma interface de rede.](virtual-network-network-interface.md#create-a-network-interface)

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Adicione uma interface de rede a um VM existente

Para adicionar uma interface de rede à sua máquina virtual:

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar uma máquina virtual existente. Procure e selecione **máquinas Virtuais**.

2. Selecione o nome do seu VM. O VM deve suportar o número de interfaces de rede que pretende adicionar. Para saber quantas interfaces de rede cada tamanho VM suporta, veja os tamanhos em Azure para [VMs Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [VMs windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Na barra de comando VM, selecione **Stop**, e **depois OK** na caixa de diálogo de confirmação. Em seguida, aguarde até que o **estado** do VM mude para **parar (deallocated)**.

4. A partir da barra de menuS VM, escolha a interface de > **rede anexada**em **rede**. Em seguida, na interface de **rede existente,** escolha a interface de rede que deseja anexar e selecione **OK**.

    >[!NOTE]
    >A interface de rede selecionada não pode ter uma rede acelerada ativada, não pode ter um endereço IPv6 atribuído a ele, e deve existir na mesma rede virtual com a interface de rede atualmente anexada ao VM.

    Se não tiver uma interface de rede existente, primeiro deve criar uma. Para isso, selecione **Criar interface de rede**. Para saber mais sobre como criar uma interface de rede, consulte [Criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface). Para saber mais sobre constrangimentos adicionais ao adicionar interfaces de rede a máquinas virtuais, consulte [Constrangimentos](#constraints).

5. A partir da barra de menuS VM, escolha **Visão Geral** > **Comece** a reiniciar a máquina virtual.

Agora pode configurar o sistema operativo VM para utilizar corretamente várias interfaces de rede. Saiba como configurar [o Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm nic adicionar](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (referência); [passos detalhados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência); [passos detalhados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Ver interfaces de rede de uma VM

Pode visualizar as interfaces de rede atualmente anexadas a um VM para saber sobre a configuração de cada interface de rede e os endereços IP atribuídos a cada interface de rede. 

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar uma máquina virtual existente. Procure e selecione **máquinas Virtuais**.

    >[!NOTE]
    >Inscreva-se na utilização de uma conta que é atribuída a função de Proprietário, Colaborador ou Colaborador de Rede para a sua subscrição. Para saber mais sobre como atribuir funções às contas, consulte [funções incorporadas para o controlo de acesso baseado em papéis azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Selecione o nome do VM para o qual pretende visualizar as interfaces de rede anexadas.

3. Na barra de menuS VM, selecione **Networking**.

Para saber sobre as definições de interface de rede e como alterá-las, consulte [gerir as interfaces de rede](virtual-network-network-interface.md). Para saber como adicionar, alterar ou remover endereços IP atribuídos a uma interface de rede, consulte [Gerir os endereços IP](virtual-network-network-interface-addresses.md)da interface da rede .

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm lista nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Remova uma interface de rede de um VM

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar uma máquina virtual existente. Procure e selecione **máquinas Virtuais**.

2. Selecione o nome do VM para o qual pretende visualizar as interfaces de rede anexadas.

3. Na barra de ferramentas VM, escolha **Stop**.

4. Aguarde até que o **Estado** do VM mude para **parar (deallocated)**.

5. Na barra de menuS VM, escolha a interface de**rede Detach**em **rede** > .

6. Na caixa de diálogo de interface de **rede Detach,** selecione a interface de rede que pretende desprender. Em seguida, selecione **OK**.

    >[!NOTE]
    >Se apenas uma interface de rede estiver listada, não pode desmontá-la, porque uma máquina virtual deve ter sempre pelo menos uma interface de rede ligada à sua.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm nic remover](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (referência); [passos detalhados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remover-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência); [passos detalhados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Restrições

- Um VM deve ter pelo menos uma interface de rede ligada a ele.

- Um VM só pode ter tantas interfaces de rede anexadas a ele como o tamanho VM suporta. Para saber mais sobre quantas interfaces de rede cada tamanho VM suporta, consulte os tamanhos em Azure para [VMs Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [VMs Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos os tamanhos suportam pelo menos duas interfaces de rede.

- As interfaces de rede que adiciona a um VM não podem ser atualmente anexadas a outro VM. Para saber mais sobre como criar interfaces de rede, consulte [Criar uma interface](virtual-network-network-interface.md#create-a-network-interface)de rede .

- No passado, poderia adicionar interfaces de rede apenas a VMs que suportavam várias interfaces de rede e foram criados com pelo menos duas interfaces de rede. Não foi conseguido adicionar uma interface de rede a um VM que foi criado com uma interface de rede, mesmo que o tamanho VM suportasse mais do que uma interface de rede. Inversamente, só se podia remover interfaces de rede de um VM com pelo menos três interfaces de rede, porque os VMs criados com pelo menos duas interfaces de rede tinham sempre de ter pelo menos duas interfaces de rede. Estes constrangimentos já não se aplicam. Agora pode criar um VM com qualquer número de interfaces de rede (até ao número suportado pelo tamanho VM).

- Por predefinição, a primeira interface de rede anexa a um VM é a interface de rede *primária.* Todas as outras interfaces de rede no VM são interfaces de rede *secundárias.*

- Pode controlar a interface de rede para a qual envia tráfego de saída. No entanto, um VM por padrão envia todo o tráfego de saída para o endereço IP que é atribuído à configuração ip primária da interface de rede primária.

- No passado, todos os VMs dentro do mesmo conjunto de disponibilidade eram obrigados a ter uma única, ou múltipla, interfaces de rede. VMs com qualquer número de interfaces de rede podem agora existir no mesmo conjunto de disponibilidade, até ao número suportado pelo tamanho VM. Só é possível adicionar um VM a um conjunto de disponibilidade quando é criado. Para saber mais sobre conjuntos de disponibilidade, consulte [Gerir a disponibilidade de VMs em Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Pode ligar interfaces de rede no mesmo VM a diferentes subredes dentro de uma rede virtual. No entanto, as interfaces de rede devem estar todas ligadas à mesma rede virtual.

- Pode adicionar qualquer endereço IP para qualquer configuração IP de qualquer interface de rede primária ou secundária a um pool de back-end Azure Load Balancer. No passado, apenas o endereço IP primário para a interface de rede primária poderia ser adicionado a um pool de back-end. Para saber mais sobre endereços ip e configurações, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

- A eliminação de um VM não elimina as interfaces de rede que lhe estão anexadas. Quando elimina um VM, as interfaces de rede são separadas do VM. Pode adicionar essas interfaces de rede a diferentes VMs ou eliminá-las.

- Tal como acontece com o IPv6, não é possível anexar uma interface de rede com uma rede acelerada ativada a um VM depois de a criar. Além disso, para tirar partido da rede acelerada, deve também completar os passos dentro do sistema operativo VM. Saiba mais sobre a rede acelerada e outros constrangimentos ao utilizá-lo, para máquinas virtuais [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux.](create-vm-accelerated-networking-cli.md)

## <a name="next-steps"></a>Passos seguintes

Para criar um VM com múltiplas interfaces de rede ou endereços IP, consulte:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com vários NICs|[CLI,](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Crie um único NIC VM com vários endereços IPv4|[CLI,](virtual-network-multiple-ip-addresses-cli.md) [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Crie um único NIC VM com um endereço IPv6 privado (atrás de um Equilíbrio de Carga Azure)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell,](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Modelo de Gestor de [Recursos Azure](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
