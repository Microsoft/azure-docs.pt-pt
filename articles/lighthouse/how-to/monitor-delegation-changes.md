---
title: Monitorize as mudanças de delegação no seu inquilino gerente
description: Saiba como monitorizar a atividade da delegação desde os inquilinos do cliente até ao seu inquilino gerente.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 9fdf47df4ac37fec44cf53b565b7fe1411540793
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089427"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorize as mudanças de delegação no seu inquilino gerente

Como prestador de serviços, poderá querer estar ciente de que as subscrições de clientes ou grupos de recursos são delegados ao seu inquilino através [do Farol Azure,](../overview.md)ou quando os recursos previamente delegados são removidos.

No inquilino gerente, o [log de atividades Azure](../../azure-monitor/platform/platform-logs-overview.md) acompanha a atividade da delegação ao nível do inquilino. Esta atividade registada inclui quaisquer delegações adicionadas ou removidas de todos os inquilinos do cliente.

Este tópico explica as permissões necessárias para monitorizar a atividade da delegação ao seu inquilino (em todos os seus clientes). Também inclui um guião de amostra que mostra um método para consulta e relatório sobre estes dados.

> [!IMPORTANT]
> Todos estes passos devem ser realizados no seu inquilino gerente, e não em qualquer inquilino de cliente.
>
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar os mesmos processos.

## <a name="enable-access-to-tenant-level-data"></a>Permitir o acesso aos dados ao nível do inquilino

