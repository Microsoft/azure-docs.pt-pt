---
title: Tutorial-alta disponibilidade para VMs do Windows no Azure
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para implementar máquinas virtuais de elevada disponibilidade em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0f94f4d312cefec80a0f294e256ee1ad908b903c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068140"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Tutorial: Criar e implementar máquinas virtuais de elevada disponibilidade com o Azure PowerShell

Neste tutorial, você aprenderá a aumentar a disponibilidade e a confiabilidade de suas VMs (máquinas virtuais) usando conjuntos de disponibilidade. Os conjuntos de disponibilidade garantem que as VMs implantadas no Azure sejam distribuídas entre vários nós de hardware isolados, em um cluster. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure


## <a name="availability-set-overview"></a>Descrição geral do conjunto de disponibilidade

Um conjunto de disponibilidade é um recurso de agrupamento lógico para isolar recursos de VM uns dos outros quando eles são implantados. O Azure garante que as VMs colocadas em um conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou de software, apenas um subconjunto de suas VMs será afetado e sua solução geral permanecerá operacional. Os conjuntos de disponibilidade são essenciais para a criação de soluções de nuvem confiáveis.

Consideremos uma solução típica baseada em VM em que poderá ter quatro servidores Web de front-end e duas VMs de back-end. Com o Azure, você desejaria definir dois conjuntos de disponibilidade antes de implantar suas VMs: uma para a camada da Web e outra para a camada de fundo. Ao criar uma nova VM, você especifica o conjunto de disponibilidade como um parâmetro. O Azure garante que as VMs sejam isoladas em vários recursos de hardware físico. Se o hardware físico em que um dos servidores está em execução tiver um problema, você saberá que as outras instâncias dos servidores continuarão em execução porque estão em um hardware diferente.

Utilize Conjuntos de Disponibilidade quando pretender implementar soluções fiáveis baseadas em VM no Azure.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O hardware numa localização está dividido em vários domínios de atualização e domínios de falhas. Um **domínio de atualização** é um grupo de VMs e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. As VMs no mesmo **domínio de falha** partilham o mesmo armazenamento, bem como a fonte de energia e o comutador de rede.  

Você pode criar um conjunto de disponibilidade usando [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). Neste exemplo, o número de domínios de atualização e de falha é *2* e o conjunto de disponibilidade é chamado de *myavailabilityset*.

Crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Crie um conjunto de disponibilidade gerenciado usando [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) com o parâmetro `-sku aligned`.

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
As VMs devem ser criadas dentro do conjunto de disponibilidade para garantir que elas sejam distribuídas corretamente pelo hardware. Você não pode adicionar uma VM existente a um conjunto de disponibilidade após sua criação. 


Ao criar uma VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), use o parâmetro `-AvailabilitySetName` para especificar o nome do conjunto de disponibilidade.

Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, crie duas VMs com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) no conjunto de disponibilidade.

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

Se você examinar o conjunto de disponibilidade no portal acessando **grupos de recursos** > **MyResourceGroupAvailability** > **myavailabilityset**, deverá ver como as VMs são distribuídas entre os dois domínios de falha e de atualização.

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Verificar os tamanhos de VM disponíveis 

Pode adicionar mais VMs ao conjunto de disponibilidade posteriormente, mas tem de conhecer os tamanhos de VM que estão disponíveis no hardware. Use [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Verificar o Assistente do Azure 

Você também pode usar o Azure Advisor para obter mais informações sobre como melhorar a disponibilidade de suas VMs. O Azure Advisor analisa a telemetria de configuração e uso e, em seguida, recomenda soluções que podem ajudá-lo a melhorar a eficácia, o desempenho, a disponibilidade e a segurança dos seus recursos do Azure.

Inicie sessão no [portal do Azure](https://portal.azure.com), selecione **Todos os serviços** e escreva **Assistente**. O painel do Advisor mostra recomendações personalizadas para a assinatura selecionada. Para obter mais informações, veja [Introdução ao Assistente do Azure](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure

Avance para o tutorial seguinte para saber mais sobre os conjuntos de dimensionamento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de VM](tutorial-create-vmss.md)


