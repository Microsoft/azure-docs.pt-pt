---
title: Recursos, funções e controlo de acessos em Azure Application Insights | Microsoft Docs
description: Proprietários, colaboradores e leitores das ideias da sua organização.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589595"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, funções e controlo de acessos em Insights de Aplicações

Pode controlar quem leu e atualizou o acesso aos seus dados no Azure [Application Insights,][start]utilizando [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Atribua acesso aos utilizadores no **grupo de recursos ou subscrição** a que pertence o seu recurso de aplicação - não no próprio recurso. Atribua a **função de contribuinte de componente de Application Insights.** Isto garante um controlo uniforme do acesso a testes e alertas web, juntamente com o recurso da sua aplicação. [Saiba mais](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos e subscrições

Primeiro, algumas definições:

* **Recurso** - Uma instância de um serviço Microsoft Azure. O seu recurso Application Insights recolhe, analisa e exibe os dados de telemetria enviados da sua aplicação.  Outros tipos de recursos Azure incluem aplicações web, bases de dados e VMs.
  
    Para ver os seus recursos, abra o [portal Azure,][portal]inscreva-se e clique em Todos os Recursos. Para encontrar um recurso, digite parte do seu nome no campo do filtro.
  
    ![Lista de recursos da Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos**][group] - Todos os recursos pertencem a um grupo. Um grupo é uma forma conveniente de gerir recursos relacionados, particularmente para o controlo de acessos. Por exemplo, num grupo de recursos, pode colocar uma Web App, um recurso Application Insights para monitorizar a aplicação e um recurso de Armazenamento para manter os dados exportados.

* [**Subscrição**](https://portal.azure.com) - Para utilizar Insights de Aplicação ou outros recursos Azure, faça sedutado numa subscrição do Azure. Cada grupo de recursos pertence a uma subscrição da Azure, onde você escolhe o seu pacote de preço e, se é uma subscrição da organização, escolha os membros e suas permissões de acesso.
* [**Conta Microsoft**][account] - O nome de utilizador e a palavra-passe que utiliza para iniciar singing nas subscrições do Microsoft Azure, XBox Live, Outlook.com e outros serviços da Microsoft.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> Controlar o acesso no grupo de recursos

É importante entender que além do recurso que criou para a sua aplicação, existem também recursos ocultos separados para alertas e testes web. Estão ligados ao mesmo [grupo de recursos](#resource-group) que o seu recurso Application Insights. Você também pode ter colocado outros serviços Azure lá, como sites ou armazenamento.

## <a name="to-provide-access-to-another-user"></a>Para fornecer acesso a outro utilizador

Deve ter direitos de proprietário para a subscrição ou para o grupo de recursos.

O utilizador deve ter uma [Conta Microsoft,][account]ou acesso à sua [conta organizacional Microsoft.](../../active-directory/fundamentals/sign-up-organization.md) Pode fornecer acesso a indivíduos, e também a grupos de utilizadores definidos no Azure Ative Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navegue para o grupo de recursos ou diretamente para o próprio recurso

Escolha o controlo de **acesso (IAM)** no menu da esquerda.

![Screenshot do botão de controlo de acesso no portal Azure](./media/resources-roles-access-control/0001-access-control.png)

Selecione **Adicionar atribuição de função**

![Screenshot do menu de controlo access com botão de adicionar realçado a vermelho](./media/resources-roles-access-control/0002-add.png)

A **visualização de permissões Add** abaixo é principalmente específica para os recursos de Application Insights, se estivesse a visualizar as permissões de controlo de acesso de um nível mais elevado, como grupos de recursos, veria funções adicionais não centradas em Insights de Aplicação.

Para visualizar informações sobre todas as funções incorporadas no controlo de funções Azure, utilize o [conteúdo oficial de referência](../../role-based-access-control/built-in-roles.md).

![Screenshot da lista de funções do utilizador do controlo do acesso](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Selecionar uma função

Quando aplicável, ligamo-nos à documentação oficial de referência associada.

| Função | No grupo de recursos |
| --- | --- |
| [Proprietário](../../role-based-access-control/built-in-roles.md#owner) |Pode alterar qualquer coisa, incluindo o acesso ao utilizador. |
| [Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) |Pode editar qualquer coisa, incluindo todos os recursos. |
| [Colaborador do Componente de Insights de Aplicação](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Pode editar recursos de Insights de Aplicação. |
| [Leitor](../../role-based-access-control/built-in-roles.md#reader) |Pode ver, mas não mudar nada. |
| [Insights de aplicação Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Dá ao utilizador permissão para utilizar as funcionalidades de Debugger Snapshot Do Application Insights. Note que esta função não está incluída nem nas funções de Proprietário nem Contribuinte. |
| Colaborador de Gestão de Lançamento de Lançamento de Serviço Azure | Função contributiva para serviços implantados através do Serviço Azure Deploy. |
| [Purgador de dados](../../role-based-access-control/built-in-roles.md#data-purger) | Papel especial para a purga de dados pessoais. Consulte a nossa [orientação para obter dados pessoais](../logs/personal-data-mgmt.md) para mais informações.   |
| Administrador ExpressRoute | Pode criar exclusão e gestão de rotas expressas.|
| [Contribuidor do Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VM; leitura das chaves da conta de armazenamento para poder configurar a recolha de registos do Azure Storage; criar e configurar contas de automação; adição de soluções; e configurar diagnósticos Azure em todos os recursos da Azure.  |
| [Leitor do Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos Azure em todos os recursos do Azure. |
| leitor de mestres | Permite que um utilizador veja tudo, mas não faça alterações. |
| [Colaborador de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | Pode ler todos os dados de monitorização e atualizar as definições de monitorização.|
| [Editor de Métricas de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Permite publicar métricas contra os recursos da Azure. |
| [Leitor de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) | Pode ler todos os dados de monitorização. |
| Contribuidor de Política de Recursos (Pré-visualização) | Os utilizadores reenchitados da EA, com direitos de criar/modificar a política de recursos, criar bilhete de suporte e ler recursos/hierarquia.  |
| [Administrador de Acesso do Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) | Permite que um utilizador gere o acesso de outros utilizadores aos recursos da Azure.|
| [Colaborador do Site](../../role-based-access-control/built-in-roles.md#website-contributor) | Permite-lhe gerir websites (não planos web), mas não acesso aos mesmos..|

A 'edição' inclui a criação, eliminação e atualização:

* Recursos
* Testes web
* Alertas
* Exportação contínua

#### <a name="select-the-user"></a>Selecione o utilizador

Se o utilizador que deseja não estiver no diretório, pode convidar qualquer pessoa com uma conta Microsoft.
(Se utilizarem serviços como Outlook.com, OneDrive, Windows Phone ou XBox Live, têm uma conta Microsoft.)

## <a name="related-content"></a>Conteúdo relacionado

* [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Consulta PowerShell para determinar a adesão ao papel

Uma vez que certas funções podem estar ligadas a notificações e alertas de e-mail, pode ser útil ser capaz de gerar uma lista de utilizadores que pertencem a uma determinada função. Para ajudar a gerar este tipo de listas, oferecemos as seguintes consultas de amostra que podem ser ajustadas de acordo com as suas necessidades específicas:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Consulta de toda a subscrição para funções de Administração + Funções contributivas

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Consulta no contexto de um recurso específico de Insights de Aplicação para proprietários e contribuintes

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Consulta no contexto de um grupo de recursos específico para proprietários e contribuintes

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md
