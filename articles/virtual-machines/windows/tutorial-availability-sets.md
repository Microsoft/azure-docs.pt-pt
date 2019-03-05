---
title: Tutorial – Elevada disponibilidade para VMs do Windows no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para implementar máquinas virtuais de elevada disponibilidade em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f71bfa39e4ded0ea300cc2d329c442fdc6ddec37
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309082"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Tutorial: Criar e implementar máquinas virtuais altamente disponíveis com o Azure PowerShell

Neste tutorial, saiba como aumentar a disponibilidade e fiabilidade das suas máquinas virtuais (VMs) ao utilizar conjuntos de disponibilidade. Conjuntos de disponibilidade, certifique-se de que as VMs que implementa no Azure são distribuídas por vários nós de hardware isolados, num cluster. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure


## <a name="availability-set-overview"></a>Descrição geral do conjunto de disponibilidade

Um conjunto de disponibilidade é uma funcionalidade de agrupamento lógico para isolar os recursos da VM entre si quando são implantados. O Azure torna-se de que as VMs que colocar num conjunto de disponibilidade são executadas em vários servidores físicos, computação racks, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou software, apenas um subconjunto das suas VMs são afetados e a solução global permanece operacional. Conjuntos de disponibilidade são essenciais para a criação de soluções cloud fiáveis.

Consideremos uma solução típica baseada em VM em que poderá ter quatro servidores Web de front-end e duas VMs de back-end. Com o Azure, iria querer definir dois conjuntos de disponibilidade antes de implementar as suas VMs: um para a camada web e para a camada de back. Quando cria uma nova VM, especifique o conjunto como um parâmetro de disponibilidade. O Azure torna-se de que as VMs estão isoladas em vários recursos de hardware físico. Se o hardware físico, um dos seus servidores em execução no tiver um problema, sabe que as outras instâncias dos seus servidores continuará a ser executada uma vez que estão num hardware diferente.

Utilize Conjuntos de Disponibilidade quando pretender implementar soluções fiáveis baseadas em VM no Azure.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O hardware numa localização está dividido em vários domínios de atualização e domínios de falhas. Um **domínio de atualização** é um grupo de VMs e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. As VMs no mesmo **domínio de falha** partilham o mesmo armazenamento, bem como a fonte de energia e o comutador de rede.  

Pode criar um conjunto de disponibilidade com [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). Neste exemplo, é o número de domínios de atualização e falha *2* e o nome do conjunto de disponibilidade *myAvailabilitySet*.

Crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Criar uma conjunto através de disponibilidade gerido [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) com o `-sku aligned` parâmetro.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs num conjunto de disponibilidade
VMs tem de ser criadas dentro do conjunto de disponibilidade para certificar-se de que estão corretamente distribuídas no hardware. Não é possível adicionar uma VM existente para um conjunto de disponibilidade depois é criado. 


Quando cria uma VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), que utiliza o `-AvailabilitySetName` parâmetro para especificar o nome do conjunto de disponibilidade.

Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie duas VMs com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) no conjunto de disponibilidade.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Demora alguns minutos para criar e configurar ambas as VMs. Quando terminar, tem duas máquinas virtuais distribuídas no hardware subjacente. 

Se examinar a conjunto de disponibilidade no portal, vai **grupos de recursos** > **myResourceGroupAvailability** > **myAvailabilitySet**, deverá ver como as VMs são distribuídas entre as duas falhas e domínios de atualização.

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Verificar os tamanhos de VM disponíveis 

Pode adicionar mais VMs ao conjunto de disponibilidade posteriormente, mas tem de conhecer os tamanhos de VM que estão disponíveis no hardware. Uso [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) para listar todos os tamanhos disponíveis no hardware de cluster para o conjunto de disponibilidade.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Verificar o Assistente do Azure 

Também pode utilizar o Assistente do Azure para obter mais informações sobre como melhorar a disponibilidade das suas VMs. O Azure Advisor analisa a sua configuração e a telemetria de utilização, em seguida, recomenda soluções que podem ajudar a melhorar a eficiência de custos, desempenho, disponibilidade e segurança dos seus recursos do Azure.

Inicie sessão no [portal do Azure](https://portal.azure.com), selecione **Todos os serviços** e escreva **Assistente**. O dashboard do assistente apresenta recomendações personalizadas para a subscrição selecionada. Para obter mais informações, veja [Introdução ao Assistente do Azure](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure

Avance para o tutorial seguinte para saber mais sobre os conjuntos de dimensionamento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de VM](tutorial-create-vmss.md)


