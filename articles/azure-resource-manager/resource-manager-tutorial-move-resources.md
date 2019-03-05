---
title: Mover recursos do Azure para outro grupo de recursos | Documentos da Microsoft
description: Aprenda a mover recursos do Azure a partir de um grupo de recursos para outro grupo de recursos ou de uma subscrição para outra subscrição.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 62e35a361f2fcf9d6f484b699084a78f66121957
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310102"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group"></a>Tutorial: Mover recursos do Azure para outro grupo de recursos

Aprenda a mover recursos do Azure a partir de um grupo de recursos para outro grupo de recursos. Também pode mover os recursos do Azure a partir de uma subscrição do Azure para outra subscrição do Azure. Neste tutorial, utilizou um modelo do resource manager para implementar dois grupos de recursos e uma conta de armazenamento. Em seguida, mover a conta de armazenamento de um grupo de recursos para outro.

![Diagrama de recursos de movimentação do Azure Resource Manager](./media/resource-manager-tutorial-move-resources/resource-manager-template-move-resources.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare os recursos.
> * Certifique-se de que o recurso pode ser movido.
> * Lista de verificação antes de mover o recurso.
> * Valide a operação de movimentação.
> * Mova o recurso.
> * Limpe os recursos.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prepare-the-resources"></a>Preparar os recursos

Um modelo foi criado e colocado para um [partilhado a conta de armazenamento](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). O modelo define dois grupos de recursos e uma conta de armazenamento. Ao implementar o modelo, terá de fornecer um nome de projeto. O nome do projeto é utilizado para gerar nomes de recurso exclusiva.  O JSON seguinte é extraído do modelo:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Tenha em atenção as localizações definidas no json, os grupos de dois recursos estão localizados nos E.U.A. leste e E.U.A. oeste. A conta de armazenamento encontra-se nos EUA Leste. Ao mover um recurso para outro grupo de recursos com uma localização diferente, a operação de movimentação não altera a localização do recurso.

Selecione **experimente** para abrir o Cloud shell e, em seguida, execute o script do PowerShell no Cloud shell:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Aguarde até que o script é concluído com êxito, em seguida, abra a [portal do Azure](https://portal.azure.com)e certifique-se de que os grupos de recursos e a conta de armazenamento são implementados conforme esperado.

> [!NOTE]
> Como o modelo define dois grupo de recursos, esta implementação é considerada como implementação de nível de subscrição. A implementação de modelo do portal não suporta implementações de nível de subscrição. Portanto, o Azure PowerShell é utilizado neste tutorial. CLI do Azure também suporta implementações de nível de subscrição. Ver [criar grupos de recursos e recursos para uma subscrição do Azure](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Certifique-se de que o recurso pode ser movido

Nem todos os recursos podem ser movidos. O recurso utilizado neste tutorial é uma conta de armazenamento, que pode ser movida. Para verificar se um recurso pode ser movido, consulte [serviços que podem ser movidos](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Este passo é opcional para este tutorial, isso já foi feito.

Existem alguns passos importantes para o fazer antes de mover um recurso. Ver [lista de verificação antes de mover recursos](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Validar a movimentação

A validar a movimentação é opcional para este tutorial, porque isso já foi feito.

A operação de movimentação de validar permite testar o seu cenário de movimentação sem, na verdade, mover os recursos. Esta operação é utilizada para determinar se a mudança será bem sucedida. Para obter mais informações, consulte [validar a movimentação](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Mova o recurso

A conta de armazenamento é dentro do grupo de recursos de origem (rg1), execute o PowerShell seguinte script de mover o recurso para o grupo de recursos de destino (rg2). Certifique-se utilizar o mesmo nome de projeto que utilizou quando implementar os recursos.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Abra o [portal do Azure](https://portal.azure.com), certifique-se de que a conta de armazenamento foi movida para o grupo de recursos e também verificar a localização da conta de armazenamento ainda é E.U.A. Leste.

Quando mover os recursos, o grupo de origem e o grupo de destino estão bloqueados durante a operação. Escrever e eliminar operações os grupos de recursos não é permitido enquanto a migração for concluída. Esse bloqueio significa que não é possível adicionar, atualizar ou eliminar recursos nos grupos de recursos, mas isso não significa que os recursos são interrompidos. Por exemplo, se mover um servidor de SQL e a respetiva base de dados para um novo grupo de recursos, uma aplicação que utiliza a base de dados experiências sem períodos de indisponibilidade. Pode ainda ler e escrever na base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome de grupo de recursos de origem.  
4. Selecione **Eliminar grupo de recursos** no menu superior.
5. Selecione o nome de grupo de recursos de destino.  
6. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a mover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos. Até agora, estava lidando com uma conta de armazenamento ou de várias instâncias de conta de armazenamento. No próximo tutorial, vai desenvolver um modelo com vários recursos e vários tipos de recurso. Alguns dos recursos têm recursos dependentes.

> [!div class="nextstepaction"]
> [Criar recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
