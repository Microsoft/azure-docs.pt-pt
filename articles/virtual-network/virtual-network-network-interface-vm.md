---
title: Interfaces de rede para adicionar ou remover de máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba como interfaces de rede para adicionar ou remover interfaces de rede de máquinas virtuais.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: a6371746d156fb0be2d45ac94c898652a3147a6b
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887493"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Interfaces de rede para adicionar ou remover interfaces de rede de máquinas virtuais

Saiba como adicionar uma interface de rede existente quando cria uma máquina virtual do Azure (VM), ou adicionar ou remover interfaces de rede a partir de uma VM existente no estado parado (desalocada). Uma interface de rede permite que uma máquina virtual do Azure comunicar com a internet, o Azure e recursos no local. Uma VM pode ter uma ou mais interfaces de rede. 

Se precisa para adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [gerir os endereços IP de interface de rede](virtual-network-network-interface-addresses.md). Se precisar de criar, alterar, ou elimine interfaces de rede, consulte [gerir as interfaces de rede](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.26 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Adicionar interfaces de rede existente para uma nova VM

Quando cria uma máquina virtual através do portal, o portal cria uma interface de rede com configurações padrão e anexa-o para a VM para. Não é possível adicionar interfaces de rede existente para uma nova VM, nem para criar uma VM com várias interfaces de rede, com o portal do Azure. Pode fazer ambos, com a CLI ou o PowerShell, mas certifique-se de que se familiarize-se com o [restrições](#constraints). Se criar uma VM com várias interfaces de rede, também tem de configurar o sistema operativo para utilizá-los corretamente depois de criar a VM. Saiba como configurar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos

Antes de criar a VM, criar uma interface de rede, utilizando os passos em [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface).

|Ferramenta|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicionar uma interface de rede a uma VM existente

1. Inicie sessão no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, escreva o nome da VM à qual pretende adicionar a interface de rede ou navegue para a VM selecionando **todos os serviços**e, em seguida **máquinas virtuais**. Depois de encontrar a VM, selecione-o. A VM tem de suportar o número de interfaces de rede que pretende adicionar. Para obter informações sobre cada tamanho de VM do interfaces de rede quantos suporta, consulte [tamanhos de máquinas de virtuais do Linux no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [máquinas de virtuais de tamanhos para Windows no Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Selecione **descrição geral**, em **definições**. Selecione **parar**e, em seguida, aguarde até a **estado** da VM é alterado para **parada (desalocada)**.
4. Selecione **Networking**, em **definições**.
5. Selecione **interface de rede Attach**. Na lista de interfaces de rede que não estão atualmente anexadas à outra VM, selecione aquela que pretende anexar.

   >[!NOTE]
   >A interface de rede que selecionar não pode ter accelerated networking ativado, não pode ter um endereço de IPv6 atribuído a ele e têm de existir na mesma rede virtual como aquela que contém a interface de rede atualmente ligada à VM.

   Se não tiver uma interface de rede existente, tem primeiro de criar uma. Para tal, selecione **interface de rede de criar**. Para saber mais sobre como criar uma interface de rede, veja [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface). Para saber mais sobre as restrições adicionais ao adicionar interfaces de rede para máquinas virtuais, veja [restrições](#constraints).

6. Selecione **OK**.
7. Selecione **descrição geral**, em **definições**e, em seguida **iniciar** para iniciar a máquina virtual.
8. Configure o sistema de operacional VM para utilizar várias interfaces de rede corretamente. Saiba como configurar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos
|Ferramenta|Comando|
|---|---|
|CLI|[Adicionar AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [detalhadas passos](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Adicionar-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [detalhadas passos](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Ver interfaces de rede para uma VM

Pode ver as interfaces de rede atualmente anexadas a uma VM para saber mais sobre a configuração de cada interface de rede e os endereços IP atribuídos a cada interface de rede. 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com uma conta que é atribuída a função de proprietário, Contribuidor ou contribuinte de rede para a sua subscrição. Para saber mais sobre como atribuir funções para as contas, veja [funções incorporadas para controlo de acesso baseado em funções do Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém o texto **recursos de pesquisa** na parte superior do portal do Azure, escreva **máquinas virtuais**. Quando **máquinas virtuais** aparecer nos resultados da pesquisa, selecione.
3. Selecione o nome da VM para o qual pretende ver interfaces de rede.
4. Na **definições** secção para a VM que selecionou, e selecione **Networking**. Para saber mais sobre as definições de interface de rede e como alterá-las, consulte [gerir as interfaces de rede](virtual-network-network-interface.md). Para saber mais sobre como adicionar, alterar ou remover endereços IP atribuídos a uma interface de rede, veja [gerir os endereços IP de interface de rede](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Remover uma interface de rede de uma VM

1. Inicie sessão no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, procure o nome da VM que pretende remover (desligar) a interface de rede da ou navegue para a VM selecionando **todos os serviços**e, em seguida **máquinas virtuais**. Depois de encontrar a VM, selecione-o.
3. Selecione **descrição geral**, em **definições**e, em seguida **parar**. Aguarde até que o **Status** da VM é alterado para **parada (desalocada)**.
4. Selecione **Networking**, em **definições**.
5. Selecione **interface de rede de anulação de exposições**. Na lista de interfaces de rede atualmente anexadas à máquina virtual, selecione a interface de rede que pretende desligar.

   >[!NOTE]
   >Se apenas uma interface de rede estiver listada, não pode desligá-lo, uma vez que uma máquina virtual sempre tem de ter, pelo menos, uma interface de rede ligada ao mesmo.
6. Selecione **OK**.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[remover AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [detalhadas passos](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [detalhadas passos](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Restrições

- Uma VM tem de ter, pelo menos, uma interface de rede ligada ao mesmo.
- Uma VM só pode ter como muitos anexados a ele como o suporte de tamanho VM de interfaces de rede. Para saber mais sobre o número de interfaces de rede oferece suporte a cada tamanho de VM, veja [tamanhos de máquinas de virtuais do Linux no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [máquinas de virtuais de tamanhos para Windows no Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos os tamanhos de suportam, pelo menos, duas interfaces de rede.
- As interfaces de rede que adicionar a uma VM atualmente não podem ser ligadas a outra VM. Para saber mais sobre como criar interfaces de rede, veja [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface).
- No passado, interfaces de rede só podem ser adicionadas para VMs que suporte várias interfaces de rede e foram criadas com, pelo menos, duas interfaces de rede. Não foi possível adicionar uma interface de rede a uma VM que foi criada com uma interface de rede, mesmo que o tamanho VM suportadas várias interfaces de rede. Por outro lado, só foi possível remover interfaces de rede a partir de uma VM com, pelo menos, três interfaces de rede, porque as VMs criadas com a rede, pelo menos, duas interfaces sempre precisavam ter, pelo menos, duas interfaces de rede. Nenhuma destas restrições aplicam-se mais. Agora, pode criar uma VM com qualquer número de interfaces de rede (até o número suportado pelo tamanho da VM).
- Por predefinição, a primeira interface de rede ligada a uma VM é definida como o *primário* interface de rede. Todas as outras interfaces de rede na VM são *secundário* interfaces de rede.
- Embora pode controlar qual interface de rede enviado o tráfego de saída para, por predefinição, todo o tráfego de saída da VM é enviado o endereço IP atribuído à configuração de IP principal da interface de rede principal.
- No passado, todas as VMs no mesmo conjunto de disponibilidade foram necessárias para ter interfaces de rede único ou vários. VMs com qualquer número de interfaces de rede podem existir agora no mesmo conjunto de disponibilidade, até ao número suportado pelo tamanho da VM. Só pode adicionar uma VM para um conjunto quando é criado de disponibilidade. Para saber mais sobre os conjuntos de disponibilidade, veja [gerir a disponibilidade de VMs no Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Enquanto a interfaces de rede na mesma VM podem ser ligadas a sub-redes diferentes dentro de uma rede virtual, as interfaces de rede devem todas estar ligadas à mesma rede virtual.
- Pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer interface de rede primária ou secundária para um conjunto de back-end de Balanceador de carga do Azure. No passado, apenas o endereço IP primário para a interface de rede primária foi possível adicionar a um conjunto de back-end. Para saber mais sobre endereços IP e configurações, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).
- Eliminar uma VM não elimina as interfaces de rede que estão ligadas ao mesmo. Quando elimina uma VM, as interfaces de rede estiver desligadas da VM. Pode adicionar as interfaces de rede para diferentes VMs ou eliminá-los.
- Se uma interface de rede tiver um endereço IPv6 privado atribuído ao mesmo, tem de adicionar (anexar) para uma VM ao criar a VM. Não é possível adicionar uma interface de rede com um endereço de IPv6 atribuído a uma VM depois de criar a VM. Se adicionar uma interface de rede com um endereço IPv6 privado atribuído ao criar uma máquina virtual, apenas pode adicionar essa interface de rede para a máquina virtual, independentemente de quantas interfaces de rede suporta o tamanho da VM. Ver [gerir os endereços IP de interface de rede](virtual-network-network-interface-addresses.md) para saber mais sobre como atribuir endereços IP a interfaces de rede.
- Tal como acontece com o IPv6, é possível anexar uma interface de rede com redes aceleradas ativada para uma VM depois de o criar. Além disso, para tirar partido do funcionamento em rede acelerado, também tem de concluir os passos no sistema de operativo VM. Saiba mais sobre o funcionamento em rede acelerado e outras restrições quando utilizá-lo, para [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) máquinas virtuais.

## <a name="next-steps"></a>Passos Seguintes
Para criar uma VM com várias interfaces de rede ou endereços IP, veja os artigos seguintes:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com vários NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM de NIC único com vários endereços de IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM de NIC único com um endereço IPv6 privado (por trás de um balanceador de carga do Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
