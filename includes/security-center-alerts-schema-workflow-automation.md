---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272896"
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
|**Nome de exibição de alerta**|String|O nome do alerta.|
|**Tipo de alerta**|String|O tipo de alerta. Os alertas do mesmo tipo devem ter o mesmo valor. Este campo é uma cadeia com chave que representa o tipo de alerta e não de uma instância de alerta. Todos os casos de alerta da mesma lógica de deteção/analítica devem ter o mesmo valor para o tipo de alerta.|
|**Entidade Comprometida**|String|O nome de exibição do recurso mais relacionado com este alerta.|
|**Descrição**|String|Descrição do alerta.|
|**Fim do Tempo Utado**|DateTime|A hora do último evento ou atividade incluída no alerta.  O campo deve ser uma cadeia em conformidade com o formato ISO8601, incluindo informações sobre o fuso horário UTC.|
|**Entidades**|IEnumerable (IEntity)|Uma lista de entidades relacionadas com o alerta. Esta lista pode conter uma mistura de entidades de diversos tipos. O tipo de entidades pode ser qualquer um dos tipos definidos na secção Entidades. As entidades que não estão na lista abaixo também podem ser enviadas, no entanto não é garantido que sejam processadas (o alerta não falhará a validação com novos tipos de entidades).|
|**Propriedades Estendidas**|Dicionário (Corda,Corda)|Os fornecedores podem (opcionalmente) incluir campos personalizados aqui.|
|**Intenção**|Enum|A intenção relacionada com a cadeia de morte por trás do alerta. Para lista de valores apoiados, e explicações das intenções suportadas pelo Azure Security Center, consulte intenções de cadeia de morte apoiadas pelo Azure Security Center, consulte [Intenções](../articles/security-center/alerts-reference.md#intentions).<br/>Este campo pode ter múltiplos valores (separados por vírina).|
|**IsIncident**|Booleano|Este campo determina se o alerta é um incidente (um agrupamento composto de vários alertas) ou um único alerta. O valor predefinido para o campo é 'falso' (o que significa que é um único alerta).|
|**ProcessamentoEndTime**|DateTime|O tempo em que o alerta foi acessível ao utilizador final do produto original que mantém o alerta.|
|**Nome do produto**|String|O nome do produto que publicou este alerta (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS, e assim por diante).|
|**RemediaçãoPassos**|Lista<String>|Itens de ação manual a tomar para remediar o alerta.|
|**Identificadores de Recursos**|Lista (Identificadores de Recursos)|Os identificadores de recursos para este alerta que podem ser utilizados para direcionar o alerta para o grupo de exposição do produto certo (inquilino, espaço de trabalho, subscrição, etc.). Pode haver vários identificadores de diferentes tipos por alerta.|
|**Gravidade**|Enum|A gravidade do alerta, conforme relatado pelo prestador. Valores possíveis: Informativo, Baixo, Médio e Alto.|
|**StartTimeUtc**|DateTime|A hora do primeiro evento ou atividade incluída no alerta. O campo deve ser uma cadeia em conformidade com o formato ISO8601, incluindo informações sobre o fuso horário UTC.|
|**Estado**|Enum|O estado do ciclo de vida do alerta.<br/>Os estatutos apoiados são: Novo, Resolvido, Dispensado, Desconhecido.<br/>Um alerta que especifica um valor diferente das opções suportadas é atribuído ao estatuto 'Desconhecido'.<br/>Um alerta que não especifica um valor é atribuído ao estado 'Novo'.|
|**Systemalertid**|String|O identificador de alerta.|
|**TimeGenerated**|DateTime|O tempo em que o alerta foi gerado pelo prestador de alerta. Caso não seja reportado por fornecedores de alerta interno, um produto pode optar por atribuir o tempo que foi recebido para processamento pelo produto.  O campo deve ser uma cadeia em conformidade com o formato ISO8601, incluindo informações sobre o fuso horário UTC.|
|**Nome do fornecedor**|String|O nome do vendedor que levanta o alerta.|
|||
