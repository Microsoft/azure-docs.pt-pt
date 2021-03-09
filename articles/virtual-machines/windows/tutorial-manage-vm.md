---
title: Tutorial - Criar e gerir VMs do Windows com Azure PowerShell
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar e gerir VMs do Windows no Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: b9b7fee32ec8d844452cfcb5eba29859467105e4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520766"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Tutorial: Criar e gerir VMs do Windows com o Azure PowerShell

As máquinas virtuais do Azure proporcionam um ambiente informático totalmente configurável e flexível. Este tutorial abrange tarefas de implementação básicas de máquinas virtuais (VM) do Azure, como selecionar um tamanho de VM, selecionar uma imagem de VM e implementar uma VM. Saiba como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos específicos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de uma VM

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. No exemplo seguinte, é criado um grupo de recursos designado *myResourceGroupVM* na região *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

O grupo de recursos é especificado ao criar ou modificar uma VM, o que pode ser visto ao longo deste tutorial.

## <a name="create-a-vm"></a>Criar uma VM

Ao criar um VM, várias opções estão disponíveis como imagem do sistema operativo, configuração de rede e credenciais administrativas. Este exemplo cria uma VM denominada *myVM*, que executa a versão predefinida do Windows Server 2016 Datacenter.

Desconfiem do nome de utilizador e da palavra-passe necessários para a conta do administrador na VM com [a Get-Credential:](/powershell/module/microsoft.powershell.security/get-credential)

```azurepowershell-interactive
$cred = Get-Credential
```

