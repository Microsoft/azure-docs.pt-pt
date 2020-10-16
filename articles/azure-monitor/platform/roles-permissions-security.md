---
title: Funções, permissões e segurança no Azure Monitor
description: Saiba como utilizar as funções e permissões incorporadas do Azure Monitor para restringir o acesso aos recursos de monitorização.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7d92cbc25411f5cc2d528ccf6ecec4539494d380
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533279"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Funções, permissões e segurança no Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Muitas equipas precisam de regular rigorosamente o acesso à monitorização de dados e configurações. Por exemplo, se tiver membros da equipa que trabalham exclusivamente na monitorização (engenheiros de suporte, engenheiros DevOps) ou se utilizar um prestador de serviços gerido, poderá conceder-lhes acesso apenas a monitorizar dados, limitando a sua capacidade de criar, modificar ou eliminar recursos. Este artigo mostra como aplicar rapidamente um papel de Azure de monitorização incorporado a um utilizador em Azure ou construir o seu próprio papel personalizado para um utilizador que precisa de permissões de monitorização limitadas. Em seguida, discute considerações de segurança para os seus recursos relacionados com o Azure Monitor e como pode limitar o acesso aos dados que contêm.

## <a name="built-in-monitoring-roles"></a>Funções de monitorização incorporadas
As funções incorporadas do Azure Monitor destinam-se a ajudar a limitar o acesso aos recursos numa subscrição, permitindo ainda aos responsáveis pela monitorização das infraestruturas obter e configurar os dados de que necessitam. O Azure Monitor fornece duas funções fora da caixa: um leitor de monitorização e um colaborador de monitorização.

### <a name="monitoring-reader"></a>Leitor de Monitorização
As pessoas atribuídas à função Monitoring Reader podem visualizar todos os dados de monitorização numa subscrição, mas não podem modificar qualquer recurso ou editar quaisquer definições relacionadas com recursos de monitorização. Esta função é adequada para utilizadores de uma organização, como engenheiros de suporte ou operações, que precisam de ser capazes de:

* Veja os painéis de monitorização no portal e crie os seus próprios painéis de monitorização privados.
* Ver regras de alerta definidas em [Alertas Azure](alerts-overview.md)
* Consulta para métricas utilizando o [Azure Monitor REST API,](/rest/api/monitor/metrics) [powerShell cmdlets](../samples/powershell-samples.md), ou [cli cross-platform](../samples/cli-samples.md).
* Consultar o Registo de Atividades utilizando o portal, Azure Monitor REST API, Cmdlets PowerShell ou CLI de plataforma cruzada.
* Consulte as [definições de diagnóstico](diagnostic-settings.md) para obter um recurso.
* Ver o [perfil de registo](./activity-log.md#legacy-collection-methods) de uma subscrição.
* Ver definições de autoescala.
* Ver atividade de alerta e configurações.
* Aceder a dados do Access Application Insights e ver dados em AI Analytics.
* Pesquisar dados do espaço de trabalho do Log Analytics, incluindo dados de utilização para o espaço de trabalho.
* Ver grupos de gestão Log Analytics.
* Recupere o esquema de pesquisa no espaço de trabalho log Analytics.
* Listar pacotes de monitorização no espaço de trabalho Log Analytics.
* Recupere e execute pesquisas guardadas no espaço de trabalho do Log Analytics.
* Recupere a configuração de armazenamento do espaço de trabalho Log Analytics.

> [!NOTE]
> Esta função não dá acesso à leitura de dados de registo que tenham sido transmitidos para um centro de eventos ou armazenados numa conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) as informações sobre a configuração do acesso a estes recursos.
> 
> 

### <a name="monitoring-contributor"></a>Colaborador de monitorização
As pessoas atribuídas à função de Colaborador de Monitorização podem visualizar todos os dados de monitorização numa subscrição e criar ou modificar as definições de monitorização, mas não podem modificar quaisquer outros recursos. Esta função é um superconjunto da função monitorar o leitor, e é apropriado para membros de uma equipa de monitorização de uma organização ou prestadores de serviços geridos que, além das permissões acima, também precisam de ser capazes de:

* Publique os painéis de monitorização como um dashboard partilhado.
* Defina [as definições de diagnóstico](diagnostic-settings.md) para um recurso.\*
* Desa estação o [perfil de registo](./activity-log.md#legacy-collection-methods) de uma subscrição.\*
* Defina a atividade e as definições de regras de alerta através [de Alertas Azure](alerts-overview.md).
* Criar Testes e componentes web do Application Insights.
* List Log Analytics workspace compartilhado chaves.
* Ative ou desative os pacotes de monitorização no espaço de trabalho do Log Analytics.
* Criar e excluir e executar pesquisas guardadas no espaço de trabalho do Log Analytics.
* Crie e elimine a configuração de armazenamento do espaço de trabalho Log Analytics.

\*o utilizador também deve receber separadamente a permissão de ListKeys no recurso-alvo (conta de armazenamento ou espaço de nome do centro de eventos) para definir um perfil de registo ou definição de diagnóstico.

> [!NOTE]
> Esta função não dá acesso à leitura de dados de registo que tenham sido transmitidos para um centro de eventos ou armazenados numa conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) as informações sobre a configuração do acesso a estes recursos.
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Permissões de monitorização e funções personalizadas Azure
Se as funções incorporadas acima não corresponderem às necessidades exatas da sua equipa, pode [criar um papel personalizado Azure](../../role-based-access-control/custom-roles.md) com mais permissões granulares. Abaixo estão as operações comuns do Azure Monitor RBAC com as suas descrições.

