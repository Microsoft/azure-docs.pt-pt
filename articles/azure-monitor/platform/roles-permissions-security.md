---
title: Começar com as funções, permissões e segurança com o Azure Monitor
description: Saiba como utilizar permissões e funções incorporadas do Azure Monitor para restringir o acesso a recursos de monitorização.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 55a7a26815dac1140d100c05a47057f8d5000f9d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317820"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Começar com as funções, permissões e segurança com o Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Muitas equipes precisam estritamente regular o acesso aos dados e definições de monitorização. Por exemplo, se tiver os membros da Equipe que trabalham exclusivamente em monitorização (engenheiros de suporte, engenheiros de devops) ou se usar um provedor de serviço gerida, pode querer lhes conceder acesso a dados de monitorização apenas ao restringir a capacidade de criar, modificar, ou Elimine recursos. Este artigo mostra como aplicar uma função de monitorização incorporada RBAC a um utilizador no Azure ou criar sua própria função personalizada para um utilizador que tem permissões de monitorização limitadas rapidamente. Em seguida, ele aborda considerações de segurança dos seus recursos relacionados com o Azure Monitor e a forma como pode limitar o acesso aos dados que contêm.

## <a name="built-in-monitoring-roles"></a>Funções de monitorização incorporadas
Funções incorporadas do Monitor do Azure foram concebidas para ajudar a limitar o acesso aos recursos numa subscrição, enquanto ainda permite que os responsáveis pela monitorização de infraestrutura para obter e configurar os dados que precisam. Monitor do Azure fornece duas funções de out-of-the-box: Um leitor de monitorização e um Contribuidor de monitorização.

### <a name="monitoring-reader"></a>Leitor de Monitorização
As pessoas atribuídas a função de leitor de monitorização podem ver todos os dados de monitorização numa subscrição, mas não é possível modificar qualquer recurso ou editar as definições relacionadas com a monitorização de recursos. Esta função é adequada para os utilizadores numa organização, tais como engenheiros de suporte ou operações que precisam de ser capaz de:

