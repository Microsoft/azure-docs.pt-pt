---
title: O que são as notificações de saúde do serviço Azure?
description: As notificações de saúde do serviço permitem-lhe visualizar mensagens de saúde de serviço publicadas pelo Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653973"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

As notificações de saúde de serviço são publicadas pela Azure e contêm informações sobre os recursos sob a sua subscrição. Estas notificações são uma subclasse de eventos de registo de atividade, e também podem ser encontradas no registo de atividade. As notificações de saúde do serviço podem ser informativas ou exequíveis, dependendo da classe.

Existem várias classes de notificações de saúde de serviço:  

- **Ação necessária:** O Azure pode notar que algo incomum acontece na tua conta, e trabalhar contigo para resolver isto. A Azure envia-lhe uma notificação, detalhando as ações que precisa de tomar ou como contactar a engenharia Azure ou o suporte.  
- **Incidente:** Um evento que impacta o serviço está atualmente a afetar um ou mais dos recursos da sua subscrição.  
- **Manutenção:** Uma atividade de manutenção planeada que pode afetar um ou mais dos recursos sob a sua subscrição.  
- **Informação:** Otimizações potenciais que podem ajudar a melhorar o uso do seu recurso. 
- **Segurança:** Informações urgentes relacionadas com a segurança sobre as suas soluções que funcionam no Azure.

Cada notificação de saúde do serviço inclui detalhes sobre o âmbito e impacto nos seus recursos. Os detalhes incluem:

Nome da propriedade | Descrição
-------- | -----------
canais | Um dos seguintes valores: **Administrador** ou **Operação**.
correlationId | Normalmente um GUID no formato de cordas. Os eventos que pertencem à mesma ação geralmente partilham a mesma correlação.
eventoDataId | O identificador único de um evento.
nome de evento | O título de um evento.
nível | O nível de um evento
nome fornecedor de recursos | O nome do fornecedor de recursos para o recurso impactado.
resourceType| O tipo de recurso do recurso impactado.
subEstatuto | Normalmente, o código de estado HTTP da chamada REPOUSA correspondente, mas também pode incluir outras cordas descrevendo um subestatuto. Por exemplo: OK (Código de Estado HTTP: 200), Criado (Código de Estado HTTP: 201), Aceito (Código de Estado HTTP: 202), Sem Conteúdo (Código de Estado HTTP: 204), Pedido De Mau Pedido (Código de Estado HTTP: 400), Não Encontrado (Código de Estado HTTP: 404), Conflito (Código de Estado HTTP: 409), Servidor Interno Erro (Código de Estado HTTP: 500), Serviço Indisponível (Código de Estado HTTP: 503) e Prazo de Gateway (Código de Estado HTTP: 504).
eventoTimestamp | Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento.
submissãoTimestamp | Hora do tempo quando o evento ficou disponível para consulta.
subscriptionId | A subscrição azure em que este evento foi registado.
status | Cordas descrevendo o estado da operação. Alguns valores comuns são: **Iniciado,** **Em Progresso,** **Bem Sucedido,** **Falhado,** **Ativo**e **Resolvido.**
operationName | O nome da operação.
categoria | Esta propriedade é sempre **ServiceHealth**.
resourceId | A identificação de recursos do recurso impactado.
Propriedades.título | O título localizado para esta comunicação. Inglês é o padrão.
Propriedades.comunicação | Os detalhes localizados da comunicação com marcação HTML. Inglês é o padrão.
Propriedades.incidentType | Um dos seguintes valores: **Ação Requerida,** **Informacional,** **Incidente,** **Manutenção**ou **Segurança.**
Propriedades.trackingId | O incidente com o qual este evento está associado. Use isto para correlacionar os eventos relacionados com um incidente.
Propriedades.impactouServiços | Uma bolha jSON em fuga que descreve os serviços e regiões afetados pelo incidente. A propriedade inclui uma lista de serviços, cada um dos quais tem um Nome de **Serviço,** e uma lista de regiões afetadas, cada uma das quais tem um **Nome de Região**.
Propriedades.defaultLanguageTitle | A comunicação em inglês.
Propriedades.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto simples.
Propriedades.palco | Os valores possíveis para **Incidentes**, e **Segurança** são **Ativos,** **Resolvidos** ou **RCA**. Para **ação Necessária** ou **Informacional,** o único valor é **Ativo.** Para **manutenção** são: **Ativo,** **Planeado,** **InProgress,** **Cancelado,** **Reagendado,** **Resolvido**ou **Completo**.
Propriedades.communicationId | A comunicação com a qual este evento está associado.

### <a name="details-on-service-health-level-information"></a>Detalhes sobre informações sobre o nível de saúde do serviço

**Ação Requerida** (propriedades.incidentType == ActionRequired)
- Informação - É necessária uma ação de administrador para evitar o impacto nos serviços existentes.
    
**Manutenção** (propriedades.incidentType == Manutenção)
- Aviso - Manutenção de emergência
- Informação - Manutenção planeada padrão

**Informação** (propriedades.incidentType == Informação)
- Informação - O administrador pode ser obrigado a prevenir o impacto nos serviços existentes.

**Segurança** (propriedades.incidentType == Segurança)
- Aviso - Aviso de segurança que afeta os serviços existentes e pode exigir uma ação do administrador.
- Informação - Aviso de segurança que afeta os serviços existentes.

**Problemas de serviço** (propriedades.incidentType == Incidente)
- Erro - Problemas generalizados de acesso a vários serviços em várias regiões estão a afetar um vasto conjunto de clientes.
- Aviso - As questões de acesso a serviços específicos e/ou regiões específicas estão a afetar um subconjunto de clientes.
- Informação - Questões que afetam as operações de gestão e/ou a latência, não afetando a disponibilidade do serviço.
