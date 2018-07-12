---
title: Como Etiquetar um recurso de VM do Windows no Azure | Documentos da Microsoft
description: Saiba mais sobre a marcação de uma máquina virtual do Windows criada no Azure com o modelo de implementação do Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531325"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como Etiquetar uma máquina virtual do Windows no Azure
Este artigo descreve as diferentes formas de etiquetar uma máquina virtual do Windows no Azure através do modelo de implementação do Resource Manager. As etiquetas são pares de chave/valor definido pelo utilizador que podem ser colocados diretamente num recurso ou grupo de recursos. Atualmente, o Azure suporta até 15 etiquetas por recurso e grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção que as etiquetas são suportadas para os recursos criados por meio de apenas o modelo de implementação do Resource Manager. Se quiser Etiquetar uma máquina virtual do Linux, veja [como Etiquetar uma máquina virtual Linux no Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell
Para criar, adicionar e eliminar etiquetas através do PowerShell, tem de primeiro para configurar a sua [ambiente do PowerShell com o Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Depois de concluir a configuração, pode colocar as etiquetas nos recursos de computação, rede e armazenamento durante a criação ou depois do recurso é criado através do PowerShell. Este artigo se concentrará em exibição/edição etiquetas colocadas em máquinas virtuais.

Em primeiro lugar, navegue para uma Máquina Virtual através do `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se a sua máquina Virtual já contém etiquetas, em seguida, verá todas as etiquetas no recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se gostaria de adicionar etiquetas através do PowerShell, pode utilizar o `Set-AzureRmResource` comando. Tenha em atenção ao atualizar as etiquetas através do PowerShell, as etiquetas são atualizadas como um todo. Portanto, se estiver a adicionar uma etiqueta a um recurso que já tem etiquetas, terá de incluir todas as marcas que deseja ser colocado no recurso. Segue-se um exemplo de como adicionar etiquetas adicionais a um recurso através de Cmdlets do PowerShell.

Este cmdlet primeiro define todas as etiquetas colocadas no *MyTestVM* para o *$tags* variável, utilizando o `Get-AzureRmResource` e `Tags` propriedade.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando apresenta as etiquetas para a variável de determinado.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

O terceiro comando adiciona uma etiqueta adicional para o *$tags* variável. Observe o uso do **+=** para anexar o novo par de chave/valor para o *$tags* lista.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

O comando a quarto define todas as etiquetas definidas no *$tags* variável ao recurso indicado. Neste caso, é MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O quinto comando mostra todas as etiquetas no recurso. Como pode ver, *localização* é agora definido como uma marca com *MyLocation* como o valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Para saber mais sobre marcação através do PowerShell, consulte a [Cmdlets de recursos do Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre marcação seus recursos do Azure, veja [descrição geral do Azure Resource Manager] [ Azure Resource Manager Overview] e [utilizando etiquetas para organizar os recursos do Azure] [ Using Tags to organize your Azure Resources].
* Para ver como as etiquetas podem ajudar a gerir a utilização de recursos do Azure, consulte [entender a sua fatura do Azure] [ Understanding your Azure Bill] e [obter informações sobre o consumo de recursos do Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
