---
title: Módulo PowerShell para | de Serviços de Laboratório Azure Microsoft Docs
description: Este artigo fornece informações sobre um módulo PowerShell que ajuda na gestão de artefactos nos Serviços Azure Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646530"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo do PowerShell Az.LabServices (pré-visualização)
Az.LabServices é um módulo PowerShell que simplifica a gestão dos serviços do Azure Lab. Fornece funções compósíveis para criar, consultar, atualizar e apagar contas de laboratório, laboratórios, VMs e Imagens. Para mais informações sobre este módulo, consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo está em **pré-visualização.** 

## <a name="example-command"></a>Comando de exemplo
Aqui está um exemplo de usar um comando PowerShell para parar todos os VMs em execução em todos os laboratórios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introdução
1. Instale [a Azure PowerShell](/powershell/azure/) se não existir na sua máquina. 
2. Faça o download [de Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) para a sua máquina.
3. Importar o módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Executar o seguinte comando para listar todos os laboratórios da sua assinatura.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Passos seguintes
Consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).