---
title: Ativar o registo de diagnóstico na API Azure para fHIR®
description: Este artigo explica como permitir o início de diagnóstico na Azure API para fHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 948ca03b5bf503c884df5df56c61951b381874a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871702"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Ativar o registo de diagnóstico na API Azure para fHIR®

Neste artigo, você aprenderá como ativar o login de diagnóstico em API Azure para FHIR® e poderá rever algumas consultas de amostra para estes registos. O acesso aos registos de diagnóstico é essencial para qualquer serviço de saúde onde o cumprimento dos requisitos regulamentares (como o HIPAA) é obrigatório. A funcionalidade em Azure API para FHIR® que permite registos de diagnóstico são as [**definições de Diagnóstico**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) no portal Azure. 

## <a name="enable-audit-logs"></a>Ativar registos de auditoria
1. Para ativar a sessão de diagnóstico em Azure API para FHIR®, selecione o seu API AZure para o serviço FHIR® no portal Azure 
2. Navegar para **definições de**  
 ![ diagnóstico Definições de diagnóstico](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Selecione **+ Adicionar definição de diagnóstico**

4. Introduza um nome para a definição

5. Selecione o método que pretende utilizar para aceder aos seus registos de diagnóstico:

    1. **Arquivar para uma conta de armazenamento** para auditoria ou inspeção manual. A conta de armazenamento que pretende utilizar já precisa de ser criada.
    2. **Transmita para o centro** de eventos para ingestão por um serviço de terceiros ou solução analítica personalizada. Você precisará criar um espaço de nome de eventos e política de centro de eventos antes de configurar este passo.
    3. **Transmita para o** espaço de trabalho Log Analytics no Azure Monitor. Terá de criar o seu Espaço de Trabalho de Analítica de Logs antes de poder selecionar esta opção.

6. Selecione **AuditLogs** e quaisquer métricas que pretenda capturar

7. Clicar em Guardar

> [!Note] 
> Pode levar até 15 minutos para os primeiros Registos mostrarem no Log Analytics.  
 
Para obter mais informações sobre como trabalhar com registos de diagnóstico, consulte a documentação do [Registo de Recursos Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview)

## <a name="audit-log-details"></a>Detalhes do registo de auditoria
Neste momento, a Azure API para o serviço FHIR® devolve os seguintes campos no registo de auditoria: 

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
|ResultadoType|String|Os valores disponíveis atualmente são **Iniciados, Bem Sucedidos**ou **Falhados** **Started**
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
Ter acesso a registos de diagnóstico é essencial para monitorizar um serviço e fornecer relatórios de conformidade. A Azure API para FHIR® permite-lhe fazer estas ações através de registos de diagnóstico. 
 
FHIR® é a marca registada do HL7 e é utilizada com a permissão do HL7.

## <a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu a permitir registos de auditoria da Azure API para fHIR®. Em seguida, saiba mais sobre outras configurações adicionais que pode configurar na API Azure para FHIR
 
>[!div class="nextstepaction"]
>[Definições adicionais](azure-api-for-fhir-additional-settings.md)