| Operação | Descrição |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Ler, Escrever, Eliminar] |Ler/escrever/apagar grupos de ação. |
| Microsoft.Insights/ActivityLogAlerts/[Ler, Escrever, Eliminar] |Leia/escreva/elimine os alertas de registo de atividade. |
| Microsoft.Insights/AlertRules/[Ler, Escrever, Eliminar] |Leia/escreva/apague as regras de alerta (do clássico dos alertas). |
| Microsoft.Insights/AlertRules/Incidents/Read |Listar incidentes (histórico da regra de alerta a ser desencadeada) para regras de alerta. Isto só se aplica ao portal. |
| Microsoft.Insights/AutoscaleSettings/[Ler, Escrever, Excluir] |Ler/escrever/apagar definições de autoescala. |
| Microsoft.Insights/DiagnosticSettings/[Ler, Escrever, Excluir] |Ler/escrever/apagar definições de diagnóstico. |
| Microsoft.Insights/EventCategories/Read |Enumerar todas as categorias possíveis no Registo de Atividades. Usado pelo portal Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Esta permissão é necessária para os utilizadores que necessitem de acesso aos Registos de Atividade através do portal. |
| Microsoft.Insights/eventtypes/values/Read |Liste eventos de Registo de Atividade (eventos de gestão) numa subscrição. Esta permissão aplica-se tanto ao acesso programático como ao portal ao Registo de Atividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Ler, Escrever, Eliminar] | Ler/escrever/eliminar definições de diagnóstico para registos de fluxo de rede. |
| Microsoft.Insights/LogDefinitions/Read |Esta permissão é necessária para os utilizadores que necessitem de acesso aos Registos de Atividade através do portal. |
| Microsoft.Insights/LogProfiles/[Ler, Escrever, Excluir] |Ler/escrever/apagar perfis de registo (streaming de Registo de Atividade para centro de eventos ou conta de armazenamento). |
| Microsoft.Insights/MetricAlerts/[Ler, Escrever, Excluir] |Ler/escrever/apagar perto de alertas métricos em tempo real |
| Microsoft.Insights/MetricDefinitions/Read |Leia definições métricas (lista de tipos métricos disponíveis para um recurso). |
| Microsoft.Insights/Metrics/Read |Leia as métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registe-se o fornecedor de recursos Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Ler, Escrever, Excluir] |Ler/escrever/apagar alertas de registo no Azure Monitor. |



> [!NOTE]
> O acesso a alertas, configurações de diagnóstico e métricas para um recurso requer que o utilizador tenha lido o acesso ao tipo de recurso e âmbito desse recurso. Criar ("escrever") uma definição de diagnóstico ou perfil de registo que arquive a uma conta de armazenamento ou streams para centros de eventos requer que o utilizador também tenha permissão ListKeys no recurso-alvo.
> 
> 

Por exemplo, usando a tabela acima, pode criar um papel personalizado Azure para um "Leitor de Registo de Atividade" como este:

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

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para monitorização de dados
A monitorização de dados - particularmente ficheiros de registo - pode conter informações sensíveis, tais como endereços IP ou nomes de utilizadores. Os dados de monitorização do Azure vêm de três formas básicas:

1. O Registo de Atividades, que descreve todas as ações do plano de controlo na sua subscrição do Azure.
2. registos de recursos, que são registos emitidos por um recurso.
3. Métricas, que são emitidas por recursos.

Todos estes três tipos de dados podem ser armazenados numa conta de armazenamento ou transmitidos para o Event Hub, ambos com recursos Azure de uso geral. Porque estes são recursos de uso geral, criar, eliminar e aceder a eles é uma operação privilegiada reservada a um administrador. Sugerimos que utilize as seguintes práticas para monitorizar recursos relacionados com o controlo para evitar o uso indevido:

