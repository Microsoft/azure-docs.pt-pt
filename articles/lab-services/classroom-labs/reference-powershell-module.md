---
title: Módulo PowerShell para serviços de laboratório azure / Microsoft Docs
description: Este artigo fornece informações sobre um módulo PowerShell que ajuda na gestão de artefactos nos Serviços de Laboratório Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73609401"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo do PowerShell Az.LabServices (pré-visualização)
Az.LabServices é um módulo PowerShell que simplifica a gestão dos serviços do Azure Lab. Fornece funções compostas para criar, consultar, atualizar e eliminar contas de laboratório, laboratórios, VMs e Imagens. Para obter mais informações sobre este módulo, consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo está em **pré-visualização.** 

## <a name="example-command"></a>Comando de exemplo
Aqui está um exemplo de usar um comando PowerShell para parar todos os VMs em todos os laboratórios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introdução
1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se não existir na sua máquina. 
2. Baixe [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) para a sua máquina.
3. Importar o módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Execute o seguinte comando para listar todos os laboratórios na sua subscrição.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Passos seguintes
Consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
