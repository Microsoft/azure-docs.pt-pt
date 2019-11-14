---
title: Como marcar um recurso de VM do Windows no Azure
description: Saiba mais sobre como marcar uma máquina virtual do Windows criada no Azure usando o modelo de implantação do Resource Manager
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
ms.openlocfilehash: 52f8dbfc8665fce832eaa60e204273368ae7485d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068204"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Windows no Azure
Este artigo descreve diferentes maneiras de marcar uma máquina virtual do Windows no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou em um grupo de recursos. Atualmente, o Azure dá suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas têm suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager. Se você quiser marcar uma máquina virtual do Linux, consulte [como marcar uma máquina virtual do Linux no Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell
Para criar, adicionar e excluir marcas por meio do PowerShell, primeiro você precisa configurar o [ambiente do PowerShell com Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Depois de concluir a instalação, você poderá posicionar as marcas nos recursos de computação, rede e armazenamento na criação ou depois que o recurso for criado por meio do PowerShell. Este artigo se concentrará na exibição/edição de marcas colocadas em máquinas virtuais.

 

Primeiro, navegue até uma máquina virtual por meio do cmdlet `Get-AzVM`.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se sua máquina virtual já contiver marcas, você verá todas as marcas em seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se você quiser adicionar marcas por meio do PowerShell, poderá usar o comando `Set-AzResource`. Observação ao atualizar marcas por meio do PowerShell, as marcas são atualizadas como um todo. Portanto, se você estiver adicionando uma marca a um recurso que já tem marcas, será necessário incluir todas as marcas que deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar marcas adicionais a um recurso por meio de cmdlets do PowerShell.

Esse primeiro cmdlet define todas as marcas colocadas em *MyTestVM* para a variável *$Tags* , usando a propriedade `Get-AzResource` e `Tags`.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando exibe as marcas para a variável especificada.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

O terceiro comando adiciona uma marca adicional à variável *$Tags* . Observe o uso do **+=** para acrescentar o novo par de chave/valor à lista de *$Tags* .

        PS C:\> $tags += @{Location="MyLocation"}

O quarto comando define todas as marcas definidas na variável *$Tags* para o recurso fornecido. Nesse caso, é MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O quinto comando exibe todas as marcas no recurso. Como você pode ver, *Location* agora é definido como uma marca com *MyLocation* como o valor.

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

Para saber mais sobre marcação por meio do PowerShell, confira os [cmdlets de recurso do Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como marcar os recursos do Azure, confira [Azure Resource Manager visão geral][Azure Resource Manager Overview] e [usando marcas para organizar os recursos do Azure][Using Tags to organize your Azure Resources].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [noções básicas sobre sua fatura do Azure][Understanding your Azure Bill] e [obter informações sobre o consumo de recursos Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
