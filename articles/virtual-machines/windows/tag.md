---
title: Como marcar um recurso VM do Windows em Azure
description: Saiba como marcar uma máquina virtual do Windows criada em Azure utilizando o modelo de implementação do Gestor de Recursos
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b646b1a14d6cedcafa662192229daa570a0d2441
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616434"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Windows em Azure
Este artigo descreve diferentes formas de marcar uma máquina virtual do Windows em Azure através do modelo de implementação do Gestor de Recursos. As etiquetas são pares de chaves/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. O Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Por favor, note que as tags são suportadas apenas para recursos criados apenas através do modelo de implementação do Gestor de Recursos. Se quiser marcar uma máquina virtual Linux, veja [como marcar uma máquina virtual Linux em Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com PowerShell
Para criar, adicionar e eliminar tags através do PowerShell, primeiro precisa de configurar o seu [ambiente PowerShell com o Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Uma vez concluída a configuração, pode colocar etiquetas nos recursos Compute, Network e Storage na criação ou após a criação do recurso através do PowerShell. Este artigo concentrar-se-á nas etiquetas de visualização/edição colocadas em Máquinas Virtuais.

 

Primeiro, navegue para uma Máquina Virtual através do `Get-AzVM` cmdlet.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se a sua Máquina Virtual já contiver etiquetas, verá todas as etiquetas do seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se quiser adicionar tags através do PowerShell, pode utilizar o comando `Set-AzResource`. Note ao atualizar as etiquetas através do PowerShell, as etiquetas são atualizadas como um todo. Por isso, se estiver a adicionar uma etiqueta a um recurso que já tem etiquetas, terá de incluir todas as etiquetas que pretende colocar no recurso. Abaixo está um exemplo de como adicionar tags adicionais a um recurso através de Cmdlets PowerShell.

Este primeiro cmdlet define todas as etiquetas colocadas no *MyTestVM* para a *variável $tags,* utilizando a propriedade `Get-AzResource` e `Tags`.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando exibe as etiquetas para a variável dada.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

O terceiro comando adiciona uma etiqueta adicional à *variável $tags.* Note a utilização do **+=** para anexar o novo par chave/valor à lista *$tags.*

        PS C:\> $tags += @{Location="MyLocation"}

O quarto comando define todas as etiquetas definidas na *variável $tags* para o recurso dado. Neste caso, é MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O quinto comando exibe todas as etiquetas do recurso. Como pode ver, a *localização* é agora definida como uma etiqueta com *MyLocation* como o valor.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Para saber mais sobre a marcação através da PowerShell, confira os [Cmdlets][Azure Resource Cmdlets]de Recursos Azure .

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a marcação dos seus recursos Azure, consulte a [visão geral do Gestor de Recursos Azure][Azure Resource Manager Overview] e usando [tags para organizar os seus Recursos Azure.][Using Tags to organize your Azure Resources]
* Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [Understanding your Azure Bill][Understanding your Azure Bill] e Gain insights sobre o seu consumo de recursos Microsoft [Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
