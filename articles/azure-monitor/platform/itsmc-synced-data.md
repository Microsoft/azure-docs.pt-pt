---
title: Dados sincronizados do seu produto ITSM para o ESPAÇO DE TRABALHO DE LA
description: Este artigo fornece uma visão geral dos Dados sincronizados do seu produto ITSM para o ESPAÇO DE TRABALHO DE LA.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97811991"
---
# <a name="data-synced-from-your-itsm-product"></a>Dados sincronizados com o seu produto ITSM

Incidentes e pedidos de alteração são sincronizados da sua ferramenta ITSM para o seu espaço de trabalho Log Analytics, com base na configuração da ligação (utilizando o campo "Sync Data"):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [Gestor de serviços do Centro de Sistema](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Dados sincronizados

Esta secção mostra alguns exemplos de dados recolhidos pelo ITSMC.

Os campos em **ServiceDesk_CL** variam dependendo do tipo de artigo de trabalho que importa para o Log Analytics. Aqui está uma lista de campos para dois tipos de artigos de trabalho:

**Artigo de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incidente"

**Campos**

- Nome de ServiçoDeskConnectionName
- ID de serviço
- Estado
- Urgência
- Impacto
- Prioridade
- Escalamento
- Criada Por
- Resolvido por
- Fechado por
- Origem
- Atribuído a
- Categoria
- Título
- Descrição
- Data de Criação
- Data de Fecho
- Data de Resolução
- Data da Última Modificação
- Computador

**Artigo de trabalho:** **Alterar pedidos**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- Nome de ServiçoDeskConnectionName
- ID de serviço
- Criada Por
- Fechado por
- Origem
- Atribuído a
- Título
- Tipo
- Categoria
- Estado
- Escalamento
- Estado do Conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de Criação
- Data de Fecho
- Data da Última Modificação
- Data Solicitada
- Data de início planeada
- Data de fim planeada
- Data de início do trabalho
- Data de fim do trabalho
- Description
- Computador

## <a name="servicenow-example"></a>Exemplo de ServiceNow 
### <a name="output-data-for-a-servicenow-incident"></a>Dados de saída para um incidente do ServiceNow

| Log Analytics | Campo ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Aberto por |
| ResolvedBy_s | Resolvido por|
| ClosedBy_s  | Fechado por |
| Source_s| Tipo de contacto |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Breve descrição |
| Description_s|  Notas |
| CreatedDate_t|  Inaugurado |
| ClosedDate_t| fechado|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

### <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para um pedido de alteração serviceNow

| Log Analytics | Campo ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Breve descrição |
| Type_s|  Tipo |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por data |
| ClosedDate_t | Data fechada |
| PlannedStartDate_t  | Data de início prevista |
| PlannedEndDate_t  | Data de fim prevista |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de fim real|
| Description_s | Description |
| Computador  | Item de configuração |

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)
