---
title: Monitorização e a processar eventos de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Saiba como pode utilizar o dashboard de eventos do Centro de segurança para ver eventos de segurança de VMs do Azure e computadores não Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 2b9426043619887d99003677fd6e8488cb56cce2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241073"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorização e a processar eventos de segurança no Centro de segurança do Azure
O dashboard de eventos fornece uma descrição geral do número de eventos de segurança recolhidos ao longo do tempo e uma lista de eventos notáveis que podem exigir a sua atenção.  

> [!NOTE]
> Para utilizar esta funcionalidade, sua área de trabalho tem de executar a versão 2 do Log Analytics e estar no escalão Standard do Centro de segurança. Consulte o Centro de segurança [página de preços](security-center-pricing.md) para obter mais informações sobre o escalão Standard.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="what-is-a-security-event"></a>O que é um evento de segurança?
Usos do Centro de segurança do Microsoft Monitoring Agent para recolher vários segurança e configurações relacionados com eventos das suas máquinas e armazena esses eventos em suas áreas de trabalho. Exemplos destes dados são: registos de sistema (registos de eventos Windows), em execução operativo processa e eventos de soluções de segurança integrado com o Centro de segurança. O Agente de Monitorização da Microsoft também copia os ficheiros de informação de falha de sistema para as suas áreas de trabalho.

## <a name="events-processed-dashboard"></a>Dashboard de eventos processados
Aceder a **eventos** dashboard a partir do menu principal do Centro de segurança ou o Centro de segurança **descrição geral** painel.  

![Dashboard de eventos processados][1]

O **eventos** mosaico sob **Centro de segurança** mostra o número de eventos a ser encaminhados para o Centro de segurança de VMs do Azure e computadores não Azure.

O **dashboard de eventos** fornece uma descrição geral do número de horas extraordinárias de eventos processados e uma lista de eventos.

 ![Dashboard][2]

 A metade superior do dashboard tendências todos os eventos processados na última semana. Na parte inferior do dashboard apresenta uma lista de eventos notáveis e todos os eventos por tipo:

 - **Eventos notáveis** incluem consultas de eventos que fornece o Centro de segurança e consultas de eventos que criar e adicionar. O dashboard também fornece uma vista rápida de contagem de cada evento notável.
 - **Todos os eventos por tipo** mostra os tipos de eventos que estão a ser recebidos e uma contagem para cada tipo. Exemplos de tipo de evento são SecurityEvent, CommonSecurityLog, WindowsFirewall e W3CIISLog.

> [!NOTE]
> Incluem eventos notáveis [avaliação da linha de base web](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). A Avaliação da Linha de Base Web tem por objetivo localizar definições de servidor Web potencialmente vulneráveis.

## <a name="view-processed-event-details"></a>Ver detalhes de eventos processados
1. Sob o **Centro de segurança** menu principal, selecione **eventos**.
2. O **dashboard de eventos** pode abrir o Seletor de área de trabalho. Se tiver apenas uma área de trabalho, este Seletor de área de trabalho não aparece. Se tiver mais do que uma área de trabalho, tem de selecionar uma área de trabalho para ver os detalhes de eventos processados. Selecione uma área de trabalho na lista se tiver mais do que uma área de trabalho.

  ![Lista de área de trabalho][3]

3. O **dashboard de eventos** aberta que mostra detalhes do evento para a área de trabalho selecionada. Pode ver os eventos notáveis e todos os eventos por tipo.  Neste exemplo, selecionamos **eventos notáveis**.

  ![Evento notável][4]

4. Pode consultar para obter mais dados na área de trabalho ao selecionar um tipo de evento. Neste exemplo, selecionamos **SecurityEvent**.

  ![Selecionar um tipo de evento][5]

5. **Pesquisa de registos** abre-se com detalhes adicionais sobre o tipo de evento.

  ![Pesquisas de registos][6]

## <a name="add-a-notable-event"></a>Adicionar um evento notável
Centro de segurança fornece eventos notáveis de out-of-the-box. Pode adicionar eventos notáveis, com base na sua própria consulta utilizando o [linguagem de consulta de Kusto](../log-analytics/log-analytics-search-reference.md). Vamos voltar à **dashboard de eventos** para adicionar um evento notável.

1. Selecione **adicionar evento notável**.

  ![Adicionar um evento notável][7]

2. **Adicionar evento notável personalizado** abre.  Sob **nome a apresentar**, introduza um nome para seu evento notável. Sob **consulta de pesquisa**, introduza a consulta para o evento.

  ![Introduza a sua consulta][8]

4. Selecione **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Atualizar a sua área de trabalho para processamento de eventos
A área de trabalho tem de estar a executar a versão 2 do Log Analytics e estar no escalão Standard do Centro de segurança a utilizar o processamento de eventos no Centro de segurança. O **dashboard de eventos** Seletor de área de trabalho identifica as áreas de trabalho que não cumpram estes requisitos.

![Área de trabalho não cumpre os requisitos][9]

Se a linha de área de trabalho:

- Contém **precisa de ATUALIZAÇÃO** -tem de atualizar a sua área de trabalho para o Log Analytics, versão 2
- Contém **ATUALIZAR plano** – tem de atualizar a sua área de trabalho para o escalão Standard do Centro de segurança
- Está em branco - sua área de trabalho cumpre os requisitos e selecionar uma área de trabalho leva-o ao dashboard

> [!NOTE]
> Sob **dashboard de eventos**, o **eventos** coluna indica a quantidade de eventos em cada área de trabalho.  Esta coluna está em branco para algumas áreas de trabalho porque o escalão gratuito do Centro de segurança é aplicado a essa área de trabalho. Sob o escalão gratuito, o Centro de segurança irá recolher eventos, mas os eventos não são guardados nos registos do Azure Monitor e não estão disponíveis no dashboard.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Atualizar a área de trabalho para o Log Analytics, versão 2
1. Selecione uma área de trabalho que **requer ATUALIZAÇÃO**.
2. **Pesquisar atualização** abre. Selecione **atualizar agora**.

  ![Atualizar agora][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Atualizar para o escalão Standard do Centro de segurança
1. Selecione uma área de trabalho com **ATUALIZAR plano**.
2. **Dashboard de eventos** abre. Selecione **dashboard de eventos tente**.

  ![Experimentar dashboard][11]

3. Sob **inclusão de segurança avançada**, selecione a área de trabalho que está a atualizar.
4. Sob **preços**, selecione **padrão**.
5. Selecione **Guardar**.

  ![Atualizar para o escalão Standard][12]

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu como utilizar o dashboard de eventos do Centro de segurança. Para saber mais sobre como funciona o dashboard e escrever suas próprias consultas de eventos, consulte:

- [O que é o Azure Monitor registos?](../log-analytics/log-analytics-overview.md) – Descrição geral nos registos do Azure Monitor
- [Compreender a pesquisa em Kusto](../log-analytics/log-analytics-log-search-new.md) - descreve como as pesquisas de registos são utilizadas nos registos do Azure Monitor e fornece os conceitos que devem ser compreendidos antes de criar uma pesquisa de registos
- [Referência de pesquisa de Kusto](../log-analytics/log-analytics-search-reference.md) – Saiba como escrever suas próprias consultas de eventos usando a linguagem de consulta no registo

Para saber mais sobre o Centro de segurança, veja:

- [Descrição geral do Centro de segurança](security-center-intro.md) – capacidades do Centro de segurança descreve de principais

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
