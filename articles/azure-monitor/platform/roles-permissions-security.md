---
title: Funções, permissões e segurança no Monitor Azure
description: Aprenda a utilizar as funções e permissões incorporadas do Azure Monitor para restringir o acesso aos recursos de monitorização.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 81309f0b5781e6302887a5b079ed359e70659834
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658987"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Funções, permissões e segurança no Monitor Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Muitas equipas precisam de regular rigorosamente o acesso à monitorização de dados e configurações. Por exemplo, se tiver membros da equipa que trabalham exclusivamente na monitorização (engenheiros de suporte, engenheiros de DevOps) ou se utilizar um prestador de serviços gerido, poderá querer conceder-lhes acesso apenas a dados de monitorização, limitando a sua capacidade de criar, modificar ou eliminar recursos. Este artigo mostra como aplicar rapidamente uma função RBAC de monitorização incorporada a um utilizador em Azure ou construir o seu próprio papel personalizado para um utilizador que precisa de permissões de monitorização limitadas. Em seguida, discute considerações de segurança para os seus recursos relacionados com o Monitor Azure e como pode limitar o acesso aos dados que contêm.

## <a name="built-in-monitoring-roles"></a>Funções de monitorização incorporadas
As funções incorporadas do Azure Monitor destinam-se a ajudar a limitar o acesso aos recursos numa subscrição, permitindo ainda aos responsáveis pela monitorização da infraestrutura obter e configurar os dados de que necessitam. O Monitor Azure fornece duas funções fora da caixa: Um Leitor de Monitorização e um Colaborador de Monitorização.

### <a name="monitoring-reader"></a>Leitor de Monitorização
As pessoas atribuídas à função Monitoring Reader podem visualizar todos os dados de monitorização numa subscrição, mas não podem modificar qualquer recurso ou editar quaisquer configurações relacionadas com a monitorização de recursos. Esta função é adequada para os utilizadores de uma organização, como engenheiros de apoio ou de operações, que precisam de ser capazes de:

* Veja os dashboards de monitorização no portal e crie os seus próprios painéis de monitorização privados.
* Ver regras de alerta definidas em [Alertas Azure](alerts-overview.md)
* Consulta para métricas utilizando o API DO [MONITOR Azure,](https://msdn.microsoft.com/library/azure/dn931930.aspx) [cmdlets PowerShell](powershell-quickstart-samples.md)ou [CLI de plataforma cruzada](cli-samples.md).
* Consulta do Registo de Atividadeutilizando o portal, Azure Monitor REST API, PowerShell cmdlets ou CLI cross-platform.
* Consulte as [definições](diagnostic-settings.md) de diagnóstico para obter um recurso.
* Ver o perfil de [registo](activity-log-export.md) para uma subscrição.
* Ver definições de escala automática.
* Ver atividade de alerta e configurações.
* Aceder aos dados de Insights de Aplicação e ver dados em AI Analytics.
* Pesquisar dados do espaço de trabalho do Log Analytics, incluindo dados de utilização para o espaço de trabalho.
* Ver grupos de gestão log analytics.
* Recupere o esquema de pesquisa no espaço de trabalho do Log Analytics.
* Lista de pacotes de monitorização no espaço de trabalho do Log Analytics.
* Recupere e execute pesquisas guardadas no espaço de trabalho do Log Analytics.
* Recupere a configuração de armazenamento do espaço de trabalho Log Analytics.

> [!NOTE]
> Esta função não dá acesso a dados de registo que tenham sido transmitidos para um centro de eventos ou armazenados numa conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) informações sobre a configuração do acesso a estes recursos.
> 
> 

### <a name="monitoring-contributor"></a>Colaborador de Monitorização
As pessoas atribuídas à função de Colaborador Monitorado podem visualizar todos os dados de monitorização numa subscrição e criar ou modificar as definições de monitorização, mas não podem modificar quaisquer outros recursos. Este papel é um superconjunto do papel do Monitor ingestão de Leitores, e é apropriado para membros de uma equipa de monitorização de uma organização ou prestadores de serviços geridos que, além das permissões acima, também precisam de ser capazes de:

* Publique os dashboards de monitorização como um dashboard partilhado.
* Detete [as definições](diagnostic-settings.md) de diagnóstico para um recurso.\*
* Detete o perfil de [registo](activity-log-export.md) para uma subscrição.\*
* Delineie regras de alerta de atividade e configurações através de [Alertas Azure](alerts-overview.md).
* Criar testes e componentes web De Insights de Aplicação Insights.
* List Log Analytics chaves partilhadas.
* Ative ou desative os pacotes de monitorização no espaço de trabalho do Log Analytics.
* Criar e eliminar e executar pesquisas guardadas no espaço de trabalho do Log Analytics.
* Crie e elimine a configuração de armazenamento do espaço de trabalho Log Analytics.

