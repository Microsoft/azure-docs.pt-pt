---
title: Utilizar a pesquisa no Application Insights do Azure | Documentos da Microsoft
description: Procurar e filtrar bruta telemetria enviada pela sua aplicação web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: dfbaabd3d27804909334a7a370bcc89115e625c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900269"
---
# <a name="using-search-in-application-insights"></a>Utilizar a pesquisa no Application Insights
A pesquisa é uma funcionalidade do [Application Insights](../../azure-monitor/app/app-insights-overview.md) que utilizar para localizar e explorar os itens de telemetria individuais, como vistas de páginas, exceções ou pedidos da web. E pode ver os rastreios de registos e eventos que codificou tem.

(Para obter consultas mais complexas com os seus dados, utilize [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Onde vê a pesquisa?

### <a name="in-the-azure-portal"></a>No portal do Azure

Pode abrir explicitamente a pesquisa de diagnóstico a partir do painel de descrição geral do Application Insights da sua aplicação:

![Abra pesquisa de diagnóstico](./media/diagnostic-search/001.png)

![Captura de ecrã de gráficos de pesquisa de diagnóstico](./media/diagnostic-search/002.png)

O corpo principal da pesquisa de diagnóstico é uma lista de itens de telemetria - pedidos de servidor, página vistas, eventos personalizados que codificou tem e assim por diante. Na parte superior da lista é um gráfico de resumo que mostra as contagens dos eventos ao longo do tempo.

Clique em Atualizar para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, também há uma janela de pesquisa do Application Insights. É mais útil para apresentar eventos de telemetria gerados pela aplicação que estiver a depurar. Mas ele também pode mostrar os eventos recolhidos a partir da sua aplicação publicada no portal do Azure.

Abra a janela de pesquisa no Visual Studio:

![Abra o Visual Studio pesquisa do Application Insights](./media/diagnostic-search/32.png)

A janela de pesquisa tem recursos semelhantes para o portal web:

![Janela de pesquisa do Visual Studio Application Insights](./media/diagnostic-search/34.png)

A guia de operação de controle está disponível quando abre um pedido ou numa vista de página. Uma ' operação ' é uma sequência de eventos que está associada a uma única exibição de página ou pedido. Por exemplo, chamadas de dependências, exceções, logs de rastreamento e eventos personalizados podem fazer parte de uma única operação. A guia de operação controlar mostra graficamente o tempo e a duração desses eventos em relação a pedido ou vista de página. 

## <a name="inspect-individual-items"></a>Inspecione os itens individuais

Selecione qualquer item de telemetria para ver os campos de chave e itens relacionados.

![Captura de ecrã de um pedido de dependência individuais](./media/diagnostic-search/003.png)

Isso iniciará o modo de exibição de detalhes de transação de ponta a ponta:

![Captura de ecrã da vista de detalhes de transação de ponta a ponta.](./media/diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtrar tipos de eventos
Abra o painel de filtro e escolha os tipos de eventos que pretende ver. (Se, posteriormente, que pretende restaurar os filtros com o qual tiver aberto o painel, clique em Repor.)

![Escolha o filtro e selecione os tipos de telemetria](./media/diagnostic-search/02-filter-req.png)

Os tipos de evento são:

* **Rastreio** - [registos de diagnóstico](../../azure-monitor/app/asp-net-trace-logs.md) incluindo chamadas TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Pedir** -pedidos HTTP recebidos pela aplicação de servidor, incluindo páginas, scripts, imagens, ficheiros de estilo e dados. Estes eventos são utilizados para criar a solicitação e resposta gráficos descrição geral.
* **Vista de página** - [telemetria enviada pelo cliente web](../../azure-monitor/app/javascript.md), utilizado para criar relatórios de vista de página. 
* **Evento personalizado** – se tiver inserido chamadas de trackevent () para [monitorizar a utilização](../../azure-monitor/app/api-custom-events-metrics.md), pode procurá-los aqui.
* **Exceção** - não identificadas [exceções no servidor](../../azure-monitor/app/asp-net-exceptions.md)e os que inicie sessão com trackexception ().
* **Dependência** - [chamadas a partir da sua aplicação de servidor](../../azure-monitor/app/asp-net-dependencies.md) a outros serviços, como REST APIs ou bases de dados e AJAX chama a partir de seu [código de cliente](../../azure-monitor/app/javascript.md).
* **Disponibilidade** -os resultados da [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar valores de propriedade
Pode filtrar eventos nos valores de suas propriedades. As propriedades disponíveis dependem os tipos de evento que selecionou. 

Por exemplo, escolha os pedidos com um código de resposta específica. 

![Expanda uma propriedade e escolha um valor](./media/diagnostic-search/03-response500.png)

Escolher sem valores de uma determinada propriedade tem o mesmo efeito que escolher todos os valores. Ele muda desativar filtragem nessa propriedade.

### <a name="narrow-your-search"></a>A pesquisa
Tenha em atenção que as contagens de à direita dos valores de filtro mostram quantos ocorrências lá estão no conjunto filtrado de atual. 

Neste exemplo, é claro que "Rpt/empregados" pedir os resultados na maioria dos erros de "500":

![Expanda uma propriedade e escolha um valor](./media/diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Encontrar eventos com a mesma propriedade
Localize todos os itens com o mesmo valor de propriedade:

![Uma propriedade com o botão direito](./media/diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra [ **Analytics** ](../../azure-monitor/log-query/get-started-portal.md) da parte superior do painel de pesquisa.
> 

Pode pesquisar termos em qualquer um dos valores de propriedade. Isto é particularmente útil se tiver escrito [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md) com valores de propriedade. 

Pode querer definir uma hora de intervalo, como pesquisas ao longo de um intervalo mais curto, são mais rapidamente. 

![Abra pesquisa de diagnóstico](./media/diagnostic-search/appinsights-311search.png)

Procure palavras completas, não subcadeias de carateres. Utilize aspas incluir carateres especiais.

| string | é *não* encontrados pelo | mas esses encontrá-lo |
| --- | --- | --- |
| HomeController.About |página inicial<br/>controlador<br/>horizontalmente | homecontroller<br/>sobre<br/>"homecontroller.about"|
|Estados Unidos|Uni<br/>TED|United<br/>Estados<br/>e Estados Unidos<br/>"united states"

Aqui estão as expressões de pesquisa que pode utilizar:

| Consulta de exemplo | Efeito |
| --- | --- |
| `apple` |Localizar todos os eventos no intervalo de tempo cujos campos incluem a palavra "apple" |
| `apple AND banana` <br/>`apple banana` |Encontre eventos que contêm as duas palavras. Utilizar o capital "e", não "e". <br/>Formulário curto. |
| `apple OR banana` |Encontre eventos que contêm o word. Utilize "Ou", não "ou". |
| `apple NOT banana` |Encontre eventos que contêm uma palavra, mas não na outra. |

## <a name="sampling"></a>Amostragem
Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver usando o ASP.NET SDK versão 2.0.0-Beta3 ou posterior), o módulo de amostragem adaptável automaticamente reduz o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. No entanto, os eventos relacionados com o mesmo pedido são selecionados ou desmarcados como um grupo, para que pode navegar entre os eventos relacionados. 

[Saiba mais sobre a amostragem](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Criar item de trabalho
Pode criar um bug no GitHub ou do Azure DevOps com os detalhes de qualquer item de telemetria. 

![Clique em Novo Item de trabalho, edite os campos e, em seguida, clique em OK.](./media/diagnostic-search/42.png)

Na primeira vez que fizer isso, é-lhe perguntado para configurar uma ligação à sua organização de DevOps do Azure e o projeto.

![Preencher o URL de seus serviços de DevOps do Azure e o nome do projeto e clique em autorizar](./media/diagnostic-search/41.png)

(Também pode configurar a ligação no painel de itens de trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar telemetria mais para o Application Insights
Além da telemetria de out-of-the-box enviada pelo SDK do Application Insights, pode:

* Capturar rastreios de registos da sua arquitetura de registo favorita na [.NET](../../azure-monitor/app/asp-net-trace-logs.md) ou [Java](../../azure-monitor/app/java-trace-logs.md). Isso significa que pode pesquisar por meio de seus rastreios de registos e correlacioná-los com outros eventos, exceções e vistas de página. 
* [Escrever código](../../azure-monitor/app/api-custom-events-metrics.md) para enviar eventos personalizados e vistas de página e exceções. 

[Saiba como enviar registos e telemetria personalizada para o Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>AS PERGUNTAS E RESPOSTAS
### <a name="limits"></a>A quantidade de dados é retido?

Consulte a [resumo de limites](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como posso ver dados de POSTAGEM nos meus pedidos de servidor?
Nós não iniciar sessão automaticamente os dados de POSTAGEM, mas pode usar [chamadas TrackTrace ou registo](../../azure-monitor/app/asp-net-trace-logs.md). Coloca os dados POST no parâmetro de mensagem. É possível filtrar a mensagem da mesma forma, que pode filtrar nas propriedades, mas o limite de tamanho é maior.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Passos seguintes
* [Escrever consultas complexas no Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Enviar registos e telemetria personalizada para o Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configurar a disponibilidade e testes de capacidade de resposta](../../azure-monitor/app/monitor-web-app-availability.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
