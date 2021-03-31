---
title: Windows Virtual Desktop (clássico) atribuição de função principal de serviço - Azure
description: Como criar princípios de serviço e atribuir funções utilizando o PowerShell no Windows Virtual Desktop (clássico).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: 91bb14a174d5bd5c16b38513825579097e1d6f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078533"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Tutorial: Criar diretores de serviço e atribuições de funções com PowerShell no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

Os principais do serviço são identidades que pode criar no Azure Ative Directory para atribuir funções e permissões para um propósito específico. No Windows Virtual Desktop, pode criar um principal de serviço para:

- Automatizar tarefas específicas de gestão do Windows Virtual Desktop.
- Utilize como credenciais no lugar dos utilizadores necessários para o MFA ao executar qualquer modelo de Gestor de Recursos Azure para o Windows Virtual Desktop.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um diretor de serviço no Azure Ative Directory.
> * Crie uma atribuição de funções no Windows Virtual Desktop.
> * Inscreva-se no Windows Virtual Desktop utilizando o principal do serviço.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar diretores de serviços e atribuições de funções, tem de fazer três coisas:

1. Instale o módulo AzureAD. Para instalar o módulo, executar o PowerShell como administrador e executar o seguinte cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Descarregue e importe o módulo PowerShell virtual do Windows Desktop.](/powershell/windows-virtual-desktop/overview/)

3. Siga todas as instruções deste artigo na mesma sessão PowerShell. O processo pode não funcionar se interromper a sua sessão PowerShell fechando a janela e reabrindo-a mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar um principal de serviço no Azure Active Directory

Depois de ter cumprido os pré-requisitos na sua sessão PowerShell, execute os seguintes cmdlets PowerShell para criar um diretor de serviço multitenant em Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Veja as suas credenciais no PowerShell

Antes de criar a atribuição de funções para o seu principal de serviço, veja as suas credenciais e escreva-as para referência futura. A palavra-passe é especialmente importante porque não poderá recuperá-la depois de fechar esta sessão PowerShell.

Aqui estão as três credenciais que deve escrever e os cmdlets que precisa de correr para as obter:

- Palavra-passe:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID do Inquilino:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Application ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de funções no Windows Virtual Desktop

Em seguida, precisa de criar uma atribuição de funções para que o diretor de serviço possa iniciar sessão no Windows Virtual Desktop. Certifique-se de iniciar sessão com uma conta que tenha permissões para criar atribuições de funções.

Em primeiro lugar, [descarregue e importe o módulo PowerShell virtual do Windows Desktop](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez.

Execute os seguintes cmdlets PowerShell para ligar ao Windows Virtual Desktop e exibir os seus inquilinos.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Quando encontrar o nome do inquilino para o inquilino para o qual pretende criar uma atribuição de funções, use esse nome no seguinte cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Inscreva-se com o diretor de serviço

Depois de criar uma atribuição de funções para o principal de serviço, certifique-se de que o principal de serviço pode iniciar sessão no Windows Virtual Desktop executando o seguinte cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Depois de ter feito a sua assinatura, certifique-se de que tudo funciona testando alguns cmdlets Virtual Desktop PowerShell do Windows com o principal de serviço.

## <a name="next-steps"></a>Passos seguintes

Depois de ter criado o principal de serviço e lhe ter atribuído um papel no seu inquilino virtual windows Desktop, pode usá-lo para criar uma piscina de anfitriões. Para saber mais sobre as piscinas de anfitrião, continue o tutorial para criar uma piscina de anfitriões no Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Criar um conjunto de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