Crie o VM com [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Ligar à VM

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto à VM.

Execute os seguintes comandos para devolver o endereço IP público da VM. Tome nota deste endereço IP, para que se possa ligar ao mesmo com o seu browser e testar a conectividade Web num passo posterior.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Utilize o seguinte comando no computador local para criar uma sessão de ambiente de trabalho remoto com a VM. Substitua o endereço IP pelo *publicIPAddress* da VM. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a VM.

```powershell
mstsc /v:<publicIpAddress>
```

Na janela **Segurança do Windows**, selecione **Mais escolhas** e **Utilizar uma conta diferente**. Escreva o nome de utilizador e a palavra-passe que criou para a VM e clique em **OK**.

## <a name="understand-marketplace-images"></a>Compreender as imagens do Marketplace

O Azure Marketplace inclui várias imagens que podem ser utilizadas para criar uma VM nova. Nos passos anteriores, foi criada uma VM com uma imagem do Windows Server 2016 Datacenter. Neste passo, o módulo do PowerShell serve para pesquisar outras imagens do Windows no marketplace, que pode também ser utilizado como base para novas VMs. Este processo consiste em encontrar o publicador, a oferta, o SKU e, opcionalmente, um número de versão para [identificar](cli-ps-findimage.md#terminology) a imagem.

Utilize o comando [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) para devolver uma lista de editores de imagem:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Utilize o [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) para devolver uma lista de ofertas de imagem. Com este comando, a lista devolvida é filtrada no publicador especificado, denominado `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Os resultados serão semelhantes a este exemplo: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

O comando [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) irá então filtrar a editora e oferecer o nome para devolver uma lista de nomes de imagem.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Os resultados serão semelhantes a este exemplo: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Estas informações podem ser utilizadas para implementar uma VM com uma imagem específica. Este exemplo implementa uma VM com a versão mais recente de uma imagem de Contentores do Windows Server 2016.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os pedidos do PowerShell voltem para si. Pode ver os detalhes das tarefas em segundo plano com o cmdlet `Get-Job`.

## <a name="understand-vm-sizes"></a>Compreender os tamanhos de VM

O tamanho VM determina a quantidade de recursos computacional como CPU, GPU e memória que são disponibilizados ao VM. As máquinas virtuais devem ser criadas utilizando um tamanho VM adequado para a carga de trabalho. Se a carga de trabalho aumentar, uma máquina virtual existente também pode ser redimensionada.

### <a name="vm-sizes"></a>Tamanhos de VMs

A tabela seguinte categoriza tamanhos em casos de utilização.  

| Tipo                     | Tamanhos comuns           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| CPU para memória equilibrada. Ideal para desenvolvimento/teste e aplicações e soluções de dados pequenas a médias.  |
| [Com otimização de computação](../sizes-compute.md)   | Fsv2          | CPU para memória elevada. É adequado para aplicações de tráfego médio, dispositivos de rede e processos em lote.        |
| [Com otimização de memória](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Memória-para-núcleo elevada. É ideal para bases de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](../sizes-storage.md)      | Lsv2, Ls              | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | VMs especializadas destinadas a composição gráfica e edição de vídeo exigentes.       |
| [Elevado desempenho](../sizes-hpc.md) | H        | As nossas mais avançadas VMs com CPU, com interfaces de rede de alto débito (RDMA) opcionais. |

### <a name="find-available-vm-sizes"></a>Localizar todos os tamanhos de VM disponíveis

Para ver uma lista de tamanhos VM disponíveis numa determinada região, utilize o comando [Get-AzVMSize.](/powershell/module/az.compute/get-azvmsize)

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Redimensionar uma VM

Depois de implementar uma VM, esta pode ser redimensionada para aumentar ou diminuir a alocação de recursos.

Antes de redimensionar um VM, verifique se o tamanho que deseja está disponível no cluster VM atual. O comando [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) devolve uma lista de tamanhos.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Se o tamanho estiver disponível, o VM pode ser redimensionado a partir de um estado alimentado, no entanto é reiniciado durante a operação.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Se o tamanho que deseja não estiver disponível no cluster atual, o VM precisa de ser transacionado antes de a operação de redimensionar. Desalocar a VM remove os dados no disco temporário e o endereço IP público é alterado, a menos que esteja a ser utilizado um endereço IP estático.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Estados de energia da VM

Uma VM do Azure pode ter um de vários estados de energia. 


| Estado de Energia | Description
|----|----|
| A iniciar | A máquina virtual está a ser iniciada. |
| Em Execução | A máquina virtual está a funcionar. |
| A parar | A máquina virtual está a ser detida. |
| Parada | A VM está parada. As máquinas virtuais no estado de paragem continuam a incorrer em custos de computação.  |
| A desalocar | O VM está a ser negociado. |
| Desalocada | Indica que o VM é removido do hipervisor, mas ainda está disponível no plano de controlo. As máquinas virtuais no estado `Deallocated` não incorrem em custos de computação. |
| - | O estado de energia do VM é desconhecido. |


Para obter o estado de um VM em particular, use o comando [Get-AzVM.](/powershell/module/az.compute/get-azvm) Certifique-se de que especifica um nome válido para uma VM e um grupo de recursos.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

A saída terá um aspeto semelhante a este exemplo:

```powershell
Status
------
PowerState/running
```

Para recuperar o estado de potência de todos os VMs na sua subscrição, utilize as [Máquinas Virtuais - Liste Todas as API](/rest/api/compute/virtualmachines/listall) com o status de **parâmetros Definidos** para *verdadeiros*.

## <a name="management-tasks"></a>Tarefas de gestão

Durante o ciclo de vida de um VM, pode querer executar tarefas de gestão como iniciar, parar ou eliminar um VM. Além disso, pode querer criar scripts para automatizar tarefas repetitivas ou complexas. Ao utilizar o Azure PowerShell, muitas tarefas comuns de gestão podem ser executadas na linha de comandos ou em scripts.

### <a name="stop-a-vm"></a>Parar uma VM

Parar e negociar um VM com [Stop-AzVM:](/powershell/module/az.compute/stop-azvm)

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Se pretender manter a VM no estado aprovisionado, utilize o parâmetro -StayProvisioned.

### <a name="start-a-vm"></a>Iniciar uma VM

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Eliminar grupo de recursos

Tudo dentro de um grupo de recursos é eliminado quando elimina o grupo de recursos.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a criação e gestão básica de VMs, como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos específicos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de uma VM

Avance para o próximo tutorial para saber mais sobre os discos de VM.  

> [!div class="nextstepaction"]
> [Criar e Gerir discos VM](./tutorial-manage-data-disk.md)