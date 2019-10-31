---
title: Recursos, funções e controle de acesso no Aplicativo Azure insights | Microsoft Docs
description: Proprietários, colaboradores e leitores das informações da sua organização.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/14/2019
ms.openlocfilehash: cdc534325fd693dd34f2dc25c9953cd40ca96163
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162308"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, funções e controle de acesso no Application Insights

Você pode controlar quem tem acesso de leitura e atualização aos seus dados no Azure [Application insights][start], usando o [controle de acesso baseado em função no Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Atribua acesso a usuários no **grupo de recursos ou na assinatura** à qual o recurso de aplicativo pertence, não no próprio recurso. Atribua a função **colaborador de componente Application insights** . Isso garante o controle uniforme do acesso a testes da Web e alertas junto com o recurso do aplicativo. [Saiba mais](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos e assinaturas

Primeiro, algumas definições:

* **Recurso** -uma instância de um serviço de Microsoft Azure. O recurso Application Insights coleta, analisa e exibe os dados de telemetria enviados do seu aplicativo.  Outros tipos de recursos do Azure incluem aplicativos Web, bancos de dados e VMs.
  
    Para ver seus recursos, abra o [portal do Azure][portal], entre e clique em todos os recursos. Para localizar um recurso, digite parte de seu nome no campo de filtro.
  
    ![Lista de recursos do Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos**][group] -cada recurso pertence a um grupo. Um grupo é uma maneira conveniente de gerenciar recursos relacionados, especialmente para o controle de acesso. Por exemplo, em um grupo de recursos, você pode colocar um aplicativo Web, um Application Insights recurso para monitorar o aplicativo e um recurso de armazenamento para manter os dados exportados.

* [**Assinatura**](https://portal.azure.com) -para usar Application insights ou outros recursos do Azure, você entra em uma assinatura do Azure. Cada grupo de recursos pertence a uma assinatura do Azure, na qual você escolhe seu pacote de preço e, se for uma assinatura de organização, escolhe os membros e suas permissões de acesso.
* [**Conta Microsoft**][account] -o nome de usuário e a senha que você usa para entrar em assinaturas do Microsoft Azure, Xbox Live, Outlook.com e outros serviços da Microsoft.

## <a name="access"></a>Controlar o acesso no grupo de recursos

É importante entender que, além do recurso que você criou para seu aplicativo, também há recursos ocultos separados para alertas e testes da Web. Eles são anexados ao mesmo [grupo de recursos](#resource-group) que o recurso de Application insights. Você também pode ter colocado outros serviços do Azure ali, como sites ou armazenamento.

Para controlar o acesso a esses recursos, é recomendável:

* Controle o acesso no nível do **grupo de recursos ou da assinatura** .
* Atribua a função **colaborador de componente Application insights** aos usuários. Isso permite que eles editem testes da Web, alertas e recursos de Application Insights, sem fornecer acesso a outros serviços no grupo.

## <a name="to-provide-access-to-another-user"></a>Para fornecer acesso a outro usuário

Você deve ter direitos de proprietário para a assinatura ou o grupo de recursos.

O usuário deve ter uma [conta da Microsoft][account]ou acesso à sua [conta organizacional da Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Você pode fornecer acesso a indivíduos e também a grupos de usuários definidos no Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navegar até o grupo de recursos ou diretamente para o próprio recurso

Escolha **controle de acesso (iam)** no menu à esquerda.

![Captura de tela do botão controle de acesso na portal do Azure](./media/resources-roles-access-control/0001-access-control.png)

Selecione **Adicionar atribuição de função**

![Captura de tela do menu de controle de acesso com o botão Adicionar realçado em vermelho](./media/resources-roles-access-control/0002-add.png)

A exibição **adicionar permissões** abaixo é principalmente específica para Application insights recursos, se você estiver exibindo as permissões de controle de acesso de um nível mais alto, como grupos de recursos, você verá funções adicionais não-Application insights centradas.

Para exibir informações sobre todas as funções internas do controle de acesso baseado em função do Azure, use o [conteúdo de referência oficial](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Captura de tela da lista de funções de usuário do controle de acesso](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Selecionar uma função

Quando aplicável, link para a documentação de referência oficial associada.

| Função | No grupo de recursos |
| --- | --- |
| [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Pode alterar qualquer coisa, incluindo o acesso do usuário. |
| [Contribuidor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Pode editar qualquer coisa, incluindo todos os recursos. |
| [Colaborador de componente Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Pode editar Application Insights recursos, testes da Web e alertas. |
| [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Pode exibir, mas não alterar nada. |
| [Application Insights Depurador de Instantâneos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Concede ao usuário permissão para usar Application Insights Depurador de Instantâneos recursos. Observe que essa função está incluída nas funções proprietário e colaborador. |
| Colaborador de implantação de Release Management do serviço do Azure | Função de colaborador para serviços de implantação por meio da implantação do serviço do Azure. |
| [Limpador de dados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Função especial para limpar dados pessoais. Consulte nossas [diretrizes para dados pessoais](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) para obter mais informações.   |
| Administrador do ExpressRoute | Pode criar excluir e gerenciar rotas expressas.|
| [Colaborador de Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Log Analytics colaborador pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Editar configurações de monitoramento inclui adicionar a extensão de VM às VMs; leitura de chaves de conta de armazenamento para poder configurar a coleta de logs do armazenamento do Azure; Criando e Configurando contas de automação; adicionando soluções; e configurar o diagnóstico do Azure em todos os recursos do Azure.  |
| [Leitor de Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics leitor pode exibir e Pesquisar todos os dados de monitoramento, bem como exibir configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
| masterreader | Permite que um usuário exiba tudo, mas não faça alterações. |
| [Colaborador de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Pode ler todos os dados de monitoramento e atualizar as configurações de monitoramento. |
| [Editor de métricas de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Habilita a publicação de métricas em relação aos recursos do Azure. |
| [Leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Pode ler todos os dados de monitoramento. |
| Colaborador de política de recurso (versão prévia) | Usuários com preenchimento de EA, com direitos para criar/modificar a política de recursos, criar tíquete de suporte e ler recurso/hierarquia.  |
| [Administrador de Acesso de Utilizador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Permite que um usuário gerencie o acesso de outros usuários aos recursos do Azure.|
| [Colaborador do site](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Permite que você gerencie sites (não planos da Web), mas não tem acesso a eles.|

A ' edição ' inclui criar, excluir e atualizar:

* Recursos
* Testes na Web
* Alertas
* Exportação contínua

#### <a name="select-the-user"></a>Selecionar o usuário

Se o usuário desejado não estiver no diretório, você poderá convidar qualquer um conta Microsoft.
(Se eles usarem serviços como Outlook.com, OneDrive, Windows Phone ou XBox Live, eles terão um conta Microsoft.)

## <a name="related-content"></a>Conteúdo relacionado

* [Controle de acesso baseado em função no Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Consulta do PowerShell para determinar a associação de função

Como determinadas funções podem ser vinculadas a notificações e alertas de email, pode ser útil ser capaz de gerar uma lista de usuários que pertencem a uma determinada função. Para ajudar com a geração desses tipos de listas, oferecemos as seguintes consultas de exemplo que podem ser ajustadas para atender às suas necessidades específicas:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Consultar toda a assinatura para funções de administrador + funções de colaborador

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Consulta dentro do contexto de um recurso de Application Insights específico para proprietários e colaboradores

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Consulta dentro do contexto de um grupo de recursos específico para proprietários e colaboradores

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
