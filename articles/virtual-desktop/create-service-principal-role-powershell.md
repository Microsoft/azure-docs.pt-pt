---
title: Criar principais de serviço de pré-visualização de ambiente de Trabalho Virtual do Windows e as atribuições de funções com o PowerShell - Azure
description: Como criar principais de serviço e atribuir funções com o PowerShell no Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 93725fc9d77552d779378d0c14294a5bbb11c926
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146133"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Tutorial: Criar principais de serviço e atribuições de funções com o PowerShell

Principais de serviço são identidades que pode criar no Azure Active Directory para atribuir funções e permissões para um fim específico. No Windows Virtual Desktop Preview, pode criar um serviço principal para:

- Automatizar tarefas de gestão de área de Trabalho Virtual do Windows específicas
- Utilizar como credenciais em vez de utilizadores necessário de MFA durante a execução de qualquer modelo de Windows Virtual Desktop Azure Resource Manager

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um principal de serviço no Azure Active Directory
> * Criar uma atribuição de função na área de Trabalho Virtual do Windows
> * Inicie sessão no ambiente de Trabalho Virtual do Windows com o principal de serviço

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar principais de serviço e as atribuições de funções, terá de fazer três coisas:

1. Instale o módulo do AzureAD. Para instalar o módulo, execute o PowerShell como administrador e execute o seguinte cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. Execute os seguintes cmdlets com os valores são aspas substituído pelos valores relevantes à sua sessão.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. Siga todas as instruções neste artigo na mesma sessão do PowerShell. Poderá não funcionar se fecha a janela e voltar a ele mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar um principal de serviço no Azure Active Directory

Assim que tiver concluído os pré-requisitos na sessão do PowerShell, execute os seguintes cmdlets do PowerShell para criar um serviço de multi-inquilino principal no Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Criar uma atribuição de função no Windows Virtual Desktop pré-visualização

Agora que criou um serviço principal, pode utilizá-lo para iniciar sessão na área de Trabalho Virtual do Windows. Certifique-se de iniciar sessão com uma conta que tenha permissões para criar a atribuição de função.

Primeiro, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

Execute os seguintes cmdlets do PowerShell para ligar a área de Trabalho Virtual do Windows e criar uma atribuição de função para o serviço principal.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Inicie sessão com o principal de serviço

Depois de criar uma atribuição de função para o serviço principal, deve agora fazer-se de que o principal de serviço pode iniciar sessão na área de Trabalho Virtual do Windows, execute o seguinte cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Uma vez iniciar sessão, certifique-se de que tudo funciona testando alguns cmdlets do Windows PowerShell de ambiente de Trabalho Virtual com o principal de serviço.

## <a name="view-your-credentials-in-powershell"></a>Ver as suas credenciais no PowerShell

Antes de terminar a sessão do PowerShell, deve ver as suas credenciais e anotá-las para referência futura. A palavra-passe é especialmente importante, porque não será possível recuperá-la depois de fechar esta sessão do PowerShell.

Seguem-se as credenciais de três que deve escrever para baixo e os cmdlets que precisa para ser executada para obtê-los:

- Palavra-passe:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID do Inquilino:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID da Aplicação:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a criar principal de um serviço e inicie sessão na área de Trabalho Virtual do Windows com o mesmo. Para saber mais sobre como iniciar sessão área de Trabalho Virtual do Windows, avance para ligar ao Windows procedimentos de ambiente de Trabalho Virtual.

- [Ligar a partir do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
- [Ligar a partir de um navegador da web](connect-web.md)
