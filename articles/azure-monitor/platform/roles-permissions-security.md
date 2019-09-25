---
title: Introdução às funções, permissões e segurança com Azure Monitor
description: Saiba como usar funções e permissões internas do Azure Monitor para restringir o acesso aos recursos de monitoramento.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c745375eb4f59208af79bbb03d45f8f0eea7f3ca
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260610"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Introdução às funções, permissões e segurança com Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Muitas equipes precisam regular estritamente o acesso a dados e configurações de monitoramento. Por exemplo, se você tiver membros da equipe que trabalham exclusivamente no monitoramento (engenheiros de suporte, engenheiros de DevOps) ou se usar um provedor de serviços gerenciados, talvez você queira conceder a eles acesso apenas aos dados de monitoramento e restringir sua capacidade de criar, modificar ou excluir recursos. Este artigo mostra como aplicar rapidamente uma função de RBAC de monitoramento interno a um usuário no Azure ou criar sua própria função personalizada para um usuário que precisa de permissões de monitoramento limitadas. Em seguida, ele aborda as considerações de segurança para seus recursos relacionados ao Azure Monitor e como você pode limitar o acesso aos dados que eles contêm.

## <a name="built-in-monitoring-roles"></a>Funções de monitoramento internas
As funções internas do Azure Monitor são projetadas para ajudar a limitar o acesso a recursos em uma assinatura e, ao mesmo tempo, permitir que os responsáveis pela infraestrutura de monitoramento obtenham e configurem os dados de que precisam. Azure Monitor fornece duas funções prontas para uso: Um leitor de monitoramento e um colaborador de monitoramento.

### <a name="monitoring-reader"></a>Leitor de Monitorização
As pessoas com as quais foi atribuída a função leitor de monitoramento podem exibir todos os dados de monitoramento em uma assinatura, mas não podem modificar nenhum recurso ou editar as configurações relacionadas aos recursos de monitoramento. Essa função é apropriada para os usuários em uma organização, como engenheiros de suporte ou operações, que precisam ser capazes de:

* Exiba painéis de monitoramento no portal e crie seus próprios painéis de monitoramento privados.
* Exibir regras de alerta definidas nos [alertas do Azure](alerts-overview.md)
* Consultar métricas usando a [API REST do Azure monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), os [cmdlets do PowerShell](powershell-quickstart-samples.md)ou a [CLI de plataforma cruzada](cli-samples.md).
* Consulte o log de atividades usando o portal, a API REST Azure Monitor, os cmdlets do PowerShell ou a CLI de plataforma cruzada.
* Exibir as [configurações de diagnóstico](diagnostic-settings.md) para um recurso.
* Exibir o [perfil de log](activity-log-export.md) para uma assinatura.
* Exibir configurações de dimensionamento automático.
* Exibir atividade e configurações de alerta.
* Acesse Application Insights dados e exiba dados na análise de ia.
* Pesquise Log Analytics dados do espaço de trabalho, incluindo dados de uso para o espaço de trabalho.
* Exibir Log Analytics grupos de gerenciamento.
* Recupere o esquema de pesquisa no espaço de trabalho Log Analytics.
* Liste pacotes de monitoramento no espaço de trabalho Log Analytics.
* Recuperar e executar pesquisas salvas no espaço de trabalho Log Analytics.
* Recupere a configuração de armazenamento do espaço de trabalho Log Analytics.