\*utilizador também deve ser concedida separadamente a permissão ListKeys no recurso-alvo (conta de armazenamento ou espaço de nome do centro de eventos) para definir um perfil de registo ou definição de diagnóstico.

> [!NOTE]
> Esta função não dá acesso a dados de registo que tenham sido transmitidos para um centro de eventos ou armazenados numa conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) informações sobre a configuração do acesso a estes recursos.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Permissões de monitorização e funções RBAC personalizadas
Se as funções acima incorporadas não atenderem às necessidades exatas da sua equipa, pode [criar um papel RBAC personalizado](../../role-based-access-control/custom-roles.md) com permissões mais granulares. Abaixo estão as operações rBAC do Monitor Azure com as suas descrições.

| Operação | Descrição |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Leia/escreva/apague grupos de ação. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Ler/escrever/eliminar alertas de registo de atividade. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Leia/escreva/apague as regras de alerta (a partir de alertas clássicos). |
| Microsoft.Insights/AlertRules/Incidents/Read |Enumeram incidentes (histórico da regra de alerta a ser desencadeado) para regras de alerta. Isto só se aplica ao portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Ler/escrever/eliminar as definições de escala automática. |
| Microsoft.Insights/DiagnosticSettings/[Ler, Escrever, Excluir] |Ler/escrever/eliminar as definições de diagnóstico. |
| Microsoft.Insights/EventCategories/Read |Enumerar todas as categorias possíveis no Registo de Atividades. Usado pelo portal Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Esta permissão é necessária para os utilizadores que necessitem de acesso a Registos de Atividade através do portal. |
| Microsoft.Insights/eventtypes/values/Read |Lista de eventos de Registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável tanto ao acesso programático como ao portal ao Registo de Atividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Leia/escreva/elimine as definições de diagnóstico dos registos de fluxo de rede. |
| Microsoft.Insights/LogDefinitions/Read |Esta permissão é necessária para os utilizadores que necessitem de acesso a Registos de Atividade através do portal. |
| Microsoft.Insights/LogProfiles/[Ler, Escrever, Excluir] |Ler/escrever/eliminar perfis de registo (streaming Registo de Atividades para centro de eventos ou conta de armazenamento). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Ler/escrever/eliminar alertas métricos em tempo real |
| Microsoft.Insights/MetricDefinitions/Read |Ler definições métricas (lista dos tipos métricos disponíveis para um recurso). |
| Microsoft.Insights/Metrics/Read |Leia as métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registe o fornecedor de recursos Do Monitor Azure. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Leia/escreva/apague os alertas de registo no Monitor Azure. |



> [!NOTE]
> O acesso a alertas, configurações de diagnóstico e métricas para um recurso requer que o utilizador tenha lido o acesso ao tipo de recurso e ao âmbito desse recurso. Criar ("escrever") uma definição de diagnóstico ou um perfil de registo que arquiva uma conta de armazenamento ou streams para centros de eventos requer que o utilizador também tenha permissão do ListKeys no recurso-alvo.
> 
> 

Por exemplo, usando a tabela acima, você poderia criar uma função RBAC personalizada para um "Activity Log Reader" como este:

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
A monitorização dos dados , particularmente ficheiros de registo, pode conter informações sensíveis, tais como endereços IP ou nomes de utilizadores. Os dados de monitorização do Azure têm três formas básicas:

1. O Registo de Atividades, que descreve todas as ações de control-plane na sua subscrição Azure.
2. registos de recursos, que são registos emitidos por um recurso.
3. Métricas, que são emitidas por recursos.

Todos estes três tipos de dados podem ser armazenados numa conta de armazenamento ou transmitidos para o Event Hub, ambos recursos Azure de uso geral. Porque estes são recursos de uso geral, criando, apagando e acedendo a eles é uma operação privilegiada reservada a um administrador. Sugerimos que utilize as seguintes práticas para monitorizar recursos relacionados com a monitorização para evitar o uso indevido:

