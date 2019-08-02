---
title: Monitorando e processando eventos de segurança na central de segurança do Azure | Microsoft Docs
description: Saiba como você pode usar o painel de eventos da central de segurança para ver eventos de segurança de suas VMs do Azure e computadores não Azure.
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
ms.openlocfilehash: 389aaee621251890cd3f75744a94b9c9b29c5695
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662385"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitorando e processando eventos de segurança na central de segurança do Azure
O painel eventos fornece uma visão geral do número de eventos de segurança coletados ao longo do tempo e uma lista de eventos notáveis que podem exigir atenção.  

> [!NOTE]
> O painel de eventos de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_events).

## <a name="what-is-a-security-event"></a>O que é um evento de segurança?
A central de segurança usa a Microsoft Monitoring Agent para coletar várias configurações e eventos relacionados à segurança de seus computadores e armazena esses eventos em seus espaços de trabalho. Exemplos desses dados são: logs do sistema operacional (logs de eventos do Windows), processos em execução e eventos de soluções de segurança integradas à central de segurança. O Agente de Monitorização da Microsoft também copia os ficheiros de informação de falha de sistema para as suas áreas de trabalho.

## <a name="requirements"></a>Requisitos
Para usar esse recurso, seu espaço de trabalho deve estar executando Log Analytics versão 2 e estar na camada Standard da central de segurança. Consulte a página de [preços](security-center-pricing.md) da central de segurança para obter mais informações sobre a camada Standard.

## <a name="events-processed-dashboard"></a>Painel de eventos processados
Você acessa o painel **eventos** no menu principal da central de segurança ou na folha **visão geral** da central de segurança.  

![Painel de eventos processados][1]

O bloco **eventos** na **central de segurança** exibe o número de eventos que fluem para a central de segurança de suas VMs do Azure e de computadores não Azure.

O **painel eventos** fornece uma visão geral do número de eventos processados por hora e uma lista de eventos.

 ![Dashboard][2]

 A metade superior do painel tendências de todos os eventos processados na última semana. A metade inferior do painel lista eventos notáveis e todos os eventos por tipo:

 - **Eventos notáveis** incluem consultas de eventos que a central de segurança fornece e consultas de eventos que você cria e adiciona. O painel também fornece uma exibição rápida da contagem de cada evento notável.
 - **Todos os eventos por tipo** mostra os tipos de evento que estão sendo recebidos e uma contagem para cada tipo. Os exemplos de tipo de evento são SecurityEvent, CommonSecurityLog, WindowsFirewall e W3CIISLog.

> [!NOTE]
> Os eventos notáveis incluem a [avaliação de linha de base da Web](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). A Avaliação da Linha de Base Web tem por objetivo localizar definições de servidor Web potencialmente vulneráveis.

## <a name="view-processed-event-details"></a>Exibir detalhes do evento processado
1. No menu principal da **central de segurança** , selecione **eventos**.
2. O seletor de espaço de trabalho **painel de eventos** pode ser aberto. Se você tiver apenas um espaço de trabalho, esse seletor de espaço de trabalho não será exibido. Se você tiver mais de um espaço de trabalho, precisará selecionar um espaço de trabalho para exibir seus detalhes de evento processados. Selecione um espaço de trabalho na lista se você tiver mais de um espaço de trabalho.

   ![Lista de espaços de trabalho][3]

3. O **painel eventos** é aberto mostrando os detalhes do evento para o espaço de trabalho selecionado. Você pode exibir os eventos notáveis e todos os eventos por tipo.  Neste exemplo, selecionamos **eventos notáveis**.

   ![Evento notável][4]

4. Você pode consultar mais dados no espaço de trabalho selecionando um tipo de evento. Neste exemplo, selecionamos **SecurityEvent**.

   ![Selecionando um tipo de evento][5]

5. A **pesquisa de logs** é aberta com detalhes adicionais sobre o tipo de evento.

   ![Pesquisas de registos][6]

## <a name="add-a-notable-event"></a>Adicionar um evento notável
A central de segurança fornece eventos notáveis prontos para uso. Você pode adicionar eventos notáveis com base em sua própria consulta usando a [linguagem de consulta Kusto](../log-analytics/log-analytics-search-reference.md). Retornaremos ao painel de **eventos** para adicionar um evento notável.

1. Selecione **Adicionar evento notável**.

   ![Adicionar um evento notável][7]

2. Adicione aberturas de **eventos notáveis personalizados** .  Em **nome de exibição**, insira um nome para seu evento notável. Em **consulta de pesquisa**, insira sua consulta para o evento.

   ![Insira sua consulta][8]

4. Selecione **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Atualizar seu espaço de trabalho para processamento de eventos
Seu espaço de trabalho deve estar executando Log Analytics versão 2 e estar na camada Standard da central de segurança para usar o processamento de eventos na central de segurança. O seletor de espaço de trabalho **painel de eventos** identifica espaços de trabalho que não atendem a esses requisitos.

![O espaço de trabalho não atende aos requisitos][9]

Se a linha do espaço de trabalho:

- Contains **requer atualização** -você precisa atualizar seu espaço de trabalho para log Analytics versão 2
- Contém **plano de atualização** – você precisa atualizar seu espaço de trabalho para a camada Standard da central de segurança
- Está em branco-seu espaço de trabalho atende aos requisitos e selecionar um espaço de trabalho leva você até o painel

> [!NOTE]
> No **painel eventos**, a coluna **eventos** indica a quantidade de eventos em cada espaço de trabalho.  Essa coluna está em branco para alguns espaços de trabalho porque a camada gratuita da central de segurança é aplicada a esse espaço de trabalho. Na camada gratuita, a central de segurança coletará eventos, mas os eventos não serão salvos nos logs de Azure Monitor e não estarão disponíveis no painel.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Atualizar espaço de trabalho para Log Analytics versão 2
1. Selecione um espaço de trabalho que **exija atualização**.
2. A **atualização da pesquisa** é aberta. Selecione **Atualizar agora**.

   ![Atualizar agora][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Atualizar para a camada Standard da central de segurança
1. Selecione um espaço de trabalho com o **plano de atualização**.
2. O **painel de eventos** é aberto. Selecione o **painel testar eventos**.

   ![Experimente o painel][11]

3. Em **integração à segurança avançada**, selecione o espaço de trabalho que você está atualizando.
4. Em **preços**, selecione **padrão**.
5. Selecione **Guardar**.

   ![Atualizar para a camada Standard][12]

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, você aprendeu a usar o painel de eventos da central de segurança. Para saber mais sobre como o painel funciona e escrever suas próprias consultas de evento, consulte:

- [O que é Azure Monitor logs?](../log-analytics/log-analytics-overview.md) – Visão geral dos logs de Azure Monitor
- [Noções básicas sobre pesquisas de log em Kusto](../log-analytics/log-analytics-log-search-new.md) -descreve como as pesquisas de log são usadas em logs de Azure monitor e fornece conceitos que devem ser compreendidos antes de criar uma pesquisa de log
- [Referência de pesquisa do Kusto](../log-analytics/log-analytics-search-reference.md) – saiba como escrever suas próprias consultas de evento usando a linguagem de consulta no log

Para saber mais sobre a central de segurança, consulte:

- [Visão geral da central de segurança](security-center-intro.md) – descreve os principais recursos da central de segurança

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
