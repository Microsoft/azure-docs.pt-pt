---
title: Como marcar um VM usando PowerShell
description: Saiba como marcar uma máquina virtual usando o PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594973"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Como marcar uma máquina virtual em Azure usando o PowerShell

Este artigo descreve como marcar um VM em Azure usando PowerShell. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Se pretender marcar uma máquina virtual utilizando o Azure CLI, consulte [Como marcar uma máquina virtual em Azure utilizando o Azure CLI](tag-cli.md).


Utilize o `Get-AzVM` cmdlet para visualizar a lista atual de etiquetas para o seu VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Se a sua Máquina Virtual já tiver etiquetas, verá todas as tags no formato da lista.

Para adicionar tags, use o `Set-AzResource` comando. Ao atualizar as tags através do PowerShell, as tags são atualizadas como um todo. Se estiver a adicionar uma etiqueta a um recurso que já tenha etiquetas, terá de incluir todas as etiquetas que pretende colocar no recurso. Abaixo está um exemplo de como adicionar tags adicionais a um recurso através de PowerShell Cmdlets.

Atribua todas as etiquetas atuais para o VM à `$tags` variável, utilizando a `Get-AzResource` propriedade e a `Tags` propriedade.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Para ver as etiquetas atuais, digite a variável.

```azurepowershell-interactive
$tags
```

Aqui está o que a saída pode parecer:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

No exemplo seguinte, adicionamos uma etiqueta chamada `Location` com o `myLocation` valor. Utilize `+=` para anexar o novo par de chave/valor à `$tags` lista.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Utilize `Set-AzResource` para definir todas as etiquetas definidas na *variável $tags* no VM.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Utilize `Get-AzResource` para exibir todas as etiquetas no recurso.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

A saída deve parecer-se com o seguinte, que agora inclui a nova etiqueta:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Próximos passos**

- Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure](../azure-resource-manager/management/overview.md) e [a utilização de Tags para organizar os seus Recursos Azure.](../azure-resource-manager/management/tag-resources.md)
- Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [compreender o seu Azure Bill](../cost-management-billing/understand/review-individual-bill.md) e obter informações sobre o seu consumo de recursos Microsoft [Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
