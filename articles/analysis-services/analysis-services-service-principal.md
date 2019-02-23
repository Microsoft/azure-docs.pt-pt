---
title: Automatizar tarefas de Azure Analysis Services com principais de serviço | Documentos da Microsoft
description: Saiba como criar principais de serviço para automatizar tarefas de Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d87fe608b92dd70cb2dee78c817e0055445b7c70
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732530"
---
# <a name="automation-with-service-principals"></a>Automação com principais de serviço

Os principais de serviço são um recurso de aplicações do Azure Active Directory que pode criar no seu inquilino para efetuar operações automáticas de recursos e níveis de serviço. Eles são um tipo exclusivo de *identidade do usuário* com um ID de aplicação e a palavra-passe ou o certificado. Um principal de serviço tem apenas essas permissões necessárias para efetuar tarefas definidas para as funções e permissões para o qual está atribuída. 

No Analysis Services, os principais de serviço são utilizados com a automatização do Azure, modo autônomo do PowerShell, aplicativos de cliente personalizadas e aplicações web para automatizar tarefas comuns. Por exemplo, servidores de aprovisionamento, implementação de modelos, atualização de dados, dimensionamento, e colocar em pausa/retomar podem todos ser automatizado através da utilização de principais de serviço. As permissões estão atribuídas a principais de serviço através da associação de função, muito à semelhança de contas do Azure AD UPN regulares.

## <a name="create-service-principals"></a>Criar principais de serviço
 
Principais de serviço podem ser criados no portal do Azure ou através do PowerShell. Para saber mais, consulte:

[Criar principal de serviço - portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Store ativos de certificado e a credencial na automatização do Azure

Credenciais do principal de serviço e os certificados podem ser armazenados em segurança na automatização do Azure para operações de runbooks. Para saber mais, consulte:

[Ativos de credencial na automatização do Azure](../automation/automation-credentials.md)   
[Recursos de certificados na Automatização do Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicionar principais de serviço à função de administrador do servidor

Antes de poder utilizar um principal de serviço para operações de gestão de servidor do Analysis Services, tem de o adicionar à função de administradores do servidor. Para obter mais informações, consulte [adicionar um principal de serviço para a função de administrador de servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Principais de serviço em cadeias de ligação

AppID de principal de serviço e uma palavra-passe ou certificado pode ser utilizado na ligação de cadeias de caracteres semelhante a um UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Quando utilizar um principal de serviço para operações de gestão de recursos com o [Az.AnalysisServices](/powershell/module/az.analysisservices) módulo, utilize `Connect-AzAccount` cmdlet. Quando utilizar um principal de serviço para operações de servidor com o [SQLServer](https://www.powershellgallery.com/packages/SqlServer) módulo, utilize `Add-AzAnalysisServicesAccount` cmdlet. 

No exemplo seguinte, a appID e uma palavra-passe são utilizados para efetuar uma operação de atualização de base de dados do modelo:

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO e ADOMD 

Ao ligar-se com aplicações de cliente e aplicações web, [bibliotecas de cliente AMO e ADOMD](analysis-services-data-providers.md) versão 15.0.2 e pacotes instaláveis superior do NuGet de suporte principais de serviço nas cadeias de ligação com a seguinte sintaxe: `app:AppID` e a palavra-passe ou `cert:thumbprint`. 

No exemplo a seguir `appID` e um `password` são utilizados para efetuar uma operação de atualização de base de dados do modelo:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Passos Seguintes
[Inicie sessão com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Adicionar um principal de serviço para a função de administrador do servidor](analysis-services-addservprinc-admins.md)   
