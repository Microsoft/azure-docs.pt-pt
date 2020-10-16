---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400940"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Tipo de dados|Descrição|
|----|----|----|
|**Nome de Alertadisplay**|Cadeia|O nome do alerta.|
|**AlertaType**|Cadeia|O tipo de alerta. Os alertas do mesmo tipo devem ter o mesmo valor. Este campo é uma corda com chave que representa o tipo de alerta e não de uma instância de alerta. Todas as instâncias de alerta da mesma lógica de deteção/analítico devem ter o mesmo valor para o tipo de alerta.|
|**Entidade Comprometida**|Cadeia|O nome de exibição do recurso mais relacionado com este alerta.|
|**Descrição**|Cadeia|Descrição do alerta.|
|**EndTimeUtc**|DateTime|A hora do último evento ou atividade incluída no alerta.  O campo deve ser uma cadeia que esteja em conformidade com o formato ISO8601, incluindo informações sobre o ausmo horário UTC.|
|**Entidades**|IEnumerable (IEntity)|Uma lista de entidades relacionadas com o alerta. Esta lista pode conter uma mistura de entidades de diversos tipos. O tipo de entidades pode ser qualquer um dos tipos definidos na secção Entidades. As entidades que não constam da lista abaixo também podem ser enviadas, no entanto não é garantido que sejam processadas (o alerta não falhará a validação com novos tipos de entidades).|
|**Extensões**|Dicionário (String,String)|Os fornecedores podem (opcionalmente) incluir campos personalizados aqui.|
|**Intenção**|Enumeração|A corrente de morte está por detrás do alerta. Para lista de valores apoiados e explicações das intenções de cadeia de morte apoiadas pelo Azure Security Center, consulte [Intenções](../articles/security-center/alerts-reference.md#intentions).<br/>Este campo pode ter múltiplos valores (separados por vírgula).|
|**IsIncident**|Booleano|Este campo determina se o alerta é um incidente (um agrupamento composto de vários alertas) ou um único alerta. O valor predefinido para o campo é 'falso' (o que significa que é um único alerta).|
|**ProcessamentoEndTime**|DateTime|O momento em que o alerta foi acessível ao utilizador final no produto original que mantém o alerta.|
|**Nome do produto**|Cadeia|O nome do produto que publicou este alerta (Azure Security Center, Azure ATP, Microsoft Defender ATP, MCAS, e assim por diante).|
|**ReparaçãoSteps**|Lista<String>|Itens de ação manual a tomar para remediar o alerta.|
|**Identificadores de Recursos**|Lista (Identificadores de Recursos)|Os identificadores de recursos para este alerta que podem ser usados para direcionar o alerta para o grupo de exposição do produto certo (inquilino, espaço de trabalho, subscrição, etc.). Pode haver vários identificadores de diferente tipo por alerta.|
|**Gravidade**|Enumeração|A gravidade do alerta, conforme reportado pelo provedor. Valores possíveis: Informativo, Baixo, Médio e Alto.|
|**StartTimeUtc**|DateTime|A hora do primeiro evento ou atividade incluída no alerta. O campo deve ser uma cadeia que esteja em conformidade com o formato ISO8601, incluindo informações sobre o ausmo horário UTC.|
|**Estado**|Enumeração|O estado do ciclo de vida do alerta.<br/>Os estatutos apoiados são: Novos, Resolvidos, Dispensados, Desconhecidos.<br/>Um alerta que especifica um valor diferente das opções suportadas é atribuído o estado 'Desconhecido'.<br/>Um alerta que não especifica um valor é atribuído o estado 'Novo'.|
|**SystemAlertId**|Cadeia|O identificador de alerta.|
|**TimeGenerated**|DateTime|O momento em que o alerta foi gerado pelo fornecedor de alerta. Se não for reportado por fornecedores de alerta interno, um produto pode optar por atribuir o tempo que foi recebido para processamento pelo produto.  O campo deve ser uma cadeia que esteja em conformidade com o formato ISO8601, incluindo informações sobre o ausmo horário UTC.|
|**Nome do fornecedor**|Cadeia|O nome do vendedor que levanta o alerta.|
|||
