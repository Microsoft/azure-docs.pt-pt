---
title: O que são notificações de saúde do serviço Azure?
description: As notificações de saúde do serviço permitem-lhe visualizar mensagens de saúde de serviço publicadas pela Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86529017"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>Utilize o portal Azure para ver notificações de saúde do serviço

As notificações de saúde do serviço são publicadas pela Azure e contêm informações sobre os recursos sob a sua subscrição. Estas notificações são uma sub-classe de eventos de registo de atividade, e também podem ser encontradas no registo de atividades. As notificações de saúde do serviço podem ser informativas ou accuais, dependendo da classe.

Existem várias classes de notificações de saúde de serviço:  

- **Ações necessárias:** O Azure pode notar que algo incomum acontece na tua conta, e trabalha contigo para resolver isto. O Azure envia-lhe uma notificação, detalhando as ações que precisa de tomar ou como contactar a engenharia ou suporte da Azure.  
- **Incidente:** Um evento que impacta o serviço está atualmente a afetar um ou mais dos recursos na sua subscrição.  
- **Manutenção:** Uma atividade de manutenção planeada que pode afetar um ou mais dos recursos sob a sua subscrição.  
- **Informação:** Otimizações potenciais que podem ajudar a melhorar o uso do seu recurso. 
- **Segurança:** Informações urgentes relacionadas com a segurança sobre as suas soluções que funcionam no Azure.

Cada notificação de saúde do serviço inclui detalhes sobre o âmbito e impacto dos seus recursos. Os detalhes incluem:

Nome da propriedade | Description
-------- | -----------
canais | Um dos seguintes valores: **Administração** ou **Operação**.
correlationId | Normalmente um GUID no formato de corda. Eventos que pertencem à mesma ação geralmente partilham a mesma correlação.
eventDataId | O identificador único de um evento.
nome de evento | O título de um evento.
nível | O nível de um evento
nome de recursoProviderName | O nome do fornecedor de recursos para o recurso impactado.
resourceType| O tipo de recurso do recurso impactado.
subStatus | Normalmente, o código de estado HTTP da chamada REST correspondente, mas também pode incluir outras cadeias que descrevem um substatus. Por exemplo: OK (HTTP Status Code: 200), Criado (CÓDIGO DE Estado HTTP: 201), Aceite (CÓDIGO DE Estado HTTP: 202), Sem Conteúdo (Código de Estado HTTP: 204), Mau pedido (Código de Estado HTTP: 400), Não Encontrado (CÓDIGO DE Estado HTTP: 404), Conflito (Código de Estado HTTP: 409), Erro do Servidor Interno (Código de Estado HTTP: 500), Serviço Indisponível (Código de Estado HTTP: 503) e Prazo de Gateway (Código de Estado HTTP: 504).
eventTimestamp | Timetamp quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento.
submissãoTimestamp | Timetamp quando o evento ficou disponível para consulta.
subscriptionId | A assinatura Azure em que este evento foi registado.
status | String descrevendo o estado da operação. Alguns valores comuns são: **Iniciado,** Em **Progresso,** **Bem sucedido,** **Falhado,** **Ativo** e **Resolvido.**
operationName | O nome da operação.
categoria | Esta propriedade é sempre **ServiceHealth.**
resourceId | O ID de recursos do recurso impactado.
Propriedades.título | O título localizado para esta comunicação. Inglês é o padrão.
Propriedades.comunicação | Os detalhes localizados da comunicação com a marcação HTML. Inglês é o padrão.
Propriedades.incidentType | Um dos seguintes valores: **ActionRequired,** **Informational,** **Incident,** **Maintenance,** or **Security**.
Propriedades.trackingId | O incidente com o qual este evento está associado. Use isto para correlacionar os eventos relacionados com um incidente.
Propriedades.ImpactedServs | Uma bolha JSON em fuga que descreve os serviços e regiões afetados pelo incidente. O imóvel inclui uma lista de serviços, cada um dos quais tem um Nome de **Serviço,** e uma lista de regiões com impacto, cada uma das quais tem um **Nome Regional.**
Propriedades.defaultLanguageTitle | A comunicação em inglês.
Propriedades.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto simples.
Propriedades.palco | Os valores possíveis para **Incidente**, e **Segurança** são **Ativos,** **Resolvidos** ou **RCA**. Para **ActionRequired** ou **Informational** o único valor é **Ative.** Para **manutenção** são: **Ativos,** **Planeados, InProgress, Cancelados,** **Reagendados,** **Resolvidos** ou **Concluídos.**  
Propriedades.communicationId | A comunicação com a qual este evento está associado.

### <a name="details-on-service-health-level-information"></a>Detalhes sobre informações sobre o nível de saúde do serviço

**Ação Necessária** (propriedades.incidentType == ActionRequired)
- Informação - É necessária uma ação de administrador para evitar o impacto nos serviços existentes.
    
**Manutenção** (propriedades.incidentType == Manutenção)
- Aviso - Manutenção de emergência
- Informação - Manutenção padrão planeada

**Informação** (propriedades.incidentType == Informação)
- Informativo - O administrador pode ser necessário para evitar o impacto nos serviços existentes.

**Segurança** (propriedades.incidentType == Segurança)
- Aviso - Aviso de segurança que afeta os serviços existentes e pode exigir ação do administrador.
- Informational - Aviso de segurança que afeta os serviços existentes.

**Problemas de serviço** (propriedades.incidentType == Incidente)
- Error - Problemas generalizados de acesso a múltiplos serviços em várias regiões estão a afetar um vasto conjunto de clientes.
- Aviso - As questões que acedem a serviços específicos e/ou regiões específicas estão a afetar um subconjunto de clientes.
- Informação - Problemas com impacto nas operações de gestão e/ou latência, não com impacto na disponibilidade do serviço.
