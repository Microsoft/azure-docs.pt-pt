---
title: Permitir a exploração de diagnóstico na AZure API para FHIR
description: Este artigo explica como permitir o início de diagnóstico na Azure API para fHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 02/24/2021
ms.openlocfilehash: 73e1db2754749e1fb1142231e7179771bcce8e76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712781"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Ativar o registo de diagnóstico na API Azure para FHIR

Neste artigo, você vai aprender como ativar o login de diagnóstico em AZure API para FHIR e ser capaz de rever algumas consultas de amostra para estes registos. O acesso aos registos de diagnóstico é essencial para qualquer serviço de saúde onde o cumprimento dos requisitos regulamentares (como o HIPAA) é obrigatório. A funcionalidade em Azure API para FHIR que permite registos de diagnóstico são as [**definições de Diagnóstico**](../azure-monitor/essentials/diagnostic-settings.md) no portal Azure. 

## <a name="view-and-download-fhir-metrics-data"></a>Ver e baixar dados de métricas FHIR

Pode ver as métricas em Monitorização | Métricas do portal. As métricas incluem Número de Pedidos, Latência Média, Número de Erros, Tamanho dos Dados, RUs Utilizados, Número de pedidos que excederam a capacidade, e Disponibilidade (em %). A imagem abaixo mostra RUs usados para um ambiente de amostra com muito poucas atividades nos últimos 7 dias. Pode descarregar os dados no formato Json.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Azure API para métricas FHIR do portal" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Ativar registos de auditoria
1. Para ativar a sessão de diagnóstico em Azure API para FHIR, selecione a sua API Azure para o serviço FHIR no portal Azure 
2. Navegue para **definições de Diagnóstico** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Adicione as definições de diagnóstico Azure FHIR." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Selecione **+ Adicionar definição de diagnóstico**

4. Introduza um nome para a definição

5. Selecione o método que pretende utilizar para aceder aos seus registos de diagnóstico:

    1. **Arquivar para uma conta de armazenamento** para auditoria ou inspeção manual. A conta de armazenamento que pretende utilizar já precisa de ser criada.
    2. **Transmita para o centro** de eventos para ingestão por um serviço de terceiros ou solução analítica personalizada. Você precisará criar um espaço de nome de eventos e política de centro de eventos antes de configurar este passo.
    3. **Transmita para o** espaço de trabalho Log Analytics no Azure Monitor. Terá de criar o seu Espaço de Trabalho de Analítica de Logs antes de poder selecionar esta opção.

6. Selecione **AuditLogs** e/ou **AllMetrics**. As métricas incluem nome de serviço, disponibilidade, tamanho de dados, latência total, pedidos totais, erros totais e marcação de tempo. Pode encontrar mais detalhes sobre [métricas suportadas.](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices) 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Definições de diagnóstico Azure FHIR. Selecione AuditLogs e/ou AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Selecione **Guardar**


> [!Note] 
> Pode levar até 15 minutos para os primeiros Registos mostrarem no Log Analytics.  
 
Para obter mais informações sobre como trabalhar com registos de diagnóstico, consulte a documentação do [Registo de Recursos Azure](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>Detalhes do registo de auditoria
Neste momento, a Azure API para o serviço FHIR devolve os seguintes campos no registo de auditoria: 

|Nome do Campo  |Tipo  |Notas  |
|---------|---------|---------|
|Entidade CallerId|Dinâmica|Um saco de propriedade genérica contendo informações de identidade
|CallerIdentityIssuer|String|Emissor 
|CallerIdentityObjectId|String|Object_Id 
|Endereço de chamadaIPAddress|String|O endereço IP do chamador 
|CorrelationId|String| ID de Correlação
|FhirResourceType|String|O tipo de recurso para o qual a operação foi executada
|LogCategoria|String|A categoria de registo (estamos neste momento a devolver 'AuditLogs' LogCategoria)
|Localização|String|A localização do servidor que processou o pedido (por exemplo, South Central US)
|OperaçãoDuração|int|O tempo que demorou a completar este pedido em segundos
|OperationName|String| Descreve o tipo de operação (por exemplo, atualização, tipo de pesquisa)
|RequestUri|String|O pedido URI 
|ResultType|String|Os valores disponíveis atualmente são **Iniciados, Bem Sucedidos** ou **Falhados** 
|Código de Estado|int|O código de estado HTTP. (por exemplo, 200) 
|TimeGenerated|DateTime|Data e hora do evento|
|Propriedades|String| Descreve as propriedades do fhirResourceType
|SourceSystem|String| Sistema fonte (sempre Azure neste caso)
|TenantId|String|ID do inquilino
|Tipo|String|Tipo de registo (sempre MicrosoftHealthcareApisAuditLog neste caso)
|_ResourceId|String|Detalhes sobre o recurso

## <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas básicas de Insights de Aplicação que pode utilizar para explorar os seus dados de registo.

Faça esta consulta para ver os **100** registos mais recentes:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Executar esta consulta para operações de grupo por **FHIR Resource Type**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Faça esta consulta para obter todos os **resultados falhados**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusão 
Ter acesso a registos de diagnóstico é essencial para monitorizar um serviço e fornecer relatórios de conformidade. A Azure API para FHIR permite-lhe fazer estas ações através de registos de diagnóstico. 
 
FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a permitir registos de auditoria para Azure API para FHIR. Em seguida, saiba mais sobre outras configurações adicionais que pode configurar na API Azure para FHIR
 
>[!div class="nextstepaction"]
>[Definições adicionais](azure-api-for-fhir-additional-settings.md)
