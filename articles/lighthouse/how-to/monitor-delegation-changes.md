---
title: Monitorize as mudanças de delegação no seu inquilino gerente
description: Saiba como monitorizar a atividade da delegação, desde os inquilinos dos clientes até ao seu inquilino gerente.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 82c41c392210e088c85af510b9698e0140f660e5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421911"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorize as mudanças de delegação no seu inquilino gerente

Como prestador de serviços, poderá querer estar ciente quando as subscrições de clientes ou grupos de recursos são delegados ao seu inquilino através da [gestão de recursos delegados do Azure,](../concepts/azure-delegated-resource-management.md)ou quando os recursos previamente delegados forem removidos.

No inquilino gerente, o log de [atividade azure](../../azure-monitor/platform/platform-logs-overview.md) acompanha a atividade de delegação ao nível do arrendatário. Esta atividade registada inclui quaisquer delegações adicionadas ou removidas de todos os inquilinos clientes.

Este tópico explica as permissões necessárias para monitorizar a atividade da delegação ao seu inquilino (em todos os seus clientes) e as melhores práticas para o fazer. Também inclui um script de amostra que mostra um método para consulta e reportagem sobre estes dados.

> [!IMPORTANT]
> Todos estes passos devem ser realizados no seu inquilino gerente, e não em qualquer inquilino de clientes.

## <a name="enable-access-to-tenant-level-data"></a>Permitir o acesso aos dados ao nível dos inquilinos

Para aceder aos dados do Registo de Atividade ao nível do arrendatário, deve ser atribuída uma conta à função incorporada do Leitor de [Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) no âmbito raiz (/). Esta atribuição deve ser realizada por um utilizador que tenha a função de Administrador Global com acesso adicional elevado.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevar o acesso a uma conta de Administrador Global

Para atribuir um papel no âmbito raiz (/), terá de ter o papel de Administrador Global com acesso elevado. Este acesso elevado só deve ser adicionado quando necessitar de fazer a atribuição do papel e, em seguida, removido quando terminar.

Para obter instruções detalhadas sobre a adição e remoção da elevação, consulte [O acesso elevado para gerir todas as subscrições e grupos de gestão do Azure.](../../role-based-access-control/elevate-access-global-admin.md)

Depois de elevar o seu acesso, a sua conta terá a função de Administrador de Acesso ao Utilizador em Azure no âmbito de raiz. Esta atribuição de funções permite-lhe visualizar todos os recursos e atribuir acesso em qualquer subscrição ou grupo de gestão no diretório, bem como fazer atribuições de papéis no âmbito de raiz. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Criar uma nova conta principal de serviço para aceder aos dados ao nível dos inquilinos

Uma vez elevado o seu acesso, pode atribuir as permissões apropriadas a uma conta para que possa consultar os dados de registo de atividade supérum ao nível do inquilino. Esta conta terá de ter o papel integrado do Leitor de [Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) no âmbito raiz do seu inquilino gerente.

> [!IMPORTANT]
> Conceder uma atribuição de funções no âmbito de raiz significa que as mesmas permissões se aplicarão a todos os recursos do inquilino.

Como este é um amplo nível de acesso, recomendamos que atribua esta função a uma conta principal de serviço, em vez de a um utilizador individual ou a um grupo. Além disso, recomendamos as seguintes boas práticas:

- [Crie uma nova conta principal](../../active-directory/develop/howto-create-service-principal-portal.md) de serviço a ser utilizada apenas para esta função, em vez de atribuir esta função a um principal de serviço existente utilizado para outra automatização.
- Certifique-se de que este diretor de serviço não tem acesso a quaisquer recursos de clientes delegados.
- [Utilize um certificado para autenticar](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) e [armazená-lo de forma segura no Cofre de Chaves Azure](../../key-vault/key-vault-best-practices.md).
- Limite os utilizadores que tenham acesso a agir em nome do diretor de serviço.

Utilize um dos seguintes métodos para efetuar as atribuições de âmbito de raiz.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Remover o acesso elevado à conta de Administrador Global

Depois de ter criado a sua conta principal de serviço e atribuído a função de Monitoring Reader no âmbito de raiz, certifique-se de [remover o acesso elevado](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) à conta De Administrador Global, uma vez que este nível de acesso deixará de ser necessário.

## <a name="query-the-activity-log"></a>Consulta do registo de atividade

Uma vez criado uma nova conta principal de serviço com monitorização do leitor acesso ao âmbito raiz do seu inquilino gerente, pode usá-lo para consultar e reportar sobre a atividade da delegação no seu inquilino. 

[Este script Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) pode ser usado para consultar o último dia de atividade e relatórios sobre quaisquer delegações adicionadas ou removidas (ou tentativas que não foram bem sucedidas). Consulta os dados do [Registo de Atividadedo Inquilino,](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) e depois constrói os seguintes valores para reportar sobre delegações adicionadas ou removidas:

- **DelegadoResourceId**: O ID do grupo de subscrição ou recursos delegado
- **CustomerTenantId**: O ID do inquilino do cliente
- **CustomerSubscriptionId**: O ID de subscrição que foi delegado ou que contém o grupo de recursos que foi delegado
- **Estatuto de Delegação de Clientes**: A alteração de estado do recurso delegado (bem sucedida ou falhada)
- **EventTimeStamp**: A data e a hora em que a alteração da delegação foi registada

Ao consultar estes dados, lembre-se:

- Se vários grupos de recursos forem delegados numa única implantação, as entradas separadas serão devolvidas para cada grupo de recursos.
- As alterações introduzidas numa delegação anterior (como a atualização da estrutura de autorização) serão registadas como uma delegação adicional.
- Como referido acima, uma conta deve ter o leitor de monitorização incorporado no âmbito raiz (/) para aceder a estes dados ao nível do inquilino.
- Pode utilizar estes dados nos seus próprios fluxos de trabalho e reporte. Por exemplo, pode utilizar a API de Coleção de [Dados HTTP (pré-visualização pública)](../../azure-monitor/platform/data-collector-api.md) para registar dados no Azure Monitor a partir de um cliente rest API, em seguida, usar [grupos](../../azure-monitor/platform/action-groups.md) de ação para criar notificações ou alertas.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Passos seguintes

- Saiba como embarcar nos clientes para a [gestão de recursos delegados do Azure.](../concepts/azure-delegated-resource-management.md)
- Conheça o [Azure Monitor](../../azure-monitor/index.yml) e o registo de [atividade sinuoso.](../../azure-monitor/platform/platform-logs-overview.md)
