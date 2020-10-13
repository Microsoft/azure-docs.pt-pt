---
title: Como marcar um VM usando PowerShell
description: Saiba como marcar uma máquina virtual usando o PowerShell
author: mmccrory
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 3aa40ed396e87b342207fc51576cd28170c7d4e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322386"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Como marcar uma máquina virtual em Azure usando o PowerShell
Este artigo descreve diferentes formas de marcar uma máquina virtual do Windows no Azure através do modelo de implementação do Gestor de Recursos. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Por favor, note que as tags são suportadas para recursos criados apenas através do modelo de implementação do Gestor de Recursos. Se pretender marcar uma máquina virtual utilizando o Azure CLI, consulte [Como marcar uma máquina virtual em Azure utilizando o Azure CLI](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com PowerShell
Para criar, adicionar e eliminar tags através do PowerShell, primeiro tem de configurar o seu [ambiente PowerShell com o Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Uma vez concluída a configuração, pode colocar etiquetas nos recursos de Computação, Rede e Armazenamento na criação ou após a criação do recurso através do PowerShell. Este artigo concentrar-se-á nas etiquetas de visualização/edição colocadas nas Máquinas Virtuais.

 

Primeiro, navegue para uma Máquina Virtual através do `Get-AzVM` cmdlet.

```azurepowershell
PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"
```

Se a sua Máquina Virtual já tiver etiquetas, verá todas as etiquetas do seu recurso:

```json
Tags : {
        "Application": "MyApp1",
        "Created By": "MyName",
        "Department": "MyDepartment",
        "Environment": "Production"
        }
```

Se quiser adicionar tags através do PowerShell, pode utilizar o `Set-AzResource` comando. Note que ao atualizar as tags através do PowerShell, as etiquetas são atualizadas como um todo. Portanto, se estiver a adicionar uma etiqueta a um recurso que já tenha etiquetas, terá de incluir todas as etiquetas que pretende ser colocadas no recurso. Abaixo está um exemplo de como adicionar tags adicionais a um recurso através de PowerShell Cmdlets.

Este primeiro cmdlet define todas as tags colocadas no *MyTestVM* para a variável *$tags,* utilizando a `Get-AzResource` propriedade e a `Tags` propriedade.

```azurepowershell
PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags
```

O segundo comando mostra as etiquetas para a variável dada.

```azurepowershell
PS C:\> $tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

O terceiro comando adiciona uma etiqueta adicional à variável *$tags.* Note a utilização do **+=** par para anexar o novo par de chaves/valor à lista *de $tags.*

```azurepowershell
PS C:\> $tags += @{Location="MyLocation"}
```

O quarto comando define todas as etiquetas definidas no *$tags* variável ao recurso dado. Neste caso, é myTestVM.

```azurepowershell
PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

O quinto comando mostra todas as etiquetas no recurso. Como pode ver, *a localização* é agora definida como uma etiqueta com *MyLocation* como o valor.

```azurepowershell
PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

Para saber mais sobre a marcação através do PowerShell, consulte os [Cmdlets de Recursos Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure][Azure Resource Manager Overview] e [a utilização de Tags para organizar os seus Recursos Azure.][Using Tags to organize your Azure Resources]
* Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [compreender o seu Azure Bill][Understanding your Azure Bill] e obter informações sobre o seu consumo de recursos Microsoft [Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: /powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
