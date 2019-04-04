---
title: Recursos, funções e controlo de acesso no Azure Application Insights | Documentos da Microsoft
description: Proprietários, contribuidores e leitores de informações da sua organização.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: mbullwin
ms.openlocfilehash: 213f4313e96638e4d94455be5f16aa3221d35b73
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905686"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, funções e controlo de acesso no Application Insights

Pode controlar quem tem ler e atualizar o acesso aos seus dados no Azure [Application Insights][start], utilizando [controlo de acesso baseado em funções no Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Atribuir acesso a utilizadores no **grupo de recursos ou subscrição** para que o recurso de aplicação pertence - não no próprio recurso. Atribuir a **contribuinte de componentes do Application Insights** função. Isto garante uniforme controlo de acesso para testes web e os alertas, juntamente com o seu recurso de aplicação. [Saiba mais](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos e subscrições

Primeiro, algumas definições:

* **Recurso** – um instância de um serviço do Microsoft Azure. Recurso do Application Insights recolhe, analisa e exibe os dados de telemetria enviados a partir da sua aplicação.  Outros tipos de recursos do Azure incluem aplicações web, bases de dados e VMs.
  
    Para ver os seus recursos, abra a [portal do Azure][portal], inicie sessão e clique em todos os recursos. Para localizar um recurso, escreva parte do respetivo nome no campo de filtro.
  
    ![Lista de recursos do Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos** ] [ group] -cada recurso pertence a um grupo. Um grupo é uma forma conveniente de gerir os recursos relacionados, particularmente para controlo de acesso. Por exemplo, no grupo de recursos pode colocar uma aplicação Web, um recurso do Application Insights para monitorizar a aplicação e um recurso de armazenamento para manter os dados exportados.

* [**Subscrição** ](https://portal.azure.com) - para utilizar o Application Insights ou outros recursos do Azure, inicie sessão para uma subscrição do Azure. Todos os grupos de recurso pertencem a uma subscrição do Azure, em que escolha o seu pacote de preços e, se se trata de uma subscrição de organização, escolha os membros e as respetivas permissões de acesso.
* [**Conta Microsoft** ] [ account] -o nome de utilizador e palavra-passe que utiliza para iniciar sessão no Microsoft Azure subscrições, XBox Live, Outlook.com e outros serviços Microsoft.

## <a name="access"></a> Controlar o acesso no grupo de recursos

É importante compreender que, além de recurso que criou para a sua aplicação, existem também os recursos ocultos separados para os alertas e testes web. Estão ligados ao mesmo [grupo de recursos](#resource-group) como seu aplicativo. Pode também ter colocar outros serviços do Azure aqui, como Web sites ou o armazenamento.

Para controlar o acesso a estes recursos, portanto, é recomendado para:

* Controlar o acesso na **grupo de recursos ou subscrição** nível.
* Atribuir a **contribuinte de componente do Application Insights** função aos utilizadores. Isto permite-lhes editar testes da web, alertas e recursos do Application Insights, sem fornecer acesso a quaisquer outros serviços no grupo.

## <a name="to-provide-access-to-another-user"></a>Para fornecer acesso a outro utilizador

Tem de ter direitos de proprietário para a subscrição ou o grupo de recursos.

O utilizador tem de ter uma [Account Microsoft][account], ou acesso a seus [Microsoft Account organizacional](../../active-directory/fundamentals/sign-up-organization.md). Pode fornecer acesso a indivíduos bem como para os grupos de utilizadores definidos no Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navegue para o grupo de recursos ou diretamente para o próprio recurso

Escolher **controlo de acesso (IAM)** no menu esquerdo.

![Botão de controle de captura de ecrã de acesso no portal do Azure](./media/resources-roles-access-control/0001-access-control.png)

Selecione **adicionar atribuição de função**

![Menu de controle de captura de ecrã de acesso com o botão Adicionar realçado a vermelho](./media/resources-roles-access-control/0002-add.png)

O **adicionar permissões** vista abaixo é principalmente específica para recursos do Application Insights, se estivesse a visualizar as permissões de controlo de acesso de um nível mais elevado, como grupos de recursos, veria não aplicativo adicional Funções centrada em informações.

Para ver informações sobre o controlo de acesso baseado em função do Azure todas as funções incorporadas utilizam o [conteúdo de referência oficial](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Lista de funções de utilizador de controle de captura de ecrã de acesso](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Selecionar uma função

Sempre que aplicável criamos uma ligação para a documentação de referência oficial associada.

| Função | No grupo de recursos |
| --- | --- |
| [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Pode alterar tudo, incluindo o acesso de utilizador. |
| [Contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Pode editar qualquer coisa, incluindo todos os recursos. |
| [Contribuinte de componentes de informações de aplicação](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Pode editar recursos do Application Insights, testes web e alertas. |
| [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Pode ver, mas não altera nada. |
| [Snapshot Debugger do Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Fornece a permissão de utilizador para utilizar as funcionalidades do Snapshot Debugger do Application Insights. Tenha em atenção que esta função está incluída nas funções de nem o proprietário nem contribuinte. |
| Contribuinte de Gestão de Versões de Implementação de Serviço do Azure | Função de Contribuidor para serviços de implantação através de implementação de serviço do Azure. |
| [Eliminação de Dados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Função especial para remover dados pessoais. Consulte nossos [documentação de orientação para os dados pessoais](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) para obter mais informações.   |
| Administrador do ExpressRoute | Pode criar o eliminar e gerir as rotas express.|
| [Contribuidor do Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Contribuidor do log Analytics pode ler todos os dados de monitorização e editar as definições de monitorização. Editar definições de monitorização inclui adicionar a extensão VM para VMs; ler as chaves de conta de armazenamento para poder configurar a recolha de registos do armazenamento do Azure; criar e configurar contas de automatização; Adicionar soluções; e configurar os diagnósticos do Azure em todos os recursos do Azure.  |
| [Leitor do Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | O Leitor do Log Analytics pode visualizar e procurar todos os dados de monitorização assim como visualizar as definições de monitorização, incluindo a visualização da configuração de diagnósticos Azure em todos os recursos do Azure. |
| masterreader | Permite que um utilizador ver tudo, mas não faça alterações. |
| [Contribuidor de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Pode ler todos os dados de monitorização e atualizar as definições de monitorização. |
| [Publicador de Métricas de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Permite a publicação de métricas em recursos do Azure. |
| [Leitor de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Pode ler todos os dados de monitorização. |
| Contribuinte da Política de Recursos (pré-visualização) | Os utilizadores substituídos de EA, com direitos para criar/modificar a política de recursos, criar pedido de suporte e recursos/hierarquia de leitura.  |
| [Administrador de Acesso de Utilizador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Permite que um utilizador gerir o acesso para outros utilizadores aos recursos do Azure.|
| [Contribuinte de Web site](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Permite-lhe gerir sites (não planos web), mas não aceder-lhes....|

"Editar" inclui criar, eliminar e atualizar:

* Recursos
* Testes Web
* Alertas
* Exportação contínua

#### <a name="select-the-user"></a>Selecione o utilizador

Se o utilizador que pretende não estiver no diretório, pode convidar qualquer pessoa com uma conta Microsoft.
(Se utilizam serviços como o Outlook.com, OneDrive, Windows Phone ou XBox Live, têm uma conta Microsoft.)

## <a name="related-content"></a>Conteúdo relacionado

* [Controlo de acesso no Azure baseado em funções](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Consulta de PowerShell para determinar a associação de função

Uma vez que algumas funções podem ser associadas a notificações e alertas de email pode ser útil ser capaz de gerar uma lista de utilizadores que pertencem a uma determinada função. Para ajudar a gerar esses tipos de listas que oferecemos os seguintes exemplos de consultas que podem ser ajustados de acordo com as suas necessidades específicas:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Consulta a subscrição completa para funções de administrador + funções de contribuinte

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Consultar dentro do contexto de um recurso específico do Application Insights para os proprietários e contribuintes

```powershell
$resourceGroup = “RGNAME”
$resourceName = “AppInsightsName”
$resourceType = “microsoft.insights/components”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Consultar dentro do contexto de um grupo de recursos específicos para os proprietários e contribuintes

```powershell
$resourceGroup = “RGNAME”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
