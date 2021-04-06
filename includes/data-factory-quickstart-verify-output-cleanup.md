---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 34848b638ff0c7f7b9d1a2f3e5894339f8310ccc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013377"
---
## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

O pipeline cria automaticamente a pasta de saída no contentor de blobs adftutorial. Em seguida, copia o ficheiro emp.txt da pasta de entrada para a pasta de saída. 

1. No portal Azure, na página do recipiente **adftutorial,** selecione **Refresh** para ver a pasta de saída. 
    
    ![A screenshot mostra a página do recipiente onde pode refrescar a página.](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Selecione **a saída** na lista de pastas. 

3. Confirme se o ficheiro **emp.txt** foi copiado para a pasta de saída. 

    ![A screenshot mostra o conteúdo da pasta de saída.](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode eliminar o [grupo de recursos do Azure](../articles/azure-resource-manager/management/overview.md), que inclui todos os recursos no grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas a fábrica de dados que criou neste tutorial.

A eliminação de um grupo de recursos dita a eliminação de todos os recursos, incluindo as fábricas de dados nele incluídas. Execute o seguinte comando para eliminar todo o grupo de recursos: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Deixar cair um grupo de recursos pode levar algum tempo. Seja paciente durante o processo

Se quiser eliminar apenas a fábrica de dados, e não o grupo de recursos completo, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```