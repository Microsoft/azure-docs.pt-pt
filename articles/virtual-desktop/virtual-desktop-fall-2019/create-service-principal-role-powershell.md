---
title: Atribuição principal de função do serviço de desktop virtual windows - Azure
description: Como criar os principais de serviço e atribuir funções utilizando o PowerShell no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad78fe2ce66511cb75e23a28a30c44b173da9020
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117715"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Tutorial: Criar diretores de serviço e atribuições de funções utilizando a PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure.

Os principais de serviço são identidades que pode criar no Diretório Ativo Azure para atribuir funções e permissões para um fim específico. No Windows Virtual Desktop, pode criar um principal de serviço para:

- Automatizar tarefas específicas de gestão do Ambiente de Trabalho virtual do Windows.
- Utilize como credenciais no lugar dos utilizadores necessários ao executar qualquer modelo de Gestor de Recursos Azure para o Windows Virtual Desktop.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um diretor de serviço no Azure Ative Directory.
> * Crie uma atribuição de funções no Windows Virtual Desktop.
> * Inscreva-se no Windows Virtual Desktop utilizando o diretor de serviço.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar diretores de serviço e atribuições de papéis, tem de fazer três coisas:

1. Instale o módulo AzureAD. Para instalar o módulo, executar powerShell como administrador e executar o seguinte cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Descarregue e importe o módulo PowerShell do Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/).

3. Siga todas as instruções deste artigo na mesma sessão PowerShell. O processo pode não funcionar se interromper a sua sessão PowerShell fechando a janela e reabrindo-a mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar um principal de serviço no Azure Active Directory

Depois de ter cumprido os pré-requisitos na sua sessão powerShell, faça os seguintes cmdlets PowerShell para criar um serviço multiarrendatário principal em Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Veja as suas credenciais no PowerShell

Antes de criar a atribuição de funções para o seu diretor de serviço, veja as suas credenciais e escreva-as para referência futura. A palavra-passe é especialmente importante porque não poderá recuperá-la depois de encerrar esta sessão powerShell.

Aqui estão as três credenciais que deve escrever e os cmdlets que precisa correr para as obter:

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

Em seguida, é necessário criar uma atribuição de funções para que o diretor de serviço possa iniciar sessão no Windows Virtual Desktop. Certifique-se de assinar com uma conta que tenha permissões para criar atribuições de papéis.

Primeiro, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez.

Execute os seguintes cmdlets PowerShell para ligar ao Windows Virtual Desktop e mostrar os seus inquilinos.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Quando encontrar o nome do inquilino para o inquilino, pretende criar uma atribuição de funções, use esse nome no seguinte cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Inscreva-se no diretor de serviço

Depois de criar uma atribuição de funções para o diretor de serviço, certifique-se de que o diretor de serviço pode iniciar sessão no Windows Virtual Desktop executando o seguinte cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Depois de ter assinado o contrato, certifique-se de que tudo funciona testando alguns cmdlets Windows Virtual Desktop PowerShell com o diretor de serviço.

## <a name="next-steps"></a>Próximos passos

Depois de ter criado o diretor de serviço e lhe ter atribuído um papel no seu inquilino do Windows Virtual Desktop, pode usá-lo para criar uma piscina de anfitriões. Para saber mais sobre as piscinas de anfitriões, continue ao tutorial para criar uma piscina de anfitriões no Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Criar um conjunto de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
