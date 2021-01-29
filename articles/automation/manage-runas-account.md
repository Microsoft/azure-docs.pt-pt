---
title: Gerir uma execução de automação Azure Como conta
description: Este artigo diz como gerir a sua conta Run As com o PowerShell ou a partir do portal Azure.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f9e99318c526bb935d0e035fdbf59874249390da
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050944"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerir uma execução de automação Azure Como conta

Executar Como contas na Azure Automation fornecem autenticação para gerir recursos no Azure Resource Manager ou no modelo de implementação Azure Classic utilizando runbooks automation e outras funcionalidades de Automação. Este artigo fornece orientações sobre como gerir uma conta Run As ou Classic Run As.

Para saber mais sobre a autenticação da conta Azure Automation e orientação relacionada com cenários de automatização de processos, consulte [a visão geral da autenticação da Conta de Automação](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovar um certificado auto-assinado

O certificado auto-assinado que criou para a conta Run As expira um ano a contar da data de criação. Em algum momento antes da sua conta run Como expira, você deve renovar o certificado. Pode renová-lo a qualquer momento antes que expire.

Quando renova o certificado auto-assinado, o certificado válido atual é mantido para garantir que quaisquer livros que estejam em fila ou que estejam a funcionar ativamente, e que autentem com a conta Run As, não sejam afetados negativamente. O certificado permanece válido até à data de expiração.

>[!NOTE]
>Se acha que a conta Run As foi comprometida, pode eliminar e recriar o certificado auto-assinado.

>[!NOTE]
>Se configurar a sua conta Run As para utilizar um certificado emitido pela autoridade de certificados da empresa e utilizar a opção de renovar uma opção de certificado auto-assinado, o certificado da empresa é substituído por um certificado auto-assinado.

Utilize os seguintes passos para renovar o certificado auto-assinado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Aceda à sua conta Demômes automática e selecione **Executar Como Contas** na secção de definições de conta.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Painel de propriedades de conta de automação.":::

1. Na página **'Executar Como Contas'** propriedades, selecione **'Executar como Conta'** ou **'S'S's Classic Como Conta,** dependendo da conta para a qual necessita renovar o certificado.

1. Na página **Propriedades** para a conta selecionada, selecione **o certificado Renovar.**

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Renovar certificado para executar como conta.":::

1. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Grant Run Como permissões de conta em outras subscrições

A Azure Automation suporta usar uma única conta de Automação a partir de uma subscrição e executar runbooks contra recursos do Azure Resource Manager através de várias subscrições. Esta configuração não suporta o modelo de implementação Azure Classic.

Atribui o gestor de conta Run As the [Contributor](../role-based-access-control/built-in-roles.md#contributor) na outra subscrição, ou permissões mais restritivas. Para obter mais informações, consulte o controlo de acesso baseado em [funções](automation-role-based-access-control.md) na Azure Automation. Para atribuir a conta Run As à função na outra subscrição, a conta de utilizador que executa esta tarefa tem de ser membro da função **Proprietário** nessa subscrição.

> [!NOTE]
> Esta configuração suporta apenas várias subscrições de uma organização usando um inquilino AD comum.

Antes de conceder as permissões de conta Run As, tem de primeiro anotar o nome de exibição do titular do serviço para atribuir.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na sua conta de Automação, selecione **Executar como contas** em **Definições de Conta**.
1. Selecione **Azure Run as Account**.
1. Copie ou note o valor do **Nome de Exibição** na página **Azure Run As Account.**

Para obter etapas detalhadas sobre como adicionar atribuições de funções, confira os seguintes artigos dependendo do método que pretende utilizar.

* [Adicione a atribuição de funções Azure do portal Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicione a atribuição de funções Azure usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Adicione a atribuição de funções Azure usando o CLI Azure](../role-based-access-control/role-assignments-cli.md)
* [Adicionar atribuição de função Azure usando a API REST](..//role-based-access-control/role-assignments-rest.md)

Depois de atribuir a conta Run As à função, no seu runbook `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` especificar para definir o contexto de subscrição a utilizar. Para mais informações, consulte [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Prazo executado como permissões de conta

Para controlar a segmentação da Automação contra recursos em Azure, pode executar o [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) script. Este script altera o seu run como principal do serviço de conta existente para criar e usar uma definição de função personalizada. O papel tem permissões para todos os recursos, exceto [o Key Vault.](../key-vault/index.yml)

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1,** os livros que acedem ao Key Vault através da utilização de Run As contas já não funcionam. Antes de executar o script, deve rever os runbooks na sua conta para chamadas para Azure Key Vault. Para permitir o acesso ao Key Vault a partir de runbooks da Azure Automation, tem de [adicionar a conta Run As às permissões do Key Vault](#add-permissions-to-key-vault).

Se precisar de restringir ainda mais o que o responsável de serviço pode fazer, pode adicionar outros tipos de recursos ao `NotActions` elemento da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute/*` . Se adicionar este tipo de recurso `NotActions` para a definição de função, a função não será capaz de aceder a qualquer recurso Compute. Para saber mais sobre definições de funções, consulte [as definições de funções para os recursos da Azure.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Pode determinar se o titular do serviço utilizado pela sua conta Run As atribuiu a função **Contribuinte** ou uma função personalizada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Aceda à sua conta Demômes automática e selecione **Executar Como Contas** na secção de definições de conta.
1. Selecione **Azure Run as Account**.
1. Selecione **Função** para localizar a definição de função que está a ser usada.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Verifique a função 'Executar como conta'." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Também pode determinar a definição de função utilizada pelas contas Run As para várias subscrições ou contas de Automação. Faça-o utilizando [](https://aka.ms/AA5hug5) oCheck-AutomationRunAsAccountRoleAssignments.ps1script na PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões ao Cofre de Chaves

Pode permitir que a Azure Automation verifique se o Key Vault e o seu responsável pelo serviço de conta Run As estão a utilizar uma definição de função personalizada. Tens de o fazer:

* Conceda permissões ao Key Vault.
* Desa parte da política de acesso.

Pode utilizar [ o ](https://aka.ms/AA5hugb)Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1script na PowerShell Gallery para conceder a sua execução como permissões de conta para Key Vault. Consulte [a política de acesso do Cofre de Chaves](../key-vault/general/assign-access-policy-powershell.md) para obter mais detalhes sobre a definição de permissões no Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração errada para run As accounts

Alguns itens de configuração necessários para uma execução como ou corrida clássica Como a conta pode ter sido eliminada ou criada indevidamente durante a configuração inicial. Possíveis casos de má configuração incluem:

* Recurso de certificado
* Recurso de ligação
* Executar Como conta removida da função contribuinte
* Principal de serviço ou aplicação no Azure AD

Para tais casos de configuração errada, a conta Automation deteta as alterações e apresenta um estado de *Incompleto* no painel de propriedades run As accounts para a conta.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Execução incompleta Como configuração de conta.":::

Quando seleciona a conta Run As, o painel de propriedades da conta apresenta a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Pode resolver rapidamente estes problemas de conta run como [eliminando](delete-run-as-account.md) e [recriando](create-run-as-account.md) a conta Run As.

## <a name="next-steps"></a>Passos seguintes

* [Objetos de aplicação e objetos principais de serviço.](../active-directory/develop/app-objects-and-service-principals.md)
* [Visão geral dos certificados para serviços de nuvem Azure](../cloud-services/cloud-services-certs-create.md).
* Para criar ou recriar uma conta Run As, consulte [Criar uma conta Run As](create-run-as-account.md).
* Se já não precisar de utilizar uma conta Run As, consulte [eliminar uma conta 'Executar'.](delete-run-as-account.md)