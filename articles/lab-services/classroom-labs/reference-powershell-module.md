---
title: Módulo do PowerShell para Azure Lab Services | Microsoft Docs
description: Este artigo fornece informações sobre um módulo do PowerShell que ajuda no gerenciamento de artefatos no Azure Lab Services.
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
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609401"
---
# <a name="azlabservices-powershell-module-preview"></a>Módulo do PowerShell AZ. LabServices (versão prévia)
AZ. LabServices é um módulo do PowerShell que simplifica o gerenciamento dos serviços de laboratório do Azure. Ele fornece funções combináveis para criar, consultar, atualizar e excluir contas de laboratório, laboratórios, VMs e imagens. Para obter mais informações sobre esse módulo, consulte [AZ. LabServices Home Page no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Este módulo está em versão **prévia**. 

## <a name="example-command"></a>Exemplo de comando
Aqui está um exemplo de como usar um comando do PowerShell para interromper todas as VMs em execução em todos os laboratórios.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introdução
1. Instale [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se ele não existir em seu computador. 
2. Baixe [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) em seu computador.
3. Importe o módulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Execute o comando a seguir para listar todos os laboratórios em sua assinatura.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Passos seguintes
Consulte o [Home Page AZ. LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
