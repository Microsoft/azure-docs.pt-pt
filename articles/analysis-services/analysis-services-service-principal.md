---
title: Automatizar tarefas dos Serviços de Análise Azure com os principais de serviços Microsoft Docs
description: Aprenda a criar um principal de serviço para automatizar tarefas administrativas dos Serviços de Análise azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266146"
---
# <a name="automation-with-service-principals"></a>Automatização com principais de serviço

Os principais de serviço são um recurso de aplicações do Azure Active Directory que pode criar no seu inquilino para efetuar operações automáticas de recursos e níveis de serviço. São um tipo único de identidade de *utilizador* com identificação de aplicação e senha ou certificado. Um diretor de serviço tem apenas as permissões necessárias para executar tarefas definidas pelas funções e permissões para as quais é atribuído. 

Nos Serviços de Análise, os principais de serviço são utilizados com a Automação Azure, o modo PowerShell sem supervisão, aplicações personalizadas do cliente e aplicações web para automatizar tarefas comuns. Por exemplo, o fornecimento de servidores, a implementação de modelos, a atualização de dados, a escala para cima/para baixo e a pausa/retoma podem ser todos automatizados utilizando os diretores de serviço. As permissões são atribuídas aos principais de serviço através da adesão a papéis, tal como as contas regulares da Azure AD UPN.

Os Serviços de Análise também apoiam as operações realizadas por identidades geridas utilizando os principais de serviço. Para saber mais, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) e [serviços Azure que suportam a autenticação Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)  

## <a name="create-service-principals"></a>Criar principais de serviço
 
Os principais de serviço podem ser criados no portal Azure ou utilizando o PowerShell. Para saber mais, consulte:

[Criar o diretor de serviço - portal Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Armazenar ativos credenciais e certificados na Azure Automation

As principais credenciais e certificados de serviço podem ser armazenados de forma segura na Azure Automation para operações de livro de reprodução. Para saber mais, consulte:

[Ativos credenciais na  de Automação Azure](../automation/automation-credentials.md)  
[Recursos de certificados na Automatização do Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicionar diretores de serviço à função de administração do servidor

Antes de poder utilizar um diretor de serviço para operações de gestão de servidores de Serviços de Análise, deve adicioná-lo à função de administrador do servidor. Para saber mais, consulte [Adicionar um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Principais de serviço nas cordas de ligação

O aplicativo principal de serviço e a palavra-passe ou certificado podem ser utilizados em cadeias de ligação, tal como uma UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />Usando o módulo Az.AnalysisServices

Quando utilizar um diretor de serviço para operações de gestão de recursos com o módulo [Az.AnalysisServices,](/powershell/module/az.analysisservices) utilize `Connect-AzAccount` cmdlet. 

No exemplo seguinte, o appID e uma palavra-passe são utilizados para realizar operações de plano de controlo para sincronização a réplicas apenas de leitura e escala para cima/para fora:

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

#### <a name="using-sqlserver-module"></a>Usando o módulo SQLServer

No exemplo seguinte, o appID e uma palavra-passe são utilizados para executar uma operação de atualização de base de dados modelo:

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

Ao conectar-se com aplicações de clientes e aplicações web, bibliotecas de [clientes AMO e ADOMD](analysis-services-data-providers.md) versão 15.0.2 e pacotes instalados mais altos dos principais de suporte do NuGet nas cadeias de ligação utilizando a seguinte sintaxe: `app:AppID` e senha ou `cert:thumbprint`. 

No exemplo seguinte, `appID` e um `password` são utilizados para executar uma operação de atualização de base de dados modelo:

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

## <a name="next-steps"></a>Passos Seguintes
[Inscreva-se na  Da PowerShell da Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps)  
[Adicione um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md)   
