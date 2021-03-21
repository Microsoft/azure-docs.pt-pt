---
title: Iniciar manualmente uma falha na sql Managed Instance
description: Aprenda a falhar manualmente réplicas primárias e secundárias em Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 02/27/2021
ms.openlocfilehash: 3c969c1898e67361e37a825d7976b1c52d08dd24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101691149"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Ativação pós-falha manual iniciada pelo utilizador no SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como falhar manualmente um nó primário nos níveis de serviço SQL Managed Instance General Purpose (GP) e Business Critical (BC) e como falhar manualmente um nó de réplica de leitura secundária apenas no nível de serviço BC.

## <a name="when-to-use-manual-failover"></a>Quando utilizar o failover manual

[A alta disponibilidade](../database/high-availability-sla.md) é uma parte fundamental da plataforma SQL Managed Instance que funciona de forma transparente para as suas aplicações de base de dados. Falhas dos nós primários para os secundários em caso de degradação do nó ou deteção de falhas, ou durante atualizações regulares mensais de software são uma ocorrência esperada para todas as aplicações usando SQL Managed Instance in Azure.

Pode considerar a execução de um [failover manual](../database/high-availability-sla.md#testing-application-fault-resiliency) na SQL Managed Instance por algumas das seguintes razões:
- Aplicação de teste para a resiliência por falhas antes de ser implantada na produção
- Testar sistemas de ponta a ponta para a resiliência de falhas nas falhas automáticas
- Teste como o failover impacta as sessões de base de dados existentes
- Verifique se uma falha altera o desempenho de ponta a ponta devido a alterações na latência da rede
- Em alguns casos de degradação do desempenho da consulta, o failover manual pode ajudar a atenuar a questão do desempenho.

> [!NOTE]
> Garantir que as suas aplicações são resistentes antes de serem implementadas para a produção ajudará a mitigar o risco de falhas de aplicação na produção e contribuirá para a disponibilidade de aplicações para os seus clientes. Saiba mais sobre o teste das suas aplicações para a prontidão na nuvem com [a aplicação de testes Cloud Readiness for Failover Resiliency with SQL Managed Instance](https://youtu.be/FACWYLgYDL8) video recoding.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Iniciar falha manual em SqL Managed Instance

### <a name="azure-rbac-permissions-required"></a>Permissões Azure RBAC necessárias

O utilizador que iniciar uma falha terá de ter uma das seguintes funções do Azure:

- Função do Proprietário de Assinatura, ou
- [Papel de contribuinte de instância gerida,](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>Com o PowerShell

A versão mínima de Az.Sql tem de ser [v2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0). Considere usar [a Azure Cloud Shell](../../cloud-shell/overview.md) do portal Azure que tem sempre a versão mais recente do PowerShell disponível. 

Como pré-requisito, utilize o seguinte script PowerShell para instalar os módulos Azure necessários. Além disso, selecione a subscrição onde está localizada a Instância Gerida que pretende falhar.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Use o comando PowerShell [Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) com o seguinte exemplo para iniciar a falha do nó primário, aplicável ao nível de serviço BC e GP.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Utilize o seguinte comando PS para deixar de ler o nó secundário, aplicável apenas ao nível de serviço BC.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Com a CLI

Certifique-se de ter os scripts CLI mais recentes instalados.

Utilize o comando CLI de falha az sql com o seguinte exemplo para iniciar a falha do nó primário, aplicável ao nível de serviço BC e GP.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Utilize o seguinte comando CLI para deixar de ler o nó secundário, aplicável apenas ao nível de serviço BC.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Com a API Rest

Para utilizadores avançados que talvez precisem de automatizar as falhas das suas Instâncias Geridas SQL para efeitos de implementação de gasodutos de teste contínuos ou mitigadores de desempenho automatizados, esta função pode ser realizada através do início do failover através de uma chamada API. ver [Casos Geridos - Failover REST API](/rest/api/sql/managed%20instances%20-%20failover/failover) para mais detalhes.

Para iniciar o failover utilizando a chamada REST API, gere primeiro o Auth Token utilizando o cliente API à sua escolha. O token de autenticação gerada é usado como propriedade de Autorização no cabeçalho do pedido de API e é obrigatório.

O seguinte código é um exemplo da API URI a chamar:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

As seguintes propriedades precisam de ser passadas na chamada da API:

| **Propriedade API** | **Parâmetro** |
| --- | --- |
| subscriptionId | ID de subscrição para o qual é implementado o caso gerido |
| resourceGroupName | Grupo de recursos que contém instância gerida |
| managedInstanceName | Nome da instância gerida |
| replicaType | (Opcional) (Secundário Primário ou Legível). Estes parâmetros representam o tipo de réplica a ser chumbada: secundária primária ou legível. Se não for especificado, o failover será iniciado na réplica primária por predefinição. |
| api-version | Valor estático e atualmente precisa de ser "2019-06-01-pré-visualização" |

A resposta da API será uma das duas seguintes:

- 202 Aceito
- Um dos 400 erros de pedido.

O estado de funcionamento pode ser rastreado através da revisão das respostas da API em cabeçalhos de resposta. Para mais informações, consulte [o Estado das operações assíncronas da Azure.](../../azure-resource-manager/management/async-operations.md)

## <a name="monitor-the-failover"></a>Monitorize a falha

Para monitorizar o progresso do failover iniciado pelo utilizador para a sua instância BC, execute a seguinte consulta T-SQL no seu cliente favorito (tal é SSMS) em SQL Managed Instance. Lerá a visão do sistema sys.dm_hadr_fabric_replica_states e reportará réplicas disponíveis no caso. Refresque a mesma consulta depois de iniciar o failover manual.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Antes de iniciar a falha, a sua saída indicará a réplica primária atual no nível de serviço BC contendo um primário e três secundários no Grupo De Disponibilidade AlwaysOn. Após a execução de um failover, executar esta consulta novamente teria que indicar uma mudança do nó primário.

Não poderá ver a mesma saída com o nível de serviço GP acima mostrado para BC. Isto porque o nível de serviço GP baseia-se apenas num único nó. Pode utilizar consulta alternativa T-SQL mostrando o processo SQL iniciado no nó para a instância de nível de serviço GP:

```T-SQL
SELECT sqlserver_start_time, sqlserver_start_time_ms_ticks FROM sys.dm_os_sys_info
```

A curta perda de conectividade do seu cliente durante a execução, tipicamente com duração inferior a um minuto, será a indicação da execução falhada independentemente do nível de serviço.

> [!NOTE]
> A conclusão do processo de failover (não a indisponibilidade efetiva e curta) pode demorar vários minutos de cada vez em caso de cargas de trabalho **de alta intensidade.** Isto porque o motor de instância está a tratar de todas as transações atuais no principal e a recuperar o nó secundário, antes de ser capaz de falhar.

> [!IMPORTANT]
> As limitações funcionais da falha manual iniciada pelo utilizador são:
> - Pode haver um (1) failover iniciado na mesma Instância Gerida a cada **15 minutos**.
> - Para os casos a.C. deve existir quórum de réplicas para que o pedido de caduco seja aceite.
> - Para os casos a.C. não é possível especificar qual réplica secundária legível para iniciar a falha.
> - A falha não será permitida até que a primeira cópia de segurança completa de uma nova base de dados seja concluída por sistemas de backup automatizados.
> - Não será permitida a falha se houver uma recuperação da base de dados em curso.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o teste das suas aplicações para a prontidão na nuvem com [a aplicação de testes Cloud Readiness for Failover Resiliency with SQL Managed Instance](https://youtu.be/FACWYLgYDL8) video recoding.
- Saiba mais sobre a alta disponibilidade de exemplo gerido [Alta disponibilidade para Azure SQL Managed Instance](../database/high-availability-sla.md).
- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
