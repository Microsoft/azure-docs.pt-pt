---
title: Criar entidades de serviço e atribuições de função do Windows Virtual Desktop Preview usando o PowerShell-Azure
description: Como criar entidades de serviço e atribuir funções usando o PowerShell na visualização da área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
ms.openlocfilehash: a9b5eecd97b078c9446e28d971f900c4cf65130f
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845527"
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

2. [Baixe e importe o módulo do PowerShell da área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

3. Siga todas as instruções neste artigo na mesma sessão do PowerShell. O processo pode não funcionar se você interromper a sessão do PowerShell fechando a janela e reabrindo-a mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar um principal de serviço no Azure Active Directory

Depois de ter atendido os pré-requisitos em sua sessão do PowerShell, execute os seguintes cmdlets do PowerShell para criar uma entidade de serviço multilocatário no Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Exibir suas credenciais no PowerShell

Antes de criar a atribuição de função para sua entidade de serviço, exiba suas credenciais e anote-as para referência futura. A senha é especialmente importante porque você não poderá recuperá-la depois de fechar esta sessão do PowerShell.

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

Em seguida, você precisa criar uma atribuição de função para que a entidade de serviço possa entrar na área de trabalho virtual do Windows. Certifique-se de entrar com uma conta que tenha permissões para criar atribuições de função.

Primeiro, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso.

Execute os seguintes cmdlets do PowerShell para se conectar à área de trabalho virtual do Windows e exibir seus locatários.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Quando você encontrar o nome do locatário para o locatário para o qual deseja criar uma atribuição de função, use esse nome no seguinte cmdlet:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
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
