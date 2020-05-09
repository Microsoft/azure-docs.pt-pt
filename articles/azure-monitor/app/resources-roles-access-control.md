---
title: Recursos, funções e controlo de acesso sintetizar em Insights de Aplicação Azure [ Microsoft Docs
description: Proprietários, colaboradores e leitores das ideias da sua organização.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ee14fee66e3aa6f4e8d999f6f98debe361387515
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628246"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, funções e controlo de acesso em Insights de Aplicação

Pode controlar quem leu e atualizou o acesso aos seus dados no Azure [Application Insights,][start]utilizando o [controlo de acesso baseado em Role no Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Atribuir acesso aos utilizadores do **grupo de recursos ou subscrição** a que pertence o seu recurso de aplicação - não no próprio recurso. Atribuir a função de colaborador da **componente Application Insights.** Isto garante um controlo uniforme do acesso aos testes e alertas web juntamente com o seu recurso de aplicação. [Saiba mais](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos e subscrições

Primeiro, algumas definições:

* **Recurso** - Uma instância de um serviço Microsoft Azure. O seu recurso Application Insights recolhe, analisa e exibe os dados de telemetria enviados a partir da sua aplicação.  Outros tipos de recursos Do Azure incluem aplicações web, bases de dados e VMs.
  
    Para ver os seus recursos, abra o [portal Azure,][portal]inscreva-se e clique em Todos os Recursos. Para encontrar um recurso, digite parte do seu nome no campo de filtro.
  
    ![Lista de recursos do Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo**][group] de recursos - Cada recurso pertence a um grupo. Um grupo é uma forma conveniente de gerir recursos relacionados, particularmente para o controlo de acessos. Por exemplo, num grupo de recursos, você poderia colocar uma Web App, um recurso Application Insights para monitorizar a app, e um recurso de Armazenamento para manter os dados exportados.

* [**Subscrição**](https://portal.azure.com) - Para utilizar insights de aplicação ou outros recursos Azure, você inscreveu-se numa subscrição Azure. Cada grupo de recursos pertence a uma subscrição Azure, onde você escolhe o seu pacote de preços e, se for uma subscrição de organização, escolha os membros e suas permissões de acesso.
* [**Conta Microsoft**][account] - O nome de utilizador e a palavra-passe que utiliza para iniciar sessão nas subscrições do Microsoft Azure, XBox Live, Outlook.com e outros serviços da Microsoft.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Controlar o acesso no grupo de recursos

É importante entender que além do recurso que criou para a sua aplicação, existem também recursos escondidos separados para alertas e testes web. Estão ligados ao mesmo grupo de [recursos](#resource-group) que o seu recurso Application Insights. Você também pode ter colocado outros serviços Azure lá, como sites ou armazenamento.

## <a name="to-provide-access-to-another-user"></a>Para fornecer acesso a outro utilizador

Deve ter direitos de proprietário para a subscrição ou o grupo de recursos.

O utilizador deve ter uma [Conta Microsoft,][account]ou aceder à sua [conta organizacional microsoft](../../active-directory/fundamentals/sign-up-organization.md). Pode fornecer acesso a indivíduos, e também a grupos de utilizadores definidos no Diretório Ativo Azure.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navegue para o grupo de recursos ou diretamente para o próprio recurso

Escolha o controlo de **acesso (IAM)** do menu à esquerda.

![Screenshot do botão de controlo de acesso no portal Azure](./media/resources-roles-access-control/0001-access-control.png)

**Selecione Adicionar atribuição de funções**

![Screenshot do menu de controlo de acesso com botão adicionado realçado a vermelho](./media/resources-roles-access-control/0002-add.png)

A visão de **permissões Add** abaixo é especificada principalmente para os recursos de Application Insights, se você estivesse vendo as permissões de controlo de acesso de um nível mais alto como grupos de recursos, você veria funções adicionais não centradas em Insights de Aplicação.

Para visualizar informações sobre todas as funções de controlo de acesso baseados em funções azure, utilize o [conteúdo oficial](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)de referência .

![Screenshot da lista de funções do utilizador de controlo de acesso](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Selecionar uma função

Quando aplicável, ligamos à documentação oficial de referência associada.

| Função | No grupo de recursos |
| --- | --- |
| [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Pode alterar qualquer coisa, incluindo o acesso ao utilizador. |
| [Contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Pode editar qualquer coisa, incluindo todos os recursos. |
| [Contribuidor de componentes de insights de aplicação](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Pode editar recursos da Application Insights. |
| [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Pode ver, mas não mudar nada. |
| [Insights de aplicação Snapshot Debugger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Dá ao utilizador permissão para utilizar as funcionalidades Debugger Snapshot Snapshot. Note que este papel não está incluído nem nas funções de Proprietário nem Colaborador. |
| Contribuidor de Gestão de Lançamento de Serviço Azure | Papel contributivo para serviços de implantação através do Azure Service Deploy. |
| [Purgador de dados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Papel especial para a purga de dados pessoais. Consulte a nossa [orientação para obter mais informações.](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data)   |
| Administrador ExpressRoute | Pode criar eliminar e gerir rotas expressas.|
| [Contribuidor do Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | O Log Analytics Contributor pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs; chaves da conta de armazenamento de leitura para poder configurar a recolha de registos do Armazenamento Azure; criação e configuração de contas de Automação; adicionando soluções; e configurar diagnósticos Azure em todos os recursos do Azure.  |
| [Leitor do Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | O Log Analytics Reader pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração dos diagnósticos do Azure em todos os recursos do Azure. |
| leitor de mestres | Permite que um utilizador veja tudo, mas não faça alterações. |
| [Colaborador de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Pode ler todos os dados de monitorização e configurações de monitorização da atualização.|
| [Editor de Métricas de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Permite a publicação de métricas contra os recursos do Azure. |
| [Leitor de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Pode ler todos os dados de monitorização. |
| Contribuinte de Política de Recursos (Pré-visualização) | Utilizadores recheados de EA, com direitos de criar/modificar a política de recursos, criar bilhetede suporte e ler recursos/hierarquia.  |
| [Administrador de Acesso do Utilizador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Permite a um utilizador gerir o acesso de outros utilizadores aos recursos do Azure.|
| [Colaborador do Site](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Permite-lhe gerir websites (não planos web), mas não ter acesso a eles..|

'Editar' inclui a criação, aeliminar e atualizar:

* Recursos
* Testes web
* Alertas
* Exportação contínua

#### <a name="select-the-user"></a>Selecione o utilizador

Se o utilizador que deseja não estiver no diretório, pode convidar qualquer pessoa com uma conta Microsoft.
(Se utilizarem serviços como Outlook.com, OneDrive, Windows Phone ou XBox Live, têm uma conta Microsoft.)

## <a name="related-content"></a>Conteúdo relacionado

* [Controlo de acesso baseado em funções em Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Consulta powerShell para determinar a adesão ao papel

Uma vez que determinadas funções podem estar ligadas a notificações e alertas de e-mail, pode ser útil ser capaz de gerar uma lista de utilizadores que pertencem a uma determinada função. Para ajudar a gerar este tipo de listas oferecemos as seguintes consultas de amostra que podem ser ajustadas para se adaptar às suas necessidades específicas:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Consulta inteira de subscrição para funções da Admin + Funções contributivas

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

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Consulta no contexto de um grupo específico de recursos para proprietários e contribuintes

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