* Utilize uma única conta de armazenamento dedicada para monitorizar dados. Se necessitar de separar os dados de monitorização em várias contas de armazenamento, nunca partilhe a utilização de uma conta de armazenamento entre dados de monitorização e não monitorização, pois isso pode inadvertidamente dar àqueles que apenas precisam de acesso a dados de monitorização (por exemplo, um SIEM de terceiros) acesso a dados não monitorizados.
* Utilize um único e dedicado espaço de nome saque de ônibus de serviço ou hub de eventos em todas as definições de diagnóstico pela mesma razão que acima.
* Limite o acesso a contas de armazenamento relacionadas com monitorização ou centros de eventos, mantendo-os num grupo de recursos separados, e [utilize o âmbito](../../role-based-access-control/overview.md#scope) das suas funções de monitorização para limitar o acesso apenas a esse grupo de recursos.
* Nunca conceda a permissão do ListKeys para contas de armazenamento ou centros de eventos no âmbito de subscrição quando um utilizador apenas precisa de acesso a dados de monitorização. Em vez disso, dê estas permissões ao utilizador num âmbito de recurso ou de recursos (se tiver um grupo de recursos de monitorização dedicado).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitação do acesso a contas de armazenamento relacionadas com a monitorização
Quando um utilizador ou aplicação precisa de acesso a dados de monitorização numa conta de armazenamento, deve [gerar uma Conta SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx) na conta de armazenamento que contenha dados de monitorização com acesso apenas ao armazenamento de leitura ao nível do serviço. Na PowerShell, isto pode parecer:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Em seguida, pode dar o símbolo à entidade que precisa de ler a partir dessa conta de armazenamento, e pode listar e ler de todas as bolhas nessa conta de armazenamento.

Em alternativa, se necessitar de controlar esta permissão com o RBAC, pode conceder a essa entidade a Microsoft.StorageAccounts/listkeys/action permission nessa conta de armazenamento específica. Isto é necessário para que os utilizadores que precisam de ser capazes de definir uma definição de diagnóstico ou um perfil de registo para arquivar em uma conta de armazenamento. Por exemplo, pode criar a seguinte função RBAC personalizada para um utilizador ou aplicação que só precisa de ser lida a partir de uma conta de armazenamento:

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
> A permissão ListKeys permite ao utilizador listar as chaves da conta de armazenamento primário e secundário. Estas chaves concedem ao utilizador todas as permissões assinadas (ler, escrever, criar bolhas, eliminar bolhas, etc.) em todos os serviços assinados (blob, fila, mesa, arquivo) nessa conta de armazenamento. Recomendamos a utilização de uma Conta SAS descrita acima, sempre que possível.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitação do acesso a centros de eventos relacionados com monitorização
Um padrão semelhante pode ser seguido com centros de eventos, mas primeiro você precisa criar uma regra de autorização de escuta dedicada. Se quiser conceder, aceda a uma aplicação que só precisa de ouvir centros de eventos relacionados com monitorização, faça o seguinte:

1. Crie uma política de acesso partilhado no centro do evento que foram criados para transmitir dados de monitorização apenas com reivindicações de Listen. Isto pode ser feito no portal. Por exemplo, pode chamar-lhe "monitorreadOnly". Se possível, irá querer dar essa chave diretamente ao consumidor e saltar o próximo passo.
2. Se o consumidor precisar de ser capaz de obter a chave ad hoc, conceda ao utilizador a ação ListKeys para esse hub de eventos. Isto também é necessário para os utilizadores que precisam de ser capazes de definir uma definição de diagnóstico ou um perfil de registo para transmitir para centros de eventos. Por exemplo, pode criar uma regra RBAC:
   
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

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorização dentro de uma rede virtual segura

O Azure Monitor precisa de acesso aos seus recursos Azure para fornecer os serviços que permite. Se quiser monitorizar os seus recursos Azure, garantindo-os do acesso à Internet Pública, pode ativar as seguintes definições.

### <a name="secured-storage-accounts"></a>Contas de Armazenamento Seguras 

Os dados de monitorização são frequentemente escritos numa conta de armazenamento. Pode querer certificar-se de que os dados copiados para uma Conta de Armazenamento não podem ser acedidos por utilizadores não autorizados. Para obter segurança adicional, pode bloquear o acesso à rede apenas para permitir que os seus recursos autorizados e serviços confiáveis da Microsoft acedam a uma conta de armazenamento, restringindo uma conta de armazenamento para utilizar "redes selecionadas".
![Definições de armazenamento Azure O diálogo](./media/roles-permissions-security/secured-storage-example.png) O Monitor Azure é considerado um destes "serviços confiáveis da Microsoft" Se permitir que os serviços fidedignos da Microsoft acedam ao seu Armazenamento Seguro, o monitor Azure terá acesso à sua Conta de Armazenamento Segura; permitindo a escrita de registos de recursos do Monitor Azure, registo de atividade e métricas na sua Conta de Armazenamento nestas condições protegidas. Isto também permitirá que o Log Analytics leia os registos a partir de armazenamento seguro.   


Para mais informações, consulte [segurança da rede e armazenamento azure](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre rBAC e permissões no Gestor de Recursos](../../role-based-access-control/overview.md)
* [Leia a visão geral da monitorização em Azure](../../azure-monitor/overview.md)


