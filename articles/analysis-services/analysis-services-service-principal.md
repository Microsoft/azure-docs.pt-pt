---
title: Automatizar tarefas de Azure Analysis Services com entidades de serviço | Microsoft Docs
description: Saiba como criar uma entidade de serviço para automatizar Azure Analysis Services tarefas administrativas.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5078fcc1ba5c581aca475025b286d0319d6024a6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572611"
---
# <a name="automation-with-service-principals"></a>Automatização com principais de serviço

Os principais de serviço são um recurso de aplicações do Azure Active Directory que pode criar no seu inquilino para efetuar operações automáticas de recursos e níveis de serviço. Eles são um tipo exclusivo de *identidade de usuário* com uma ID de aplicativo e senha ou certificado. Uma entidade de serviço tem apenas as permissões necessárias para executar tarefas definidas pelas funções e permissões para as quais ela está atribuída. 

Em Analysis Services, as entidades de serviço são usadas com a automação do Azure, o modo autônomo do PowerShell, aplicativos cliente personalizados e aplicativos Web para automatizar tarefas comuns. Por exemplo, o provisionamento de servidores, implantação de modelos, atualização de dados, escala vertical/redução e pausa/retomada pode ser automatizado usando entidades de serviço. As permissões são atribuídas às entidades de serviço por meio da Associação de função, assim como contas de UPN do Azure AD regulares.

O Analysis Services também dá suporte a operações executadas por identidades gerenciadas usando entidades de serviço. Para saber mais, confira [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) e [Serviços do Azure que dão suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).

## <a name="create-service-principals"></a>Criar principais de serviço
 
As entidades de serviço podem ser criadas no portal do Azure ou usando o PowerShell. Para saber mais, consulte:

[Criar entidade de serviço-portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Armazenar a credencial e os ativos de certificado na automação do Azure

As credenciais da entidade de serviço e os certificados podem ser armazenados com segurança na automação do Azure para operações de runbook. Para saber mais, consulte:

[Ativos de credencial na automação do Azure](../automation/automation-credentials.md)   
[Recursos de certificados na Automatização do Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicionar entidades de serviço à função de administrador do servidor

Antes de usar uma entidade de serviço para operações de gerenciamento do Analysis Services Server, você deve adicioná-la à função Administradores do servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Entidades de serviço em cadeias de conexão

A appID de entidade de serviço e a senha ou o certificado podem ser usados em cadeias de conexão de forma muito semelhante a um UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />usando o módulo AZ. AnalysisServices

Ao usar uma entidade de serviço para operações de gerenciamento de recursos com o módulo [AZ. AnalysisServices](/powershell/module/az.analysisservices) , use `Connect-AzAccount` cmdlet. 

No exemplo a seguir, appID e uma senha são usados para executar operações de plano de controle para sincronização para réplicas somente leitura e escala vertical/horizontal:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Usando o módulo do SQLServer

No exemplo a seguir, appID e uma senha são usados para executar uma operação de atualização de banco de dados modelo:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO e ADOMD 

Ao se conectar com aplicativos cliente e aplicativos Web, as [bibliotecas de cliente amo e ADOMD](analysis-services-data-providers.md) versão 15.0.2 e pacotes instaláveis mais altos do NuGet dão suporte a entidades de serviço em cadeias de conexão usando a seguinte sintaxe: `app:AppID` e senha ou @no_ _t_2_.`cert:thumbprint` 

No exemplo a seguir, `appID` e um `password` são usados para executar uma operação de atualização de banco de dados modelo:

```csharp
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

## <a name="next-steps"></a>Passos seguintes
[Entrar com Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md)   