> [!NOTE]
> Essa função não dá acesso de leitura aos dados de log que foram transmitidos para um hub de eventos ou armazenados em uma conta de armazenamento. [Consulte abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.
> 
> 

### <a name="monitoring-contributor"></a>Contribuidor de Monitorização
As pessoas que receberam a função de colaborador de monitoramento podem exibir todos os dados de monitoramento em uma assinatura e criar ou modificar as configurações de monitoramento, mas não podem modificar nenhum outro recurso. Essa função é um superconjunto da função leitor de monitoramento e é apropriado para membros da equipe de monitoramento de uma organização ou provedores de serviços gerenciados que, além das permissões acima, também precisam ser capazes de:

* Publique painéis de monitoramento como um painel compartilhado.
* Definir [configurações de diagnóstico](diagnostic-settings.md) para um recurso.\*
* Defina o [perfil de log](activity-log-export.md) para uma assinatura.\*
* Definir a atividade e as configurações de regras de alerta por meio de [alertas do Azure](alerts-overview.md).
* Crie Application Insights testes e componentes da Web.
* Liste Log Analytics chaves compartilhadas do espaço de trabalho.
* Habilite ou desabilite pacotes de monitoramento no espaço de trabalho Log Analytics.
* Crie e exclua e execute pesquisas salvas no espaço de trabalho Log Analytics.
* Crie e exclua a configuração de armazenamento Log Analytics espaço de trabalho.

\*o usuário também deve receber a permissão ListKeys no recurso de destino (conta de armazenamento ou namespace do hub de eventos) para definir um perfil de log ou uma configuração de diagnóstico.

> [!NOTE]
> Essa função não dá acesso de leitura aos dados de log que foram transmitidos para um hub de eventos ou armazenados em uma conta de armazenamento. [Consulte abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Permissões de monitoramento e funções RBAC personalizadas
Se as funções internas acima não atenderem às necessidades exatas de sua equipe, você poderá [criar uma função personalizada de RBAC](../../role-based-access-control/custom-roles.md) com permissões mais granulares. Abaixo estão as operações de RBAC Azure Monitor comuns com suas descrições.

| Operação | Descrição |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Grupos de ação de leitura/gravação/exclusão. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Alertas do log de atividades de leitura/gravação/exclusão. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Regras de alerta de leitura/gravação/exclusão (de alertas clássicos). |
| Microsoft.Insights/AlertRules/Incidents/Read |Listar incidentes (histórico da regra de alerta que está sendo disparado) para regras de alerta. Isso se aplica somente ao Portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Ler/gravar/excluir configurações de dimensionamento automático. |
| Microsoft. insights/DiagnosticSettings/[ler, gravar, excluir] |Configurações de diagnóstico de leitura/gravação/exclusão. |
| Microsoft.Insights/EventCategories/Read |Enumere todas as categorias possíveis no log de atividades. Usado pelo portal do Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. |
| Microsoft.Insights/eventtypes/values/Read |Listar eventos do log de atividades (eventos de gerenciamento) em uma assinatura. Essa permissão é aplicável a acesso de portal e programático ao log de atividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Ler/gravar/excluir configurações de diagnóstico para logs de fluxo de rede. |
| Microsoft.Insights/LogDefinitions/Read |Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. |
| Microsoft. insights/LogProfiles/[ler, gravar, excluir] |Ler/gravar/excluir perfis de log (log de atividades de streaming para o Hub de eventos ou a conta de armazenamento). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Ler/gravar/excluir alertas de métrica quase em tempo real |
| Microsoft.Insights/MetricDefinitions/Read |Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
| Microsoft.Insights/Metrics/Read |Ler métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registre o provedor de recursos de Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Ler/gravar/excluir alertas de log no Azure Monitor. |



> [!NOTE]
> O acesso a alertas, configurações de diagnóstico e métricas para um recurso requer que o usuário tenha acesso de leitura ao tipo de recurso e ao escopo desse recurso. Criar ("gravar") uma configuração de diagnóstico ou um perfil de log que arquiva em uma conta de armazenamento ou fluxos para hubs de eventos exige que o usuário também tenha a permissão ListKeys no recurso de destino.
> 
> 

Por exemplo, usando a tabela acima, você pode criar uma função RBAC personalizada para um "leitor de log de atividades" da seguinte maneira:

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

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para monitoramento de dados
Dados de monitoramento — particularmente arquivos de log — podem conter informações confidenciais, como endereços IP ou nomes de usuário. Os dados de monitoramento do Azure são fornecidos em três formas básicas:

1. O log de atividades, que descreve todas as ações de plano de controle em sua assinatura do Azure.
2. Logs de diagnóstico, que são logs emitidos por um recurso.
3. Métricas, que são emitidas pelos recursos.

Todos esses três tipos de dados podem ser armazenados em uma conta de armazenamento ou transmitidos para o Hub de eventos, ambos os recursos do Azure de finalidade geral. Como esses são recursos de finalidade geral, criar, excluir e acessá-los é uma operação privilegiada reservada para um administrador. Sugerimos que você use as seguintes práticas para recursos relacionados ao monitoramento para evitar o uso indevido:

* Use uma única conta de armazenamento dedicada para monitorar dados. Se você precisar separar os dados de monitoramento em várias contas de armazenamento, nunca Compartilhe o uso de uma conta de armazenamento entre dados de monitoramento e de não monitoramento, pois isso pode inadvertidamente fornecer àqueles que precisam apenas de acesso aos dados de monitoramento (por exemplo, um SIEM de terceiros) acesso a dados que não são de monitoramento.
* Use um único barramento de serviço dedicado ou namespace de Hub de eventos em todas as configurações de diagnóstico pelo mesmo motivo acima.
* Limite o acesso a contas de armazenamento ou hubs de eventos relacionados ao monitoramento, mantendo-os em um grupo de recursos separado, e [use o escopo](../../role-based-access-control/overview.md#scope) em suas funções de monitoramento para limitar o acesso somente a esse grupo de recursos.
* Nunca conceda a permissão ListKeys para contas de armazenamento ou hubs de eventos no escopo da assinatura quando um usuário precisar apenas de acesso aos dados de monitoramento. Em vez disso, conceda essas permissões ao usuário em um grupo de recursos ou de recursos (se você tiver um grupo de recursos de monitoramento dedicado).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitando o acesso às contas de armazenamento relacionadas ao monitoramento
Quando um usuário ou aplicativo precisa de acesso a dados de monitoramento em uma conta de armazenamento, você deve [gerar uma SAS de conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na conta de armazenamento que contém dados de monitoramento com acesso somente leitura no nível de serviço para o armazenamento de BLOBs. No PowerShell, isso pode ser semelhante a:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Em seguida, você pode fornecer o token para a entidade que precisa ler dessa conta de armazenamento e pode listar e ler de todos os BLOBs nessa conta de armazenamento.

Como alternativa, se você precisar controlar essa permissão com o RBAC, poderá conceder a essa entidade a permissão Microsoft. Storage/storageAccounts/listkeys/Action nessa conta de armazenamento específica. Isso é necessário para os usuários que precisam ser capazes de definir uma configuração de diagnóstico ou perfil de log para arquivar em uma conta de armazenamento. Por exemplo, você pode criar a seguinte função de RBAC personalizada para um usuário ou aplicativo que só precisa ler de uma conta de armazenamento:

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
> A permissão ListKeys permite que o usuário liste as chaves de conta de armazenamento primárias e secundárias. Essas chaves concedem ao usuário todas as permissões assinadas (ler, gravar, criar BLOBs, excluir BLOBs, etc.) em todos os serviços assinados (BLOB, fila, tabela, arquivo) na conta de armazenamento. É recomendável usar uma SAS de conta descrita acima, quando possível.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitando o acesso aos hubs de eventos relacionados ao monitoramento
Um padrão semelhante pode ser seguido de hubs de eventos, mas primeiro você precisa criar uma regra de autorização de escuta dedicada. Se você quiser conceder acesso a um aplicativo que só precisa escutar os hubs de eventos relacionados ao monitoramento, faça o seguinte:

1. Crie uma política de acesso compartilhado nos hubs de eventos que foram criados para streaming de dados de monitoramento com apenas declarações de escuta. Isso pode ser feito no Portal. Por exemplo, você pode chamá-lo de "monitoringReadOnly". Se possível, você desejará fornecer essa chave diretamente ao consumidor e ignorar a próxima etapa.
2. Se o consumidor precisar ser capaz de obter a chave ad hoc, conceda ao usuário a ação ListKeys para o Hub de eventos. Isso também é necessário para os usuários que precisam ser capazes de definir uma configuração de diagnóstico ou perfil de log para transmitir para os hubs de eventos. Por exemplo, você pode criar uma regra de RBAC:
   
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

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitoramento em uma rede virtual protegida

Azure Monitor precisa acessar seus recursos do Azure para fornecer os serviços que você habilitar. Se você deseja monitorar seus recursos do Azure enquanto ainda os protege de acesso à Internet pública, você pode habilitar as seguintes configurações.

### <a name="secured-storage-accounts"></a>Contas de armazenamento protegidas 

Os dados de monitoramento geralmente são gravados em uma conta de armazenamento. Talvez você queira certificar-se de que os dados copiados para uma conta de armazenamento não possam ser acessados por usuários não autorizados. Para obter mais segurança, você pode bloquear o acesso à rede para permitir que somente os recursos autorizados e os serviços confiáveis da Microsoft acessem uma conta de armazenamento, restringindo uma conta de armazenamento para usar "redes selecionadas".
![O Azure monitor de diálogo](./media/roles-permissions-security/secured-storage-example.png) configurações do armazenamento do Azure é considerado um destes "serviços confiáveis da Microsoft" se você permitir que serviços da Microsoft confiáveis acessem seu armazenamento protegido, o Azure monitor terá acesso à sua conta de armazenamento protegido; habilitando gravar Azure Monitor logs de diagnóstico, log de atividades e métricas para sua conta de armazenamento nessas condições protegidas. Isso também permitirá que Log Analytics Leia os logs do armazenamento protegido.   


Para obter mais informações, consulte [segurança de rede e armazenamento do Azure](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre RBAC e permissões no Resource Manager](../../role-based-access-control/overview.md)
* [Leia a visão geral do monitoramento no Azure](../../azure-monitor/overview.md)


