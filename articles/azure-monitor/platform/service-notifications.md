---
title: Quais são as notificações de estado de funcionamento do serviço do Azure?
description: Notificações de estado de funcionamento do serviço permitem-lhe ver mensagens de estado de funcionamento de serviço publicadas pelo Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ab4aa2f37d5a883c83b8ee09b5de6551ebf13d2e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995159"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do Estado de funcionamento de serviço com o portal do Azure

Notificações de estado de funcionamento do serviço são publicadas pelo Azure e contêm informações sobre os recursos na sua subscrição. Estas notificações são uma classe secundárias da atividade de registo de eventos e também podem ser encontradas no registo de atividades. Notificações de estado de funcionamento do serviço podem ser informativa ou passíveis de ação, dependendo da classe.

Existem várias classes de notificações de estado de funcionamento do serviço:  

- **Ação necessária:** Azure, pode observar algo invulgar se acontecer na sua conta e trabalhar para resolver isto. O Azure envia uma notificação, seja com detalhes sobre as ações que precisa de efetuar ou como contactar o suporte ou de engenharia do Azure.  
- **Recuperação assistida:** Ocorreu um evento e engenheiros tem confirmado que ainda estão a experienciar impacto. Tem de engenharia do Azure trabalhar consigo diretamente para restaurar os serviços de estado de funcionamento completo.  
- **Incidente:** Um evento que afeta o serviço está atualmente a afetar uma ou mais dos recursos na sua subscrição.  
- **Manutenção:** Uma atividade de manutenção planeada que pode afetar uma ou mais dos recursos na sua subscrição.  
- **Informações:** Utilizam otimizações possíveis que podem ajudar a melhorar o seu recurso. 
- **Segurança:** Urgentes informações relacionadas com segurança relativas à sua soluções executadas no Azure.

Cada notificação de estado de funcionamento do serviço inclui detalhes sobre o âmbito e o impacto para os seus recursos. Os detalhes incluem:

Nome da propriedade | Descrição
-------- | -----------
canais | Um dos seguintes valores: **Admin** ou **operação**.
correlationId | Normalmente, um GUID no formato de cadeia de caracteres. Eventos que pertençam à mesma ação normalmente compartilham a mesma correlationId.
eventDataId | O identificador exclusivo de um evento.
eventName | O título de um evento.
nível | O nível de um evento
resourceProviderName | O nome do fornecedor de recursos para o recurso afetado.
resourceType| O tipo de recurso do recurso afetado.
subStatus | Normalmente, o código de estado HTTP do resto correspondente chamar, mas também pode incluir outras cadeias de caracteres que descreve um subestado. Por exemplo: OK (código de estado HTTP: 200), criado (código de estado HTTP: 201), aceite (código de estado HTTP: 202), não existe conteúdo (código de estado HTTP: 204), pedido incorreto (código de estado HTTP: 400), não encontrado (código de estado HTTP: 404), conflito (código de estado HTTP: 409), erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503) e o tempo limite do Gateway (código de estado HTTP: 504).
eventTimestamp | Timestamp quando o evento foi gerado pelo serviço do Azure a processar o pedido correspondente ao evento.
submissionTimestamp | Timestamp quando o evento se tornou disponível para consulta.
subscriptionId | A subscrição do Azure em que este evento foi registado.
status | A cadeia de caracteres que descreve o estado da operação. Alguns valores comuns são: **Iniciado**, **em curso**, **foi concluída com êxito**, **falha**, **Active**, e **resolvido**.
operationName | O nome da operação.
categoria | Esta propriedade é sempre **ServiceHealth**.
resourceId | O ID de recurso do recurso afetado.
Properties.title | O título localizado para esta comunicação. Inglês é o padrão.
Properties.communication | Os detalhes de localizada da comunicação com a marcação HTML. Inglês é o padrão.
Properties.incidentType | Um dos seguintes valores: **Ação necessária**, **informativa**, **incidente**, **manutenção**, ou **segurança**.
Properties.trackingId | O incidente à qual este evento está associado. Utilize esta opção para correlacionar os eventos relacionados com a um incidente.
Properties.impactedServices | Um blob JSON com caráter de escape que descreve os serviços e regiões afetados pelo incidente. A propriedade inclui uma lista de serviços, cada um com um **ServiceName**e uma lista de regiões afetados, cada um com um **RegionName**.
Properties.defaultLanguageTitle | A comunicação em inglês.
Properties.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto sem formatação.
Properties.stage | Os possíveis valores para **incidente**, e **Security** são **Active Directory,** **resolvido** ou **RCA**. Para **ação necessária** ou **informativo** é o único valor **Active Directory.** Para **manutenção** são: **Active Directory**, **planeadas**, **InProgress**, **cancelada**, **reagendada**, **resolvido**, ou **completo**.
Properties.communicationId | A comunicação com o qual este evento está associado.

### <a name="details-on-service-health-level-information"></a>Informações de nível de detalhes sobre o service health

**É necessária qualquer ação** (properties.incidentType = = ação necessária)
- Informativo - necessário para evitar o impacto para os serviços existentes de ação do administrador
    
**Manutenção** (properties.incidentType = = manutenção)
- Aviso - manutenção de emergência
- Informativo - manutenção planeada padrão

**Informações** (properties.incidentType = = informações)
- Informativa - administrador, pode ser necessário para evitar o impacto para os serviços existentes

**Segurança** (properties.incidentType = = segurança)
- Erro - problemas de amplo acesso a vários serviços em várias regiões estão a afetar um vasto leque de clientes.
- Aviso - problemas de serviços específicos ao aceder ao e/ou regiões específicos estejam a afetar um subconjunto de clientes.
- Informativo - problemas que podem afetar as operações de gestão e/ou latência, não com impacto na disponibilidade do serviço.

**Problemas de serviço** (properties.incidentType = = incidente)
- Erro - problemas de amplo acesso a vários serviços em várias regiões estão a afetar um vasto leque de clientes.
- Aviso - problemas de serviços específicos ao aceder ao e/ou regiões específicos estejam a afetar um subconjunto de clientes.
- Informativo - problemas que podem afetar as operações de gestão e/ou latência, não com impacto na disponibilidade do serviço.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Ver as notificações de estado de funcionamento do serviço no portal do Azure
1.  Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Menu do portal de captura de ecrã do Azure, com o Monitor selecionado](./media/service-notifications/home-monitor.png)

    O Azure Monitor reúne todas as suas monitorização definições e dados numa vista consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

3.  Selecione **alertas**.

    ![Registo de atividades de captura de ecrã do Monitor, com os alertas selecionados](./media/service-notifications/service-health-summary.png)
4. Selecione **+ Adicionar alerta de registo de atividade**e configurar um alerta para garantir que é notificado para notificações de serviço futuras. Para obter mais informações, consulte [criar alertas do registo de atividade nas notificações do serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Passos Seguintes
Receber [sempre que uma notificação de estado de funcionamento do serviço de notificações de alertas](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) é publicado.  
Saiba mais sobre [alertas de registo de atividade](../../azure-monitor/platform/activity-log-alerts.md).

