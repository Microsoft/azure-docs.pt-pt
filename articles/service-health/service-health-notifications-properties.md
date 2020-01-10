---
title: O que são as notificações de integridade do serviço do Azure?
description: As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure.
ms.topic: article
ms.date: 4/12/2018
ms.openlocfilehash: f2d79dc920129241c801c75cc9009b3ba8f34b78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451559"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

As notificações de integridade do serviço são publicadas pelo Azure e contêm informações sobre os recursos em sua assinatura. Essas notificações são uma subclasse de eventos do log de atividades e também podem ser encontradas no log de atividades. As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Há várias classes de notificações de integridade do serviço:  

- **Ação necessária:** O Azure pode perceber algo incomum acontecer em sua conta e trabalhar com você para corrigir isso. O Azure envia uma notificação, detalhando as ações que você precisa tomar ou como entrar em contato com a engenharia ou o suporte do Azure.  
- **Incidente:** Um evento que afeta o serviço está atualmente afetando um ou mais dos recursos em sua assinatura.  
- **Manutenção:** Uma atividade de manutenção planejada que pode afetar um ou mais dos recursos em sua assinatura.  
- **Informações:** Possíveis otimizações que podem ajudar a melhorar o uso de recursos. 
- **Segurança:** Informações urgentes relacionadas à segurança sobre suas soluções que são executadas no Azure.

Cada notificação de integridade do serviço inclui detalhes sobre o escopo e o impacto para seus recursos. Os detalhes incluem:

Nome da propriedade | Descrição
-------- | -----------
meios | Um dos seguintes valores: **admin** ou **Operation**.
correlationId | Geralmente um GUID no formato de cadeia de caracteres. Os eventos que pertencem à mesma ação normalmente compartilham a mesma CorrelationId.
eventDataId | O identificador exclusivo de um evento.
eventName | O título de um evento.
level | O nível de um evento
resourceProviderName | O nome do provedor de recursos para o recurso afetado.
resourceType| O tipo de recurso do recurso afetado.
subStatus | Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevem um substatus. Por exemplo: OK (código de status HTTP: 200), criado (código de status HTTP: 201), aceito (código de status HTTP: 202), nenhum conteúdo (código de status HTTP: 204), solicitação incorreta (código de status http: 400), não encontrado (código de status HTTP: 404), conflito (código de status HTTP: 409), servidor interno Erro (código de status HTTP: 500), Serviço indisponível (código de status HTTP: 503) e tempo limite do gateway (código de status HTTP: 504).
eventTimestamp | Carimbo de data/hora quando o evento foi gerado pelo serviço do Azure processando a solicitação correspondente ao evento.
submissionTimestamp | Carimbo de data/hora quando o evento ficou disponível para consulta.
subscriptionId | A assinatura do Azure na qual esse evento foi registrado.
status | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: **iniciado**, **em andamento**, com **êxito**, **com falha**, **ativo**e **resolvido**.
operationName | O nome da operação.
categoria | Essa propriedade sempre é o **perhealth**.
resourceId | A ID de recurso do recurso afetado.
Propriedades. title | O título localizado para essa comunicação. Inglês é o padrão.
Propriedades. Communication | Os detalhes localizados da comunicação com marcação HTML. Inglês é o padrão.
Properties.incidentType | Um dos seguintes valores: **ActionRequired**, **informativo**, **incidente**, **manutenção**ou **segurança**.
Properties.trackingId | O incidente ao qual esse evento está associado. Use isso para correlacionar os eventos relacionados a um incidente.
Properties.impactedServices | Um blob JSON de escape que descreve os serviços e regiões impactados pelo incidente. A propriedade inclui uma lista de serviços, cada um deles com um **ServiceName**, e uma lista de regiões afetadas, cada uma delas com **RegionName**.
Properties.defaultLanguageTitle | A comunicação em inglês.
Properties.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto sem formatação.
Propriedades. Stage | Os valores possíveis para **incidente**e **segurança** são **ativo,** **resolvido** ou **RCA**. Para **ActionRequired** ou **informativo** , o único valor é **ativo.** Para **manutenção** , eles são: **ativo**, **planejado**, em **andamento**, **cancelado**, **reagendado**, **resolvido**ou **concluído**.
Properties.communicationId | A comunicação com a qual esse evento está associado.

### <a name="details-on-service-health-level-information"></a>Detalhes sobre informações de nível de integridade do serviço

**Ação necessária** (Propriedades. incidenttype = = ActionRequired)
- Informativo-a ação do administrador é necessária para evitar o impacto nos serviços existentes.
    
**Manutenção** (Propriedades. incidenttype = = manutenção)
- Aviso-manutenção de emergência
- Informativo-manutenção planejada padrão

**Informações** (Propriedades. incidenttype = = Information)
- Informativo-o administrador pode ser solicitado a evitar o impacto nos serviços existentes.

**Security** (Propriedades. incidenttype = = Security)
- Aviso-consultoria de segurança que afeta os serviços existentes e pode exigir ação do administrador.
- Informativo-consultoria de segurança que afeta os serviços existentes.

**Problemas de serviço** (Propriedades. incidenttype = = incidente)
- Os problemas abrangentes de erro que acessam vários serviços em várias regiões estão afetando um amplo conjunto de clientes.
- Aviso-problemas ao acessar serviços específicos e/ou regiões específicas estão afetando um subconjunto de clientes.
- Informações-problemas que afetam as operações de gerenciamento e/ou latência, sem afetar a disponibilidade do serviço.
