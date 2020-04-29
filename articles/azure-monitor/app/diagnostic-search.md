---
title: Utilização de Pesquisa em Insights de Aplicação Azure [ Microsoft Docs
description: Pesquisar e filtrar a telemetria crua enviada pela sua aplicação web.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275987"
---
# <a name="using-search-in-application-insights"></a>Utilização de Pesquisa em Insights de Aplicação

A pesquisa é uma característica de Insights de [Aplicação](../../azure-monitor/app/app-insights-overview.md) que utiliza para encontrar e explorar itens de telemetria individuais, tais como vistas de página, exceções ou pedidos web. E pode ver vestígios de registo e eventos que tenha codificado.

(Para consultas mais complexas sobre os seus dados, utilize [o Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Onde vê a Procura?

### <a name="in-the-azure-portal"></a>No portal do Azure

Pode abrir a pesquisa de diagnóstico a partir do separador visão geral da aplicação Insights da sua aplicação (localizada na barra superior) ou sob investigação à esquerda.

![Separador de pesquisa](./media/diagnostic-search/view-custom-events.png)

Vá ao menu suspenso dos tipos de eventos para ver uma lista de itens de telemetria- pedidos de servidores, visualizações de página, eventos personalizados que você tem codificado, e assim por diante. No topo da lista de resultados, está um gráfico resumo que mostra a contagem de eventos ao longo do tempo.

Clique no menu suspenso ou Refresh para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Estúdio Visual, há também uma janela de pesquisa de insights de aplicação. É mais útil para exibir eventos de telemetria gerados pela aplicação que está a depurar. Mas também pode mostrar os eventos recolhidos da sua aplicação publicada no portal Azure.

Abra a janela de pesquisa no Estúdio Visual:

![Visual Studio open Application Insights pesquisa](./media/diagnostic-search/32.png)

A janela Search tem características semelhantes ao portal web:

![Janela de pesquisa de Insights de Aplicação de Estúdio Visual](./media/diagnostic-search/34.png)

O separador Operação de Rastreio está disponível quando abre um pedido ou uma visualização de página. Uma 'operação' é uma sequência de eventos que está associada a um único pedido ou visão de página. Por exemplo, chamadas de dependência, exceções, registos de rastreios e eventos personalizados podem fazer parte de uma única operação. O separador Operação de Rastreio mostra graficamente o tempo e a duração destes eventos em relação ao pedido ou à visualização da página.

## <a name="inspect-individual-items"></a>Inspecionar itens individuais

Selecione qualquer artigo de telemetria para ver campos-chave e itens relacionados.

![Screenshot de um pedido de dependência individual](./media/diagnostic-search/telemetry-item.png)

Isto lançará a visão de detalhes de transações de ponta a ponta.

## <a name="filter-event-types"></a>Tipos de eventos de filtro

Abra o menu suspenso dos tipos do Evento e escolha os tipos de eventos que pretende ver. (Se, mais tarde, pretender restaurar os filtros, clique em Reset.)

Os tipos de eventos são:

* **Rastrear** - [registos](../../azure-monitor/app/asp-net-trace-logs.md) de diagnóstico incluindo TrackTrace, log4Net, NLog e System.Diagnostic.Trace calls.
* **Pedido** - PEDIDOS HTTP recebidos pela sua aplicação de servidor, incluindo páginas, scripts, imagens, ficheiros de estilo e dados. Estes eventos são usados para criar os gráficos de visão geral de pedido e resposta.
* **Página View** - [Telemetria enviada pelo cliente web,](../../azure-monitor/app/javascript.md)usada para criar relatórios de visualização de página.
* **Evento Personalizado** - Se inseriu chamadas para trackEvent() para monitorizar a [utilização,](../../azure-monitor/app/api-custom-events-metrics.md)pode pesquisar aqui.
* **Exceção** - Exceções não [apanhadas no servidor,](../../azure-monitor/app/asp-net-exceptions.md)e aquelas que regista utilizando trackException().
* **Ligações** - de dependência[da sua aplicação do servidor](../../azure-monitor/app/asp-net-dependencies.md) para outros serviços, tais como APIs rest ou bases de dados, e chamadas AJAX do seu código de [cliente](../../azure-monitor/app/javascript.md).
* **Disponibilidade** - Resultados de testes de [disponibilidade.](../../azure-monitor/app/monitor-web-app-availability.md)

## <a name="filter-on-property-values"></a>Filtrar os valores da propriedade

Pode filtrar eventos sobre os valores das suas propriedades. As propriedades disponíveis dependem dos tipos de eventos selecionados. Clique no ícone do filtro ![Ícone de filtro](./media/diagnostic-search/filter-icon.png) para começar.

Escolher nenhum valore de uma determinada propriedade tem o mesmo efeito que escolher todos os valores. Desliga a filtragem daquela propriedade.

Note que as contagens à direita dos valores do filtro mostram quantas ocorrências existem no conjunto filtrado atual.

## <a name="find-events-with-the-same-property"></a>Encontre eventos com a mesma propriedade

Para encontrar todos os itens com o mesmo valor de propriedade, escreva-o na barra de pesquisa ou clique na caixa de verificação quando olhar através das propriedades no separador filtro.

![Clique na caixa de verificação de uma propriedade no separador filtro](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais [**complexas, abra registos (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) a partir do topo da lâmina de pesquisa.
>

Pode procurar termos em qualquer um dos valores da propriedade. Isto é útil se tiver escrito [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md) com valores de propriedade.

É melhor definir um intervalo de tempo, já que as pesquisas num intervalo mais curto são mais rápidas.

![Pesquisa de diagnóstico aberta](./media/diagnostic-search/search-property.png)

Procure palavras completas, não subcordas. Utilize aspas para encerrar caracteres especiais.

| String | *Não* encontrado | Localizado |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Estados Unidos|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Aqui estão as expressões de pesquisa que pode usar:

| Consulta de exemplo | Efeito |
| --- | --- |
| `apple` |Encontre todos os eventos na faixa de tempo cujos campos incluem a palavra "maçã" |
| `apple AND banana` <br/>`apple banana` |Encontre eventos que contenham ambas as palavras. Use maiúscula "E", não "e". <br/>Forma curta. |
| `apple OR banana` |Encontre eventos que contenham qualquer palavra. Use "OR", não "ou". |
| `apple NOT banana` |Encontre eventos que contenham uma palavra, mas não a outra. |

## <a name="sampling"></a>Amostragem

Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver a utilizar a versão SDK de ASP.NET 2.0.0-beta3 ou posterior), o módulo de amostragem adaptativo reduz automaticamente o volume que é enviado para o portal enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados com o mesmo pedido são selecionados ou desselecionados como um grupo, para que possa navegar entre eventos relacionados.

[Saiba mais sobre a amostragem](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Criar artigo de trabalho

Pode criar um bug em GitHub ou Azure DevOps com os detalhes de qualquer item de telemetria.

Vá à visualização de detalhes de transação de ponta a ponta clicando em qualquer item de telemetria e, em seguida, selecione **Criar produto**de trabalho .

![Clique em New Work Item, edite os campos e clique em OK.](./media/diagnostic-search/work-item.png)

A primeira vez que o faz, é-lhe pedido que configure um link para a sua organização e projeto Azure DevOps.

(Também pode configurar o link no separador Itens de Trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para Application Insights

Além da telemetria fora da caixa enviada pela Application Insights SDK, pode:

* Capture vestígios de registo da sua estrutura de registo favorita em [.NET](../../azure-monitor/app/asp-net-trace-logs.md) ou [Java](../../azure-monitor/app/java-trace-logs.md). Isto significa que pode pesquisar os seus vestígios de registo e correlacionar-os com visualizações de página, exceções e outros eventos.
* [Escreva código](../../azure-monitor/app/api-custom-events-metrics.md) para enviar eventos personalizados, visualizações de páginas e exceções.

[Saiba como enviar registos e telemetria personalizada para Insights](../../azure-monitor/app/asp-net-trace-logs.md)de Aplicação .

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Quantos dados são retidos?

Consulte o resumo dos [Limites.](../../azure-monitor/app/pricing.md#limits-summary)

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como posso ver os dados do POST nos meus pedidos de servidor?

Não registamos automaticamente os dados do POST, mas pode utilizar o [TrackTrace ou as chamadas de registo](../../azure-monitor/app/asp-net-trace-logs.md). Coloque os dados do POST no parâmetro da mensagem. Não é possível filtrar a mensagem da mesma forma que pode filtrar as propriedades, mas o limite de tamanho é mais longo.

## <a name="next-steps"></a><a name="add"></a>Passos seguintes

* [Escreva consultas complexas em Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Envie registos e telemetria personalizada para Insights de Aplicação](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e resposta](../../azure-monitor/app/monitor-web-app-availability.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
