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
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: a55bf014a2da10069e4e6a5f6f4eb4b8cd9ff205
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383189"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Adicione interfaces de rede para ou remover interfaces de rede de máquinas virtuais

Saiba como adicionar uma interface de rede existente quando criar uma máquina virtual Azure (VM), ou adicionar ou remover interfaces de rede de um VM existente no estado de paragem (deallocated). Uma interface de rede permite que uma máquina virtual Azure comunique com recursos da internet, Azure e no local. Um VM pode ter uma ou mais interfaces de rede. 

Se precisar de adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [gerir os endereços IP](virtual-network-network-interface-addresses.md)da interface da rede . Se precisar de criar, alterar ou eliminar interfaces de rede, consulte [Gerir interfaces](virtual-network-network-interface.md)de rede .

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.come faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.26 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Adicione as interfaces de rede existentes a um novo VM

Quando cria uma máquina virtual através do portal, o portal cria uma interface de rede com definições predefinidas e liga-a ao VM para si. Não é possível adicionar interfaces de rede existentes a um novo VM, nem criar um VM com múltiplas interfaces de rede, utilizando o portal Azure. Pode fazer ambos utilizando o CLI ou o PowerShell, mas certifique-se de se familiarizar com os [constrangimentos](#constraints). Se criar um VM com múltiplas interfaces de rede, também deve configurar o sistema operativo para os utilizar corretamente depois de criar o VM. Saiba como configurar [o Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos

Antes de criar o VM, crie uma interface de rede utilizando os passos em [Criar uma interface](virtual-network-network-interface.md#create-a-network-interface)de rede .

|Ferramenta|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicione uma interface de rede a um VM existente

1. Inicie sessão no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, escreva o nome do VM ao qual pretende adicionar a interface de rede, ou navegue pelo VM selecionando **Todos os serviços**, e depois **máquinas Virtuais.** Depois de encontrar o VM, selecione-o. O VM deve suportar o número de interfaces de rede que pretende adicionar. Para saber quantas interfaces de rede cada tamanho VM suporta, consulte [Sizes para máquinas virtuais Linux em Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Sizes para máquinas virtuais Windows em Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Selecione **visão geral,** em **DEFINIÇÕES**. Selecione **Stop**e, em seguida, aguarde até que o **estado** do VM mude para **parar (deallocated)** .
4. Selecione **Networking**, em **DEFINIÇÕES**.
5. Selecione **a interface de rede anexar**. A partir da lista de interfaces de rede que não estão atualmente anexadas a outro VM, selecione a que pretende anexar.

   >[!NOTE]
   >A interface de rede selecionada não pode ter ativado a rede acelerada, não pode ter um endereço IPv6 atribuído a ela, e deve existir na mesma rede virtual que a que contém a interface de rede atualmente anexada ao VM.

   Se não tiver uma interface de rede existente, primeiro deve criar uma. Para isso, selecione **Criar interface de rede**. Para saber mais sobre como criar uma interface de rede, consulte [Criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface). Para saber mais sobre constrangimentos adicionais ao adicionar interfaces de rede a máquinas virtuais, consulte [Constrangimentos](#constraints).

6. Selecione **OK**.
7. Selecione **visão geral**, em **DEFINIÇÕES,** **e,** em seguida, comece a ligar a máquina virtual.
8. Configure o sistema operativo VM para utilizar corretamente várias interfaces de rede. Saiba como configurar [o Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos
|Ferramenta|Comando|
|---|---|
|CLI|[az vm nic adicionar](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [passos detalhados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [passos detalhados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Ver interfaces de rede para um VM

Pode visualizar as interfaces de rede atualmente anexadas a um VM para saber sobre a configuração de cada interface de rede e os endereços IP atribuídos a cada interface de rede. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é atribuída ao papel de Proprietário, Colaborador ou Colaborador de Rede para a sua subscrição. Para saber mais sobre como atribuir funções às contas, consulte [funções incorporadas para o controlo de acesso baseado em papéis azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém os recursos de **pesquisa** de texto no topo do portal Azure, digite **máquinas virtuais.** Quando **as máquinas virtuais** aparecerem nos resultados da pesquisa, selecione-as.
3. Selecione o nome do VM para o qual pretende visualizar interfaces de rede.
4. Na secção **DEFINIÇÕES** para o VM selecionado, selecione **Networking**. Para saber sobre as definições de interface de rede e como alterá-las, consulte [gerir as interfaces de rede](virtual-network-network-interface.md). Para saber como adicionar, alterar ou remover endereços IP atribuídos a uma interface de rede, consulte [Gerir os endereços IP](virtual-network-network-interface-addresses.md)da interface da rede .

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Remova uma interface de rede de um VM

1. Inicie sessão no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, procure o nome do VM que pretende remover (desprender) a interface de rede, ou navegue para o VM selecionando **Todos os serviços**, e depois **máquinas Virtuais.** Depois de encontrar o VM, selecione-o.
3. Selecione **visão geral,** em **DEFINIÇÕES,** e depois **pare**. Aguarde até que o **Estado** do VM mude para **parar (deallocated)** .
4. Selecione **Networking**, em **DEFINIÇÕES**.
5. Selecione **interface de rede Detach**. A partir da lista de interfaces de rede atualmente anexadas à máquina virtual, selecione a interface de rede que pretende desprender.

   >[!NOTE]
   >Se apenas uma interface de rede estiver listada, não pode desmontá-la, porque uma máquina virtual deve ter sempre pelo menos uma interface de rede ligada à sua.
6. Selecione **OK**.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm nic remover](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [passos detalhados](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remover AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [passos detalhados](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Restrições

- Um VM deve ter pelo menos uma interface de rede ligada a ele.
- Um VM só pode ter tantas interfaces de rede anexadas a ele como o tamanho VM suporta. Para saber mais sobre quantas interfaces de rede cada tamanho VM suporta, consulte [Sizes para máquinas virtuais Linux em Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Sizes para máquinas virtuais Windows em Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos os tamanhos suportam pelo menos duas interfaces de rede.
- As interfaces de rede que adiciona a um VM não podem ser atualmente anexadas a outro VM. Para saber mais sobre como criar interfaces de rede, consulte [Criar uma interface](virtual-network-network-interface.md#create-a-network-interface)de rede .
- No passado, as interfaces de rede só podiam ser adicionadas a VMs que suportavam várias interfaces de rede e foram criadas com pelo menos duas interfaces de rede. Não foi possível adicionar uma interface de rede a um VM que foi criado com uma interface de rede, mesmo que o tamanho VM suportasse várias interfaces de rede. Inversamente, só se podia remover interfaces de rede de um VM com pelo menos três interfaces de rede, porque os VMs criados com pelo menos duas interfaces de rede tinham sempre de ter pelo menos duas interfaces de rede. Nenhum destes constrangimentos se aplica mais. Agora pode criar um VM com qualquer número de interfaces de rede (até ao número suportado pelo tamanho VM).
- Por predefinição, a primeira interface de rede anexa a um VM é definida como a interface de rede *primária.* Todas as outras interfaces de rede no VM são interfaces de rede *secundárias.*
- Embora possa controlar a interface de rede que enviou para, por defeito, todo o tráfego de saída do VM é enviado o endereço IP atribuído à configuração ip primária da interface de rede primária.
- No passado, todos os VMs dentro do mesmo conjunto de disponibilidade eram obrigados a ter uma única, ou múltipla, interfaces de rede. VMs com qualquer número de interfaces de rede podem agora existir no mesmo conjunto de disponibilidade, até ao número suportado pelo tamanho VM. Só é possível adicionar um VM a um conjunto de disponibilidade quando é criado. Para saber mais sobre conjuntos de disponibilidade, consulte [Gerir a disponibilidade de VMs em Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Embora as interfaces de rede no mesmo VM possam ser ligadas a diferentes subredes dentro de uma rede virtual, as interfaces de rede devem estar todas ligadas à mesma rede virtual.
- Pode adicionar qualquer endereço IP para qualquer configuração IP de qualquer interface de rede primária ou secundária a um pool de back-end Azure Load Balancer. No passado, apenas o endereço IP primário para a interface de rede primária poderia ser adicionado a um pool de back-end. Para saber mais sobre endereços ip e configurações, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).
- A eliminação de um VM não elimina as interfaces de rede que lhe estão anexadas. Quando elimina um VM, as interfaces de rede são separadas do VM. Pode adicionar as interfaces de rede a diferentes VMs ou eliminá-las.
- Tal como acontece com o IPv6, não é possível anexar uma interface de rede com uma rede acelerada ativada a um VM depois de a criar. Além disso, para tirar partido da rede acelerada, deve também completar os passos dentro do sistema operativo VM. Saiba mais sobre a rede acelerada e outros constrangimentos ao utilizá-lo, para máquinas virtuais [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux.](create-vm-accelerated-networking-cli.md)

## <a name="next-steps"></a>Passos seguintes
Para criar um VM com múltiplas interfaces de rede ou endereços IP, consulte os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com vários NICs|[CLI,](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Crie um único NIC VM com vários endereços IPv4|[CLI,](virtual-network-multiple-ip-addresses-cli.md) [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Crie um único NIC VM com um endereço IPv6 privado (atrás de um Equilíbrio de Carga Azure)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell,](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Modelo de Gestor de [Recursos Azure](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
