---
title: Unificar vários recursos de aplicação do Monitor Azure Microsoft Docs
description: Este artigo fornece detalhes sobre como usar uma função em Registos monitores Azure para consultar vários recursos de Insights de Aplicação e visualizar esses dados.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 40ce2844e33c9a71f87e434a6a3e9f8e0f7e3cc6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322113"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificar vários recursos de insights de aplicação do monitor Azure 
Este artigo descreve como consultar e visualizar todos os dados de registo de insights de aplicação num só local, mesmo quando estão em diferentes subscrições do Azure, como substituto da depreciação do Conector de Insights de Aplicação. O número de recursos de Application Insights que pode incluir numa única consulta está limitado a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem recomendada para consultar vários recursos de Insights de Aplicação 
A listagem de vários recursos de Insights de Aplicação numa consulta pode ser complicada e difícil de manter. Em vez disso, pode aproveitar a função para separar a lógica de consulta das aplicações de deteção.  

Este exemplo demonstra como pode monitorizar vários recursos de Insights de Aplicação e visualizar a contagem de pedidos falhados pelo nome da aplicação.

Crie uma função utilizando o operador sindical com a lista de aplicações e, em seguida, guarde a consulta no seu espaço de trabalho em função das aplicações de *pseudónimoS.* 

Pode modificar as aplicações listadas a qualquer momento no portal navegando para o explorador de consultas no seu espaço de trabalho e selecionando a função para edição e, em seguida, poupança, ou usando o `SavedSearch` cmdlet PowerShell. 

>[!NOTE]
>Este método não pode ser usado com alertas de registo porque a validação de acesso dos recursos da regra de alerta, incluindo espaços de trabalho e aplicações, é realizada no momento de criação de alerta. A adição de novos recursos à função após a criação de alerta não é suportada. Se preferir utilizar a função para a deteção de recursos em alertas de registo, tem de editar a regra de alerta no portal ou com um modelo de Gestor de Recursos para atualizar os recursos agrafado. Em alternativa, pode incluir a lista de recursos na consulta de alerta de registo.

O `withsource= SourceApp` comando adiciona uma coluna aos resultados que designam a aplicação que enviou o registo. O operador de parse é opcional neste exemplo e utiliza para extrair o nome da aplicação da propriedade SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Está agora pronto a utilizar aplicaçõesScoping função na consulta de recursos cruzados:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A consulta utiliza o esquema de Insights de Aplicação, embora a consulta seja executada no espaço de trabalho uma vez que a função de aplicaçõesScoping devolve a estrutura de dados de Insights de Aplicação. A função é alias devolve a união dos pedidos de todas as candidaturas definidas. A consulta então filtra para pedidos falhados e visualiza as tendências por aplicação.

![Exemplo de resultados de consulta cruzada](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[A consulta de recursos cruzados](./cross-workspace-query.md) nos alertas de registo é suportada na nova [API agendada deQueryRules](/rest/api/monitor/scheduledqueryrules). Por predefinição, o Azure Monitor utiliza a [api de alerta de log analytics para](../platform/api-alerts.md) criar novas regras de alerta de registo a partir do portal Azure, a menos que mude de [API de alertas](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)de registo legados . Após a troca, a nova API torna-se o padrão para novas regras de alerta no portal Azure e permite criar regras de alerta de registo de consulta de recursos cruzados. Pode criar regras de alerta de registo [de consulta de recursos cruzados](./cross-workspace-query.md) sem fazer o switch utilizando o modelo ARM para a API agendada para a [API de Tesouros agendados](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas esta regra de alerta é gerível embora [agendada API](/rest/api/monitor/scheduledqueryrules) e não a partir do portal Azure.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Insights de Aplicação e Diferenças de esquemas de espaço de trabalho do Log Analytics
A tabela seguinte mostra as diferenças de esquema entre Log Analytics e Application Insights.  

| Log Analytics propriedades do espaço de trabalho| Propriedades de recursos de Insights de Aplicação|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationID | appId|
| ApplicationName | appName|
| AplicaçãoTypeVersion | application_Version |
| DisponibilidadeContando | itemOconse |
| DisponibilidadeDuração | duration |
| DisponibilidadeSSessage | message |
| DisponibilidadeRunLocation | localização |
| DisponibilidadeTestId | ID |
| DisponibilidadeTestName | name |
| DisponibilidadeTimestamp | carimbo de data/hora |
| Browser | client_browser |
| Cidade | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País | client_CountryOrRegion | 
| PersonalEventCount | itemOconse | 
| CustomEventDimensions | customDimensionais |
| Nome personalizado | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExcepçãoCount | itemOconse | 
| ExceçãoHandledAt | handledAt |
| Exceção | message | 
| ExcepçãoType | tipo |
| OperaçãoID | operation_id |
| OperationName | operation_Name | 
| SO | client_OS | 
| PageViewCount | itemOconse |
| PageViewDuration | duration | 
| Nome PageView | name | 
| ParentOperationID | operation_Id | 
| PedidoCount | itemOconse | 
| PedidoDuração | duration | 
| RequestID | ID | 
| Nome de pedido | name | 
| PedidoSSuccess | exito | 
| Código de Resposta | resultadosDesco | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetriaTYpe | tipo |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Passos seguintes

Utilize [a Pesquisa de Registo](./log-query-overview.md) para ver informações detalhadas para as suas aplicações Application Insights.