* Utilize uma única conta de armazenamento dedicada para monitorizar os dados. Se precisar de separar os dados de monitorização em várias contas de armazenamento, nunca partilhe a utilização de uma conta de armazenamento entre dados de monitorização e não monitorização, pois isso pode inadvertidamente dar a quem apenas precisa de acesso a dados de monitorização (por exemplo, um SIEM de terceiros) acesso a dados não monitorizados.
* Utilize um espaço de nomes único e dedicado do Service Bus ou event Hub em todas as definições de diagnóstico pelo mesmo motivo acima.
* Limitar o acesso a contas de armazenamento ou centros de eventos relacionados com monitorização, mantendo-os num grupo de recursos separados, e [utilizar o âmbito](../../role-based-access-control/overview.md#scope) das suas funções de monitorização para limitar o acesso apenas a esse grupo de recursos.
* Nunca conceda a permissão listKeys para contas de armazenamento ou centros de eventos no âmbito de subscrição quando um utilizador apenas precisa de ter acesso aos dados de monitorização. Em vez disso, forneça estas permissões ao utilizador num âmbito de recursos ou grupo de recursos (se tiver um grupo de recursos de monitorização dedicado).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitação do acesso a contas de armazenamento relacionadas com monitorização
Quando um utilizador ou aplicação precisa de ter acesso a dados de monitorização numa conta de armazenamento, deve [gerar uma Conta SAS](/rest/api/storageservices/create-account-sas) na conta de armazenamento que contenha dados de monitorização com acesso apenas ao nível de serviço para armazenamento de bolhas. Em PowerShell, isto pode parecer:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Em seguida, pode dar o token à entidade que precisa de ler a partir dessa conta de armazenamento, e pode listar e ler a partir de todas as bolhas nessa conta de armazenamento.

Em alternativa, se precisar de controlar esta permissão com o RBAC, pode conceder a essa entidade a permissão microsoft.Storage/storageAccounts/listkeys/action nessa conta de armazenamento específica. Isto é necessário para os utilizadores que precisam de ser capazes de definir uma definição de diagnóstico ou perfil de registo para arquivar em uma conta de armazenamento. Por exemplo, pode criar o seguinte papel personalizado Azure para um utilizador ou aplicação que só precisa de ser lido a partir de uma conta de armazenamento:

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
> A permissão ListKeys permite ao utilizador listar as chaves da conta de armazenamento primária e secundária. Estas teclas concedem ao utilizador todas as permissões assinadas (ler, escrever, criar bolhas, apagar bolhas, etc.) em todos os serviços assinados (blob, fila, mesa, ficheiro) nessa conta de armazenamento. Recomendamos a utilização de uma Conta SAS descrita acima sempre que possível.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitação do acesso aos centros de eventos relacionados com a monitorização
Um padrão semelhante pode ser seguido com centros de eventos, mas primeiro você precisa criar uma regra de autorização de escuta dedicada. Se quiser conceder, aceda a uma aplicação que só precisa de ouvir os centros de eventos relacionados com a monitorização, faça o seguinte:

1. Crie uma política de acesso partilhado no(s) hub(s) de eventos que foram criados para transmitir dados de monitorização apenas com pedidos de escuta. Isto pode ser feito no portal. Por exemplo, pode chamá-lo de "monitoringReadOnly". Se possível, deverá dar essa chave diretamente ao consumidor e saltar o passo seguinte.
2. Se o consumidor precisar de ser capaz de obter o ad hoc chave, conceda ao utilizador a ação ListKeys para esse centro de eventos. Isto também é necessário para os utilizadores que precisam ser capazes de definir uma definição de diagnóstico ou perfil de log para transmitir para centros de eventos. Por exemplo, pode criar uma regra RBAC:
   
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

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorização numa Rede Virtual segura

O Azure Monitor precisa de acesso aos seus recursos Azure para fornecer os serviços que ativa. Se quiser monitorizar os seus recursos Azure, enquanto ainda os protege do acesso à Internet Pública, pode ativar as seguintes definições.

### <a name="secured-storage-accounts"></a>Contas de armazenamento garantidas 

Os dados de monitorização são frequentemente escritos numa conta de armazenamento. Pode querer certificar-se de que os dados copiados para uma Conta de Armazenamento não podem ser acedidos por utilizadores não autorizados. Para uma segurança adicional, pode bloquear o acesso à rede apenas para permitir que os seus recursos autorizados e serviços fidedignos da Microsoft tenham acesso a uma conta de armazenamento, restringindo uma conta de armazenamento para usar "redes selecionadas".
![Azure Storage Settings Dialog ](./media/roles-permissions-security/secured-storage-example.png) Azure Monitor é considerado um desses "serviços de confiança da Microsoft" Se permitir que serviços confiáveis da Microsoft acedam ao seu Armazenamento Seguro, o monitor Azure terá acesso à sua conta de armazenamento segura; ativando a escrita de registos de recursos do Azure Monitor, registo de atividade e métricas na sua Conta de Armazenamento nestas condições protegidas. Isto também permitirá que o Log Analytics leia registos de armazenamento seguro.   


Para mais informações, consulte [a segurança da rede e o armazenamento Azure](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre o RBAC e permissões no Gestor de Recursos](../../role-based-access-control/overview.md)
* [Leia a visão geral da monitorização em Azure](../overview.md)

