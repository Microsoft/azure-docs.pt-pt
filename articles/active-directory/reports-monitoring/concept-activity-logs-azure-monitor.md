---
title: Azure Active Directory logs de atividades em Azure Monitor | Microsoft Docs
description: Introdução à Azure Active Directory de logs de atividades no Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/22/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8fb570d328c7391c269d4a2aa91c69003b1cfc8
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989896"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Logs de atividades do Azure AD no Azure Monitor

Você pode rotear logs de atividade do Azure Active Directory (Azure AD) para vários pontos de extremidade para retenção de longo prazo e insights de dados. Esse recurso permite que você:

* Arquive logs de atividades do Azure AD em uma conta de armazenamento do Azure para manter os dados por um longo tempo.
* Transmita os logs de atividades do Azure AD para um hub de eventos do Azure para análise, usando ferramentas de SIEM (gerenciamento de eventos e informações de segurança) populares, como Splunk e QRadar.
* Integre os logs de atividades do Azure AD com suas próprias soluções de log personalizadas transmitindo-as a um hub de eventos.
* Envie logs de atividades do Azure AD para Azure Monitor logs para permitir visualizações avançadas, monitoramento e alertas nos dados conectados.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Relatórios suportados

Você pode rotear logs de auditoria e logs de entrada do Azure AD para sua conta de armazenamento do Azure, Hub de eventos, Azure Monitor logs ou solução personalizada usando esse recurso. 

* **Logs de auditoria**: O [relatório de atividade de logs de auditoria](concept-audit-logs.md) fornece acesso ao histórico de todas as tarefas que são executadas em seu locatário.
* **Logs de entrada**: Com o [relatório de atividade de entrada](concept-sign-ins.md), você pode determinar quem realizou as tarefas que são relatadas nos logs de auditoria.

