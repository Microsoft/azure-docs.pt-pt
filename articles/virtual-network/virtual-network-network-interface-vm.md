---
title: Adicionar ou remover interfaces de rede de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como adicionar interfaces de rede ou remover adaptadores de rede de máquinas virtuais.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
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
ms.openlocfilehash: 24f4b7435c2736527d033aa5ca7a65ad35a3a705
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022187"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Adicionar interfaces de rede ou remover adaptadores de rede de máquinas virtuais

Saiba como adicionar uma interface de rede existente ao criar uma VM (máquina virtual) do Azure ou para adicionar ou remover interfaces de rede de uma VM existente no estado parado (desalocado). Uma interface de rede permite que uma máquina virtual do Azure se comunique com a Internet, com o Azure e com recursos locais. Uma VM pode ter uma ou mais interfaces de rede. 

Se você precisar adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [gerenciar endereços IP de adaptador de rede](virtual-network-network-interface-addresses.md). Se você precisar criar, alterar ou excluir interfaces de rede, consulte [gerenciar adaptadores de rede](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.26 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Adicionar adaptadores de rede existentes a uma nova VM

Quando você cria uma máquina virtual por meio do portal, o portal cria uma interface de rede com as configurações padrão e a anexa à VM para você. Você não pode adicionar adaptadores de rede existentes a uma nova VM, nem criar uma VM com várias interfaces de rede, usando o portal do Azure. Você pode fazer ambos usando a CLI ou o PowerShell, mas não se esqueça de se familiarizar com as [restrições](#constraints). Se você criar uma VM com várias interfaces de rede, também deverá configurar o sistema operacional para usá-las corretamente depois de criar a VM. Saiba como configurar o [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos

Antes de criar a VM, crie uma interface de rede usando as etapas em [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface).

|Ferramenta|Comando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicionar uma interface de rede a uma VM existente

1. Inicie sessão no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, digite o nome da VM à qual você deseja adicionar a interface de rede ou procure a VM selecionando **todos os serviços**e, em seguida, **máquinas virtuais**. Depois de encontrar a VM, selecione-a. A VM deve dar suporte ao número de interfaces de rede que você deseja adicionar. Para descobrir a quantos adaptadores de rede cada tamanho de VM dá suporte, confira [tamanhos para máquinas virtuais Linux no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [tamanhos para máquinas virtuais do Windows no Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Selecione **visão geral**, em **configurações**. Selecione **parar**e aguarde até que o **status** da VM seja alterado para **parado (desalocado)** .
4. Selecione **rede**, em **configurações**.
5. Selecione **anexar interface de rede**. Na lista de adaptadores de rede que atualmente não estão conectados a outra VM, selecione aquele que você deseja anexar.

   >[!NOTE]
   >A interface de rede que você selecionar não pode ter a rede acelerada habilitada, não pode ter um endereço IPv6 atribuído a ela e deve existir na mesma rede virtual que a que contém a interface de rede atualmente conectada à VM.

   Se você não tiver uma interface de rede existente, deverá primeiro criar uma. Para fazer isso, selecione **criar interface de rede**. Para saber mais sobre como criar uma interface de rede, confira [criar um adaptador de rede](virtual-network-network-interface.md#create-a-network-interface). Para saber mais sobre restrições adicionais ao adicionar adaptadores de rede a máquinas virtuais, consulte [restrições](#constraints).

6. Selecione **OK**.
7. Selecione **visão geral**, em **configurações**e **Iniciar** para iniciar a máquina virtual.
8. Configure o sistema operacional da VM para usar várias interfaces de rede corretamente. Saiba como configurar o [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou o [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para várias interfaces de rede.

### <a name="commands"></a>Comandos
|Ferramenta|Comando|
|---|---|
|CLI|[AZ VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Reference) ou [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Exibir interfaces de rede para uma VM

Você pode exibir as interfaces de rede atualmente conectadas a uma VM para saber mais sobre a configuração de cada interface de rede e os endereços IP atribuídos a cada interface de rede. 

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que é atribuída à função de proprietário, colaborador ou colaborador de rede para sua assinatura. Para saber mais sobre como atribuir funções a contas, consulte [funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém os recursos de **pesquisa** de texto na parte superior da portal do Azure, digite **máquinas virtuais**. Quando as **máquinas virtuais** aparecerem nos resultados da pesquisa, selecione-as.
3. Selecione o nome da VM para a qual você deseja exibir as interfaces de rede.
4. Na seção **configurações** da VM que você selecionou, selecione **rede**. Para saber mais sobre as configurações de interface de rede e como alterá-las, consulte [gerenciar interfaces de rede](virtual-network-network-interface.md). Para saber mais sobre como adicionar, alterar ou remover endereços IP atribuídos a um adaptador de rede, consulte [Manage Network interface IP addresses](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Remover uma interface de rede de uma VM

1. Inicie sessão no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, procure o nome da VM que você deseja remover (desanexar) o adaptador de rede ou procure a VM selecionando **todos os serviços**e, em seguida, **máquinas virtuais**. Depois de encontrar a VM, selecione-a.
3. Selecione **visão geral**, em **configurações**e em **parar**. Aguarde até que o **status** da VM seja alterado para **parado (desalocado)** .
4. Selecione **rede**, em **configurações**.
5. Selecione **desanexar interface de rede**. Na lista de adaptadores de rede atualmente conectados à máquina virtual, selecione a interface de rede que você deseja desanexar.

   >[!NOTE]
   >Se apenas uma interface de rede estiver listada, você não poderá desanexá-la, pois uma máquina virtual sempre deve ter pelo menos um adaptador de rede anexado a ela.
6. Selecione **OK**.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[AZ VM NIC remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Restrições

- Uma VM deve ter pelo menos uma interface de rede anexada a ela.
- Uma VM só pode ter tantos adaptadores de rede anexados quanto o tamanho da VM dá suporte. Para saber mais sobre quantos adaptadores de rede cada tamanho de VM dá suporte, confira [tamanhos para máquinas virtuais do Linux no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [tamanhos para máquinas virtuais do Windows no Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos os tamanhos dão suporte a pelo menos duas interfaces de rede.
- As interfaces de rede que você adiciona a uma VM atualmente não podem ser anexadas a outra VM. Para saber mais sobre como criar interfaces de rede, confira [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface).
- No passado, as interfaces de rede podiam ser adicionadas apenas às VMs com suporte para várias interfaces de rede e foram criadas com pelo menos duas interfaces de rede. Você não pode adicionar um adaptador de rede a uma VM que foi criada com uma interface de rede, mesmo que o tamanho da VM tenha suporte para várias interfaces de rede. Por outro lado, você só podia remover interfaces de rede de uma VM com pelo menos três interfaces de rede, pois as VMs criadas com pelo menos duas interfaces de rede sempre tinham que ter pelo menos duas interfaces de rede. Nenhuma dessas restrições se aplica mais. Agora você pode criar uma VM com qualquer número de adaptadores de rede (até o número com suporte pelo tamanho da VM).
- Por padrão, a primeira interface de rede anexada a uma VM é definida como a interface de rede *principal* . Todas as outras interfaces de rede na VM são interfaces de rede *secundárias* .
- Embora você possa controlar a qual interface de rede você enviou o tráfego de saída, por padrão, todo o tráfego de saída da VM é enviado ao endereço IP atribuído à configuração de IP primário do adaptador de rede primário.
- No passado, todas as VMs no mesmo conjunto de disponibilidade eram necessárias para ter uma única interface de rede, ou várias. VMs com qualquer número de interfaces de rede agora podem existir no mesmo conjunto de disponibilidade, até o número suportado pelo tamanho da VM. Você só pode adicionar uma VM a um conjunto de disponibilidade quando ela é criada. Para saber mais sobre conjuntos de disponibilidade, confira [gerenciar a disponibilidade de VMs no Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Embora as interfaces de rede na mesma VM possam ser conectadas a sub-redes diferentes em uma rede virtual, as interfaces de rede devem estar conectadas à mesma rede virtual.
- Você pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer interface de rede primária ou secundária a um pool de back-ends Azure Load Balancer. No passado, somente o endereço IP primário para o adaptador de rede primário poderia ser adicionado a um pool de back-ends. Para saber mais sobre endereços IP e configurações, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).
- A exclusão de uma VM não exclui as interfaces de rede que estão anexadas a ela. Quando você exclui uma VM, as interfaces de rede são desanexadas da VM. Você pode adicionar as interfaces de rede a diferentes VMs ou excluí-las.
- Assim como acontece com o IPv6, você não pode anexar um adaptador de rede com rede acelerada habilitada para uma VM depois de criá-la. Além disso, para tirar proveito da rede acelerada, você também deve concluir as etapas dentro do sistema operacional da VM. Saiba mais sobre a rede acelerada e outras restrições ao usá-lo, para máquinas virtuais [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Passos seguintes
Para criar uma VM com vários adaptadores de rede ou endereços IP, consulte os seguintes artigos:

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com vários NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM de NIC única com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM de NIC única com um endereço IPv6 privado (por trás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager modelo](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