* Ver dashboards de monitorização no portal e crie seus próprios dashboards de monitorização privados.
* Ver as regras de alerta definidas no [alertas do Azure](../../azure-monitor/platform/alerts-overview.md)
* Consultas de métricas de utilização a [API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [cmdlets do PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), ou [CLI de várias plataformas](../../azure-monitor/platform/cli-samples.md).
* Consulte o registo de atividades com o portal, API de REST do Azure Monitor, cmdlets do PowerShell ou CLI de várias plataformas.
* Ver os [das definições de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) para um recurso.
* Ver os [perfil de registo](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile) para uma subscrição.
* Ver definições de dimensionamento automático.
* Ver atividade de alerta e definições.
* Acessar dados do Application Insights e visualizar dados no Analytics de IA.
* Procure dados de área de trabalho do Log Analytics incluindo dados de utilização para a área de trabalho.
* Ver grupos de gestão do Log Analytics.
* Obter o esquema de pesquisa do Log Analytics.
* Liste os "intelligence packs" Log Analytics.
* Obter e executar pesquisas guardadas do Log Analytics.
* Obter a configuração de armazenamento do Log Analytics.

> [!NOTE]
> Esta função não dá acesso de leitura para dados de registo que foi transmitidos para um hub de eventos ou armazenados numa conta de armazenamento. [Veja a seguir](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.
> 
> 

### <a name="monitoring-contributor"></a>Contribuidor de Monitorização
As pessoas atribuídas a função de Contribuidor de monitorização podem ver todos os dados de monitorização numa subscrição e criar ou modificar definições de monitorização, mas não é possível modificar quaisquer outros recursos. Esta função é um superconjunto da função do leitor de monitorização e é adequada para os membros da equipe de monitorização ou fornecedores de serviços geridos que, além das permissões acima, também tem de ser capaz de uma organização:

* Publica dashboards de monitorização como um dashboard partilhado.
* Definir [das definições de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) para um recurso.\*
* Definir o [perfil de registo](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile) para uma subscrição.\*
* Defina a atividade de regras de alerta e definições através de [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Crie testes web do Application Insights e componentes.
* Listar chaves partilhada da área de trabalho de Log Analytics.
* Ativar ou desativar "intelligence packs" Log Analytics.
* Crie e elimine e executar pesquisas guardadas do Log Analytics.
* Criar e eliminar a configuração de armazenamento do Log Analytics.

\*tem também separadamente ser concedido ao utilizador permissão de ListKeys no recurso de destino (armazenamento conta ou event hub namespace) para definir um perfil de registo ou a definição de diagnóstico.

> [!NOTE]
> Esta função não dá acesso de leitura para dados de registo que foi transmitidos para um hub de eventos ou armazenados numa conta de armazenamento. [Veja a seguir](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Permissões e funções RBAC personalizadas de monitorização
Se as funções incorporadas acima não atenderem às necessidades exatas de sua equipe, pode [criar uma função RBAC personalizada](../../role-based-access-control/custom-roles.md) com permissões mais granulares. Seguem-se as operações comuns do RBAC do Azure Monitor e suas descrições.

| Operação | Descrição |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Grupos de ação de leitura/escrita/eliminar. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Alertas de registo de atividade de leitura/escrita/eliminar. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Regras de alerta de leitura/escrita/eliminar (a partir de alertas clássicas). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista de incidentes (histórico de regra de alerta a ser disparado) para regras de alerta. Isto aplica-se apenas ao portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Definições de dimensionamento automático de leitura/escrita/eliminar. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Definições de diagnóstico de leitura/escrita/eliminar. |
| Microsoft.Insights/EventCategories/Read |Enumere todas as categorias de possíveis no registo de atividades. Utilizado pelo portal do Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de Atividades através do portal. |
| Microsoft.Insights/eventtypes/values/Read |Lista de eventos de registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável ao portal e programático acesso ao registo de atividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Leitura/escrita/eliminar definições de diagnóstico para os registos de fluxo de rede. |
| Microsoft.Insights/LogDefinitions/Read |Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de Atividades através do portal. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Perfis de registo de leitura/escrita/eliminar (transmissão em fluxo o registo de atividades para a conta de armazenamento ou de hub de eventos). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Leitura/escrita/eliminar perto de alertas de métricas em tempo real |
| Microsoft.Insights/MetricDefinitions/Read |Ler definições de métrica (lista de tipos de métricas disponíveis para um recurso). |
| Microsoft.Insights/Metrics/Read |Ler métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registe o fornecedor de recursos do Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Alertas de registo de leitura/escrita/eliminar no Azure Monitor. |



> [!NOTE]
> Aceder a alertas, as definições de diagnóstico e métricas para um recurso exige que o utilizador tem acesso de leitura para o tipo de recurso e o escopo desse recurso. ("Escrita") a criar um perfil de configuração ou registo de diagnóstico que arquiva para uma conta de armazenamento ou fluxos para os hubs de eventos exige que o utilizador também ter permissão de ListKeys no recurso de destino.
> 
> 

Por exemplo, utilizando a tabela acima, pode criar uma função RBAC personalizada para um "leitor do registo de atividade" como este:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para dados de monitorização
Dados de monitorização — especialmente os ficheiros de registo, pode conter informações confidenciais, como endereços IP ou nomes de utilizador. Dados de monitorização do Azure é fornecido em três formas básicas:

1. O registo de atividades, que descreve todas as ações de plano de controlo na sua subscrição do Azure.
2. Registos de diagnóstico, que são emitidos por um recurso de registos.
3. Métricas, que são emitidas pelos recursos.

Três desses tipos de dados podem ser armazenados numa conta de armazenamento ou transmitidos para o Hub de eventos, sendo que ambas são recursos do Azure para fins gerais. Uma vez que estes são os recursos para fins gerais, criação, exclusão e acesso aos mesmos são uma operação privilegiada reservada para um administrador. Sugerimos que utilize as seguintes práticas para os recursos relacionados com a monitorização para impedir a utilização indevida:

* Utilize uma conta de armazenamento exclusiva e dedicada para dados de monitorização. Se precisar de separar os dados de monitorização em várias contas de armazenamento, nunca utilização de uma conta de armazenamento entre a monitorização de partilha e não monitoramento de dados, como isso poderão, inadvertidamente, dê aqueles que apenas necessitam de acesso para a monitorização dos dados (por exemplo, um SIEM de terceiros) acesso a monitorização não dados.
* Use um namespace do Service Bus ou Hub de eventos, exclusiva e dedicado em todas as definições de diagnóstico pela mesma razão, como mostrado acima.
* Limitar o acesso a contas de armazenamento relacionada com a monitorização ou hubs de eventos por mantê-los num grupo de recursos separados, e [utilizar âmbito](../../role-based-access-control/overview.md#scope) em suas funções de monitorização para limitar o acesso a esse grupo de recursos.
* Nunca conceda a permissão de ListKeys para contas de armazenamento ou hubs de eventos no âmbito da subscrição quando o utilizador necessita apenas de acesso a dados de monitorização. Em vez disso, dar estas permissões para o usuário com um recurso ou grupo de recursos (se tiver um grupo de recursos de monitoramento dedicado) escopo.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitar o acesso a contas de armazenamento relacionada com a monitorização
Quando um utilizador ou aplicação precisa de aceder a dados numa conta de armazenamento de monitorização, deve [gerar uma SAS de conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na conta de armazenamento que contém dados de monitorização com o nível de serviço acesso só de leitura para o armazenamento de Blobs. No PowerShell, como seria o resultado:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Pode fornecer o token para a entidade que precisam para ler a partir do que o armazenamento conta e ele pode listar e ler a partir de todos os blobs nessa conta de armazenamento.

Em alternativa, se precisar de controlar esta permissão com o RBAC, pode conceder essa entidade a permissão de Microsoft.Storage/storageAccounts/listkeys/action nessa conta de armazenamento específico. Isso é necessário para os utilizadores que têm de ser capaz de configurar uma definição de diagnóstico ou iniciar perfil para arquivar numa conta de armazenamento. Por exemplo, pode criar a seguinte função RBAC personalizada para um usuário ou aplicativo que precisa apenas ler a partir de uma conta de armazenamento:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> A permissão de ListKeys permite ao utilizador listar as chaves de conta de armazenamento primário e secundário. Essas chaves concedem ao utilizador todas assinadas permissões (leitura, escrita, blobs de criar e eliminar blobs, etc.) em todos os assinado serviços (blob, fila, tabela e ficheiro) nessa conta de armazenamento. Recomendamos que utilize uma SAS de conta descrito acima, sempre que possível.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitar o acesso aos hubs de eventos relacionados com a monitorização
Pode ser seguido de um padrão semelhante com os hubs de eventos, mas primeiro tem de criar uma regra de autorização de escutar dedicada. Se quiser conceder, acesso a um aplicativo que precisa apenas de ouvir os hubs de eventos relacionados com a monitorização, efetue o seguinte:

1. Crie uma política de acesso partilhado no no hub de ou de event hubs que foram criadas para dados de monitorização com apenas afirmações de escuta de transmissão em fluxo. Isso pode ser feito no portal. Por exemplo, poderá chamar "monitoringReadOnly." Se possível, desejará dar essa chave diretamente para o consumidor e ignore o passo seguinte.
2. Se o consumidor tem de ser capaz de obter o chave ad-hoc, conceda ao utilizador a ação de ListKeys para esse hub de eventos. Isso também é necessário para os utilizadores que têm de ser capaz de configurar uma definição de diagnóstico ou perfil de registo para o stream para os hubs de eventos. Por exemplo, pode criar uma regra RBAC:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorização dentro de uma rede Virtual protegida

O Azure Monitor precisa de aceder aos recursos do Azure para fornecer os serviços que ativa. Se quiser monitorizar os recursos do Azure enquanto ainda protegendo-los contra o acesso à Internet pública, pode ativar as seguintes definições.

### <a name="secured-storage-accounts"></a>Contas de armazenamento seguro 

Dados de monitorização, muitas vezes, é escrito para uma conta de armazenamento. Pode querer Certifique-se de que os dados copiados para uma conta de armazenamento não podem ser acedidos por utilizadores não autorizados. Para segurança adicional, pode bloquear acesso à rede para permitir apenas os recursos autorizados e acesso de serviços Microsoft fidedigno para uma conta de armazenamento ao restringir uma conta de armazenamento para utilizar "redes selecionadas".
![Caixa de diálogo de definições do armazenamento do Azure](./media/roles-permissions-security/secured-storage-example.png) Azure Monitor é considerado um dos seguintes "trusted serviços da Microsoft" se permitir que os serviços Microsoft fidedignos para aceder ao seu armazenamento protegido, o Azure monitor terão acesso à sua conta de armazenamento seguro; ativar escrever os registos de diagnóstico do Azure Monitor, registo de atividades e métricas para a sua conta de armazenamento sob essas condições protegidas. Irá também permitir Log Analytics para ler registos a partir do armazenamento seguro.   


Para obter mais informações, consulte [de rede de segurança e armazenamento do Azure](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre RBAC e as permissões no Resource Manager](../../role-based-access-control/overview.md)
* [Leia a visão geral de monitorização no Azure](../../azure-monitor/overview.md)


