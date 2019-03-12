---
title: Automatizar a validação de pilha do Azure com o PowerShell | Documentos da Microsoft
description: Pode automatizar a validação de pilha do Azure com o PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7f9a4ce4f1e16f69a1d8998e24c1bfe955d17d92
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767112"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizar a validação de pilha do Azure com o PowerShell

Validação como um serviço (VaaS) fornece a capacidade de automatizar a inicialização do testes usando o **LaunchVaaSTests.ps1** script.

> [!NOTE]  
> Automatização só está disponível para o fluxo de trabalho de aprovação de teste. Os fluxos de trabalho de validação do pacote e a validação de solução só são suportados através do portal de VaaS.

Este script pode ser utilizado para:

> [!div class="checklist"]
> * Instalar pré-requisitos
> * Instalar e iniciar o agente local
> * Inicie uma categoria de testes, como *integração*, *funcional*, *confiabilidade*
> * Resultados do teste de relatório

## <a name="launch-the-test-pass-workflow"></a>Iniciar o fluxo de trabalho de aprovação de teste

1. Abra uma linha de comandos elevada do PowerShell.

2. Execute o seguinte script para transferir o script de automação:

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Execute o seguinte script com os valores de parâmetro adequado:

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parâmetros**

    | Parâmetro | Descrição |
    | --- | --- |
    | VaaSUserId | O ID de utilizador VaaS. |
    | VaaSUserPassword | A palavra-passe VaaS. |
    | VaaSAccountTenantId | O inquilino VaaS GUID. |
    | VaaSSolutionName | O nome da solução VaaS sob a qual o teste passar será executado. |
    | VaaSTestPassName | O nome do teste VaaS transmitir o fluxo de trabalho para criar. |
    | VaaSTestCategories | `Integration`, `Functional`, ou. `Reliability`. Se utilizar vários valores, separe cada valor por uma vírgula.  |
    | ServiceAdminUserName | Sua conta de administrador de serviço do Azure Stack.  |
    | ServiceAdminPassword | A palavra-passe de serviço de Azure Stack.  |
    | TenantAdminUserName | O administrador para o inquilino principal.  |
    | TenantAdminPassword | A palavra-passe para o inquilino principal.  |
    | CloudAdminUserName | O nome de utilizador do administrador de nuvem.  |
    | CloudAdminPassword | A palavra-passe para o administrador da nuvem.  |

4. Reveja os resultados de seu teste. Para outras opções, consulte [Monitor e gerenciem testes no portal do VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o PowerShell no Azure Stack, reveja os módulos mais recentes.

> [!div class="nextstepaction"]
> [Módulo do Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
