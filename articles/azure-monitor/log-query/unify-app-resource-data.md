---
title: Unifique vários recursos de Insights de Aplicação do Monitor Azure / Microsoft Docs
description: Este artigo fornece detalhes sobre como usar uma função em Registos do Monitor Azure para consultar vários recursos da Aplicação Insights e visualizar esses dados.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137503"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unifique vários recursos de Insights de Aplicação do Monitor Azure 
Este artigo descreve como consultar e visualizar todos os dados de registo de informação sobre os seus Insights de Aplicação num só local, mesmo quando estão em diferentes subscrições do Azure, como substituto da depreciação do Conector insights de aplicação. O número de recursos da Application Insights que pode incluir numa única consulta está limitado a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem recomendada para consulta de vários recursos de Insights de Aplicação 
A listagem de múltiplos recursos de Insights de Aplicação numa consulta pode ser complicada e difícil de manter. Em vez disso, pode aproveitar a função para separar a lógica de consulta das aplicações que se detecm.  

Este exemplo demonstra como pode monitorizar vários recursos de Insights de Aplicação e visualizar a contagem de pedidos falhados pelo nome da aplicação.

Crie uma função utilizando o operador sindical com a lista de aplicações e, em seguida, guarde a consulta no seu espaço de trabalho em função das aplicações do *pseudónimoSS .* 

Pode modificar as aplicações listadas a qualquer momento no portal navegando para o explorador de Consulta no seu `SavedSearch` espaço de trabalho e selecionando a função para edição e, em seguida, para poupar, ou usando o cmdlet PowerShell. 

>[!NOTE]
>Este método não pode ser utilizado com alertas de registo porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicações, é realizada em tempo de criação de alerta. Adicionar novos recursos à função após a criação de alerta não é suportado. Se preferir utilizar a função de deteção de recursos em alertas de registo, tem de editar a regra de alerta no portal ou com um modelo de Gestor de Recursos para atualizar os recursos com âmbito de aplicação. Em alternativa, pode incluir a lista de recursos na consulta de alerta de registo.

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

Está agora pronto para utilizar a função applicationsScoping na consulta de recursos cruzados:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A consulta utiliza o esquema Application Insights, embora a consulta seja executada no espaço de trabalho uma vez que a função aplicaçãoScoping devolve a estrutura de dados do Application Insights. O pseudónimo de função devolve a união dos pedidos de todas as candidaturas definidas. A consulta filtra então pedidos falhados e visualiza as tendências por aplicação.

![Exemplo de resultados de consulta cruzada](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[A consulta de recursos cruzados](../log-query/cross-workspace-query.md) em alertas de registo é suportada na nova [API agendadaQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, o Azure Monitor utiliza o [legado Log Analytics Alert API](../platform/api-alerts.md) para criar novas regras de alerta de log a partir do portal Azure, a menos que troque do legado Log [Alerts API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a troca, a nova API torna-se o padrão para novas regras de alerta no portal Azure e permite criar regras de alerta de consulta de recursos cruzados. Pode criar regras de alerta de consulta [de recursos cruzados](../log-query/cross-workspace-query.md) sem elo, utilizando o [modelo ARM para a API agendada](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , mas esta regra de alerta é controlável apesar da [API agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não do portal Azure.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Insights de aplicação e log analytics diferenças de esquemas de schema
A tabela que se segue mostra as diferenças de esquemaentre Log Analytics e Application Insights.  

| Propriedades do espaço de trabalho log Analytics| Propriedades de recursos de Insights de Aplicação|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationID | appId|
| ApplicationName | nome de app|
| AplicaçãoTypeVersion | application_Version |
| Contagem de disponibilidades | itensCount |
| DisponibilidadeDura | duration |
| Mensagem de disponibilidade | message |
| DisponibilidadeRunLocation | localização |
| DisponibilidadeTestId | ID |
| Nome de Teste de Disponibilidade | nome |
| DisponibilidadeTimestamp | carimbo de data/hora |
| Browser | client_browser |
| Localidade | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País | client_CountryOrRegion | 
| CustomEventCount | itensCount | 
| CustomEventDimensions | dimensões personalizadas |
| Nome personalizado do evento | nome | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExcepçõesContagem | itensCount | 
| ExcepçõesHandledAt | handledAt |
| Mensagem de exceção | message | 
| Tipo de exceção | tipo |
| OperaçãoID | operation_id |
| OperationName | operation_Name | 
| SO | client_OS | 
| PageViewCount | itensCount |
| PageViewDura | duration | 
| Nome pageview | nome | 
| ParentOperationID | operation_Id | 
| Contagem de Pedidos | itensCount | 
| Duração do pedido | duration | 
| RequestID | ID | 
| Nome de pedido | nome | 
| RequestSuccess | exito | 
| Código de Resposta | resultadoCódigo | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessãoId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemettryTYpe | tipo |
| do IdP | url |
| UserAccountid | user_AccountId |

## <a name="next-steps"></a>Passos seguintes

Utilize a Pesquisa de [Registos](../../azure-monitor/log-query/log-query-overview.md) para visualizar informações detalhadas para as suas aplicações De insights de aplicação.