Para aceder aos dados do Registo de Atividade ao nível do inquilino, deve ser atribuída uma conta à função incorporada do [Leitor de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure no âmbito raiz (/). Esta atribuição deve ser executada por um utilizador que tenha a função de Administrador Global com acesso adicional elevado.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevar o acesso a uma conta de Administrador Global

Para atribuir uma função no âmbito raiz (/), terá de ter a função de Administrador Global com acesso elevado. Este acesso elevado só deve ser adicionado quando precisar de fazer a tarefa de função e, em seguida, removido quando terminar.

Para obter instruções detalhadas sobre a adição e remoção da elevação, consulte [o acesso elevate para gerir todas as assinaturas e grupos de gestão da Azure.](../../role-based-access-control/elevate-access-global-admin.md)

Depois de elevar o seu acesso, a sua conta terá a função de Administrador de Acesso ao Utilizador em Azure no âmbito raiz. Esta atribuição de funções permite-lhe visualizar todos os recursos e atribuir acesso em qualquer grupo de subscrição ou gestão no diretório, bem como fazer atribuições de papéis no âmbito raiz.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Atribuir a função de leitor de monitorização no âmbito raiz

Uma vez elevado o seu acesso, pode atribuir as permissões apropriadas a uma conta para que possa consultar os dados de registo de atividade ao nível do inquilino. Esta conta terá de ter a função de [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure atribuída no âmbito raiz do seu inquilino gerente.

> [!IMPORTANT]
> Conceder uma atribuição de papel no âmbito raiz significa que as mesmas permissões se aplicarão a todos os recursos do arrendatário. Por se trata de um amplo nível de acesso, poderá [pretender atribuir esta função a uma conta principal de serviço e utilizar essa conta para consultar dados.](#use-a-service-principal-account-to-query-the-activity-log) Também pode atribuir a função de Leitor de Monitorização no âmbito raiz a utilizadores individuais ou a grupos de utilizadores para que possam [ver informações da delegação diretamente no portal Azure](#view-delegation-changes-in-the-azure-portal). Se o fizer, esteja ciente de que se trata de um amplo nível de acesso que deve limitar-se ao menor número possível de utilizadores.

Utilize um dos seguintes métodos para fazer a atribuição do âmbito da raiz.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Remover acesso elevado para a conta de Administrador Global

Depois de atribuir a função de Monitoring Reader no âmbito raiz à conta desejada, certifique-se de [remover o acesso elevado](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) para a conta de Administrador Global, uma vez que este nível de acesso deixará de ser necessário.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Ver alterações de delegação no portal Azure

Os utilizadores a quem foi atribuída a função de Leitor de Monitorização no âmbito raiz podem ver as alterações da delegação diretamente no portal Azure.

1. Navegue na página **Dos Meus clientes** e, em seguida, selecione registo de **atividade** a partir do menu de navegação à esquerda.
1. Certifique-se de que a **Atividade do Diretório** é selecionada no filtro perto da parte superior do ecrã.

Aparecerá uma lista de alterações de delegação. Pode selecionar **colunas Editar** para mostrar ou ocultar o Estado , **Categoria** **evento**, **Tempo**, **Hora, Assinatura,** **Evento iniciado por,** Grupo **de Recursos,** **Tipo de Recurso** e Valores de **Recursos.** 

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Screenshot das mudanças de delegação no portal Azure.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Utilize uma conta principal de serviço para consultar o registo de atividades

Uma vez que a função de Monitoring Reader no âmbito raiz é um nível de acesso tão amplo, pode querer atribuir a função a uma conta principal de serviço e utilizar essa conta para consultar dados utilizando o script abaixo.

> [!IMPORTANT]
> Atualmente, os inquilinos com uma grande quantidade de atividade de delegação podem ter erros ao consultar estes dados.

Ao utilizar uma conta principal de serviço para consultar o registo de atividades, recomendamos as seguintes boas práticas:

- [Crie uma nova conta principal](../../active-directory/develop/howto-create-service-principal-portal.md) de serviço para ser utilizada apenas para esta função, em vez de atribuir esta função a um principal de serviço existente utilizado para outra automatização.
- Certifique-se de que este diretor de serviço não tem acesso a quaisquer recursos delegados do cliente.
- [Utilize um certificado para autenticar](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) e [armazená-lo de forma segura no Cofre da Chave Azure](../../key-vault/general/security-overview.md).
- Limitar os utilizadores que tenham acesso a agir em nome do diretor de serviço.

Uma vez criada uma nova conta principal de serviço com o monitor de acesso do Leitor ao âmbito de raiz do seu inquilino gerente, pode usá-la para consultar e reportar sobre a atividade da delegação no seu inquilino.

[Este script Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) pode ser usado para consultar o último dia de atividade e relatórios sobre quaisquer delegações adicionadas ou removidas (ou tentativas que não foram bem sucedidas). Questiona os dados do [Registo de Atividades](/rest/api/monitor/TenantActivityLogs/List) do Arrendatário e, em seguida, constrói os seguintes valores para reportar sobre as delegações adicionadas ou removidas:

- **DelegadoResourceId**: O ID do grupo de subscrição ou recursos delegados
- **CustomerTenantId**: O ID do inquilino do cliente
- **CustomerSubscriptionId**: O ID de subscrição que foi delegado ou que contém o grupo de recursos que foi delegado
- **CustomerDelegationStatus**: A alteração de estado do recurso delegado (bem sucedido ou falhado)
- **EventTimeStamp**: A data e a hora em que a mudança de delegação foi registada

Ao consultar estes dados, tenha em mente:

- Se vários grupos de recursos forem delegados numa única implantação, serão devolvidas entradas separadas para cada grupo de recursos.
- As alterações introduzidas numa delegação anterior (como a atualização da estrutura de permissões) serão registadas como uma delegação adicional.
- Como referido acima, uma conta deve ter o monitor azure papel incorporado no âmbito raiz (/) para aceder a estes dados ao nível do inquilino.
- Pode utilizar estes dados nos seus próprios fluxos de trabalho e reporte. Por exemplo, pode utilizar a [API do Retorno de Dados HTTP (pré-visualização pública)](../../azure-monitor/platform/data-collector-api.md) para registar dados no Azure Monitor a partir de um cliente REST API e, em seguida, utilizar [grupos](../../azure-monitor/platform/action-groups.md) de ação para criar notificações ou alertas.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como embarcar clientes no [Farol Azure.](../concepts/azure-delegated-resource-management.md)
- Saiba mais sobre [o Azure Monitor](../../azure-monitor/index.yml) e o [registo de atividades Azure](../../azure-monitor/platform/platform-logs-overview.md).
- Reveja os [registos de atividade por livro de amostras de domínio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) para aprender a exibir registos de Atividade Azure em subscrições com a opção de filtrar por nome de domínio.
