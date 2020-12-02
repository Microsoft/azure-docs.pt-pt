---
title: Automatizar tarefas dos Serviços de Análise da Azure com os principais serviços Microsoft Docs
description: Saiba como criar um principal serviço para automatizar tarefas administrativas dos Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b04b9ababfe0e4c2a60d14044b9d3ee120837dc5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491048"
---
# <a name="automation-with-service-principals"></a>Automatização com principais de serviço

Os principais de serviço são um recurso de aplicações do Azure Active Directory que pode criar no seu inquilino para efetuar operações automáticas de recursos e níveis de serviço. São um tipo único de identidade de *utilizador* com identificação de aplicação e senha ou certificado. Um diretor de serviço tem apenas as permissões necessárias para executar tarefas definidas pelas funções e permissões para as quais é atribuído. 

Nos Serviços de Análise, os principais serviços são utilizados com a Azure Automation, o modo PowerShell sem supervisão, aplicações personalizadas para clientes e aplicações web para automatizar tarefas comuns. Por exemplo, o fornecimento de servidores, modelos de implementação, atualização de dados, escala para cima/para baixo e pausa/currículo podem ser automatizados utilizando os principais serviços. As permissões são atribuídas aos principais de serviço através da adesão à função, à semelhança das contas regulares da Azure AD UPN.

Os Serviços de Análise também apoiam operações realizadas por identidades geridas utilizando os princípios de serviço. Para saber mais, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) e [serviços Azure que suportam a autenticação AD AZure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)    

## <a name="create-service-principals"></a>Criar principais de serviço
 
Os principais de serviço podem ser criados no portal Azure ou utilizando o PowerShell. Para saber mais, consulte:

[Criar um principal de serviço – portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar principal de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Armazenar ativos de credencial e certificados na Azure Automation

As credenciais e certificados principais do serviço podem ser armazenados de forma segura na Azure Automation para operações de runbook. Para saber mais, consulte:

[Ativos credenciais na Azure Automation](../automation/shared-resources/credentials.md)   
[Recursos de certificados na Automatização do Azure](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicione os principais de serviço ao papel de administração do servidor

Antes de poder utilizar um principal de serviço para operações de gestão de servidores de serviços de análise, deve adicioná-lo à função de administrador do servidor. Os princípios de serviço devem ser adicionados diretamente à função de administrador do servidor. Adicionar um principal de serviço a um grupo de segurança e, em seguida, adicionar que o grupo de segurança à função de administrador do servidor não é suportado. Para saber mais, consulte [Adicionar um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Principais de serviço em cadeias de ligação

O appID principal do serviço e a palavra-passe ou certificado podem ser usados em cadeias de ligação muito iguais a uma UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Utilizando o módulo Az.AnalysisServices

Quando utilizar um principal de serviço para operações de gestão de recursos com o módulo [Az.AnalysisServices,](/powershell/module/az.analysisservices)  utilize `Connect-AzAccount` o cmdlet. 

No exemplo seguinte, o appID e uma palavra-passe são utilizados para executar operações de plano de controlo para sincronização para réplicas apenas de leitura e escalar/eliminar:

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

#### <a name="using-sqlserver-module"></a>Utilizar o módulo SQLServer

No exemplo seguinte, o appID e uma palavra-passe são usados para realizar uma operação de atualização de base de dados modelo:

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

Ao ligar-se a aplicações Web e aplicações cliente, os pacotes instaláveis das [bibliotecas cliente AMO e ADOMD](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (versão 15.0.2 e superior) do NuGet suportam principais de serviço em cadeias de ligação através da seguinte sintaxe: `app:AppID` e palavra-passe ou `cert:thumbprint`. 

No seguinte exemplo, `appID` e `password` são utilizados para executar uma operação de atualização da base de dados modelo:

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

## <a name="next-steps"></a>Próximos passos
[Inscreva-se com a Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Atualizar com o Logic Apps](analysis-services-refresh-logic-app.md)  
[Atualizar com a Automatização do Azure](analysis-services-refresh-azure-automation.md)  
[Adicione um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md)  
[Automatizar o espaço de trabalho power BI Premium e as tarefas de conjunto de dados com os principais serviços](/power-bi/admin/service-premium-service-principal)