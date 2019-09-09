---
title: Criar entidades de serviço e atribuições de função do Windows Virtual Desktop Preview usando o PowerShell-Azure
description: Como criar entidades de serviço e atribuir funções usando o PowerShell na visualização da área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 3e9ee3f5dd04ef838f78b9731885b7ea48e6c99d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811316"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Tutorial: Criar principais de serviço e atribuições de funções com o PowerShell

As entidades de serviço são identidades que você pode criar em Azure Active Directory para atribuir funções e permissões para uma finalidade específica. Na visualização da área de trabalho virtual do Windows, você pode criar uma entidade de serviço para:

- Automatize tarefas específicas de gerenciamento de área de trabalho virtual do Windows.
- Use como credenciais em vez de MFA-usuários necessários ao executar qualquer modelo de Azure Resource Manager para área de trabalho virtual do Windows.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma entidade de serviço no Azure Active Directory.
> * Crie uma atribuição de função na área de trabalho virtual do Windows.
> * Entre na área de trabalho virtual do Windows usando a entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar entidades de serviço e atribuições de função, você precisa fazer três coisas:

1. Instale o módulo AzureAD. Para instalar o módulo, execute o PowerShell como administrador e execute o seguinte cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Baixar e importar o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)

3. Siga todas as instruções neste artigo na mesma sessão do PowerShell. Ele pode não funcionar se você fechar a janela e retornar a ela mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar um principal de serviço no Azure Active Directory

Depois de ter atendido os pré-requisitos em sua sessão do PowerShell, execute os seguintes cmdlets do PowerShell para criar uma entidade de serviço multilocatário no Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="view-your-credentials-in-powershell"></a>Exibir suas credenciais no PowerShell

Antes de encerrar a sessão do PowerShell, exiba suas credenciais e anote-as para referência futura. A senha é especialmente importante porque você não poderá recuperá-la depois de fechar esta sessão do PowerShell.

Aqui estão as três credenciais que devem ser anotadas e os cmdlets que você precisa executar para obtê-las:

- La

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID do locatário:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID do aplicativo:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Criar uma atribuição de função na visualização da área de trabalho virtual do Windows

Em seguida, você criará uma atribuição de função de RDS na área de trabalho virtual do Windows para a entidade de serviço, que permitirá que a entidade de serviço entre na área de trabalho virtual do Windows. Certifique-se de usar uma conta que tenha permissões para criar atribuições de função de RDS.

Execute os seguintes cmdlets do PowerShell para se conectar à área de trabalho virtual do Windows e exibir seus locatários do RDS.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant | FL
```

Use o Tenantname para o locatário correto e execute os seguintes cmdlets do PowerShell para criar uma atribuição de função para a entidade de serviço no locatário especificado.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName "<my-rds-tenantname>"
```

## <a name="sign-in-with-the-service-principal"></a>Entrar com a entidade de serviço

Depois de criar uma atribuição de função para a entidade de serviço, verifique se a entidade de serviço pode entrar na área de trabalho virtual do Windows executando o seguinte cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Depois de entrar, verifique se tudo funciona testando alguns cmdlets do PowerShell de área de trabalho virtual do Windows com a entidade de serviço.

## <a name="next-steps"></a>Passos Seguintes

Depois de criar a entidade de serviço e atribuir a ela uma função no locatário da área de trabalho virtual do Windows, você poderá usá-la para criar um pool de hosts. Para saber mais sobre pools de hosts, continue no tutorial para criar um pool de hosts na área de trabalho virtual do Windows.

 > [!div class="nextstepaction"]
 > [Tutorial pool de hosts da área de trabalho virtual do Windows](./create-host-pools-azure-marketplace.md)