> [!NOTE]
> A auditoria relacionada com B2C e os registos de atividades de inícios de sessão não são suportados atualmente.
>

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [inscrever-se para obter uma avaliação gratuita](https://azure.microsoft.com/free/).
* [Licença](https://azure.microsoft.com/pricing/details/active-directory/) do Azure AD Gratuito, Básico, Premium 1 ou Premium 2 para aceder aos registos de auditoria do Azure AD no portal do Azure. 
* Um inquilino do Azure AD.
* Um utilizador que seja **administrador global** ou **administrador de segurança** do inquilino do Azure AD.
* [Licença](https://azure.microsoft.com/pricing/details/active-directory/) do Azure AD Premium 1 ou Premium 2 para aceder aos registos de início de sessão do Azure AD no portal do Azure. 

Dependendo do local para onde pretende encaminhar os dados de registo de auditoria, precisa de um dos seguintes:

* Uma conta de armazenamento do Azure, para a qual tenha permissões *ListKeys*. Recomendamos que utilize uma conta de armazenamento para fins gerais e não uma conta de armazenamento de Blobs. Para obter informações sobre os preços de armazenamento, veja a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Um espaço de nomes dos Hubs de Eventos do Azure, para integrar com soluções de terceiros.
* Um espaço de trabalho do Azure Log Analytics para enviar logs para Azure Monitor logs.

## <a name="cost-considerations"></a>Considerações de custos

Se já tiver uma licença do Azure AD, precisa de uma subscrição do Azure para configurar a conta de armazenamento e o hub de eventos. A subscrição do Azure é fornecida sem custos, mas terá de pagar para utilizar os recursos do Azure, incluindo a conta de armazenamento que utilizar para arquivo e o hub de eventos que utilizar para transmissão em fluxo. A quantidade de dados e, por conseguinte, o custo incorrido, pode variar significativamente consoante o tamanho do inquilino. 

### <a name="storage-size-for-activity-logs"></a>Tamanho de armazenamento para registos de atividades

Cada evento de registo de auditoria consome cerca de 2 KB de armazenamento de dados. Para um inquilino com 100 000 utilizadores, o que implicaria cerca de 1,5 milhões de eventos por dia, precisaria de aproximadamente 3 GB de armazenamento de dados por dia. Uma vez que ocorrem escritas em lotes com a duração aproximada de cinco minutos, é possível prever aproximadamente 9000 operações de escrita por mês. 


A tabela seguinte contém uma estimativa do custo, dependendo do tamanho do inquilino, de uma conta de armazenamento para fins gerais v2 nos E.U.A. Oeste durante, pelo menos, um ano de retenção. Para criar uma estimativa mais exata do volume de dados que prevê para a sua aplicação, utilize a [calculadora de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).


| Categoria do registo | Número de utilizadores | Eventos por dia | Volume de dados por mês (est.) | Custo por mês (est.) | Custo por ano (est.) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Auditoria | 100,000 | 1,5&nbsp;milhões | 90 GB | $1,93 | $23,12 |
| Auditoria | 1,000 | 15,000 | 900 MB | $0,02 | $0,24 |
| Inícios de sessão | 1,000 | 34 800 | 4 GB | $0,13 | $1,56 |
| Inícios de sessão | 100,000 | 15&nbsp;milhões | 1,7 TB | $35,41 | $424,92 |
 









### <a name="event-hub-messages-for-activity-logs"></a>Mensagens do hub de eventos para os registos de atividades

Os eventos são colocados em lote com aproximadamente cinco minutos de intervalo e são enviados como uma única mensagem que contém todos os eventos durante esse período de tempo. Uma mensagem no hub de eventos tem um tamanho máximo de 256 KB e, se o tamanho total de todas as mensagens durante o período de tempo exceder esse volume, são enviadas várias mensagens. 

Por exemplo, para um inquilino grande com mais de 100 000 utilizadores, ocorrem normalmente cerca de 18 eventos por segundo, uma taxa que equivale a 5400 eventos de cinco em cinco minutos. Uma vez que os registos de auditoria têm perto de 2k por evento, equivale a 10,8 MB de dados. Consequentemente, são enviadas 43 mensagens para o hub de eventos nesse intervalo de cinco minutos. 

A tabela seguinte contém os custos estimados por mês para um hub de eventos básico nos E.U.A. Oeste, consoante o volume de dados de eventos. Para calcular uma estimativa exata do volume de dados que prevê para a sua aplicação, utilize a [calculadora de preços do Hub de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Categoria do registo | Número de utilizadores | Eventos por segundo | Eventos por intervalo de cinco minutos | Volume por intervalo | Mensagens por intervalo | Mensagens por mês | Custo por mês (est.) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Auditoria | 100,000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | $10,83 |
| Auditoria | 1,000 | 0.1 | 52 | 104 KB | 1 | 8640 | 10,80 $ |
| Inícios de sessão | 1,000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3\.611.520 | $11,06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Considerações de custo de logs de Azure Monitor



| Categoria do registo       | Número de utilizadores | Eventos por dia | Eventos por mês (30 dias) | Custo por mês em USD (est.) |
| :--                | ---             | ---            | ---                        | --:                          |
| Auditoria e entradas | 100,000         | 16,5 milhões     | 495 milhões                |  $1093                       |
| Auditoria              | 100,000         | 1,5 milhões      | 45,000,000                 |  $246.66                     |
| Inícios de sessão           | 100,000         | 15,000,000     | 450 milhões                |  $847.28                     |










Para examinar os custos relacionados ao gerenciamento dos logs de Azure Monitor, consulte [gerenciar custos controlando o volume de dados e a retenção em logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Esta secção responde às perguntas mais frequentes e inclui discussões sobre problemas conhecidos dos registos do Azure AD no Azure Monitor.

**P: Quais logs estão incluídos?**

**A**: Os logs de atividade de entrada e os logs de auditoria estão disponíveis para roteamento por meio desse recurso, embora os eventos de auditoria relacionados ao B2C não estejam incluídos no momento. Para conhecer os tipos de registos e que registos baseados na funcionalidade são atualmente suportados, veja [Audit log schema](reference-azure-monitor-audit-log-schema.md) (Esquema de registos de auditoria) e [Sign-in log schema](reference-azure-monitor-sign-ins-log-schema.md) (Esquema de registo de inícios de sessão). 

---

**P: Quanto logo após uma ação os logs correspondentes serão exibidos no meu Hub de eventos?**

**A**: Os logs devem aparecer no Hub de eventos dentro de dois a cinco minutos após a ação ser executada. Para obter mais informações sobre os Hubs de Eventos, veja [O que são os Hubs de Eventos do Azure?](../../event-hubs/event-hubs-about.md)

---

**P: Quanto logo após uma ação os logs correspondentes serão exibidos na minha conta de armazenamento?**

**A**: Para contas de armazenamento do Azure, a latência é de 5 a 15 minutos após a ação ser executada.

---

**P: O que acontece se um administrador altera o período de retenção de uma configuração de diagnóstico?**

**A**: A nova política de retenção será aplicada aos logs coletados após a alteração. Os logs coletados antes da alteração da política não serão afetados.

---

**P: Quanto custará armazenar meus dados?**

**A**: Os custos de armazenamento dependem do tamanho dos logs e do período de retenção que você escolher. Para obter uma lista dos custos estimados para os inquilinos, que dependerão do volume de registos gerados, veja a secção [Tamanho do armazenamento para os registos de atividades](#storage-size-for-activity-logs).

---

**P: Quanto custará transmitir meus dados para um hub de eventos?**

**A**: Os custos de streaming dependem do número de mensagens recebidas por minuto. Este artigo mostra como é que os custos são calculados e apresenta uma lista das estimativas de custos, que têm por base o número de mensagens. 

---

**P: Como fazer integrar os logs de atividades do Azure AD ao meu sistema SIEM?**

**A**: Você pode fazer isso de duas maneiras:

- Utilize o Azure Monitor com os Hubs de Eventos para transmitir os registos para o seu sistema SIEM. Primeiro, [transmita os registos para um hub de eventos](tutorial-azure-monitor-stream-logs-to-event-hub.md) e, em seguida [configure a ferramenta SIEM](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) com o hub de eventos configurado. 

- Utilize a [Graph API de Relatórios](concept-reporting-api.md) para aceder aos dados e, em seguida, envie-os para o sistema SIEM através dos seus próprios scripts.

---

**P: Quais ferramentas de SIEM têm suporte atualmente?** 

**A**: Atualmente, Azure Monitor é compatível com a lógica [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar e o [Resumo](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Para obter mais informações sobre como funcionam os conectores, veja [Stream Azure monitoring data to an event hub for consumption by an external tool](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md) (Transmitir em fluxo dados de monitorização do Azure para um hub de eventos, para consumo por uma ferramenta externa).

---

**P: Como fazer integrar os logs de atividades do Azure AD à minha instância do Splunk?**

**A**: Primeiro, [encaminhe os logs de atividade do Azure ad para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md)e siga as etapas para [integrar os logs de atividade com Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**P: Como fazer integrar os logs de atividades do Azure AD à lógica do Resumo?** 

**A**: Primeiro, [encaminhe os logs de atividade do Azure ad para um hub de eventos](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)e siga as etapas para [instalar o aplicativo do Azure AD e exibir os painéis no SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**P: Posso acessar os dados de um hub de eventos sem usar uma ferramenta SIEM externa?** 

**A**: Sim. Pode utilizar a [API dos Hub de Eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) para aceder aos registos da sua aplicação personalizada. 

---


## <a name="next-steps"></a>Passos seguintes

* [Arquivar registos de atividades numa conta de armazenamento](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Encaminhar registos de atividades para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Integrar logs de atividade com Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)