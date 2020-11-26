---
title: Utilização de Pesquisa em Insights de Aplicações Azure ! Microsoft Docs
description: Pesse e filtre a telemetria crua enviada pela sua aplicação web.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: f87a972b417bf6074de1c10b7a54bd2416f88daa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187022"
---
# <a name="using-search-in-application-insights"></a>Utilização de Pesquisa em Insights de Aplicações

A pesquisa é uma funcionalidade de [Application Insights](./app-insights-overview.md) que utiliza para encontrar e explorar itens de telemetria individuais, tais como visualizações de páginas, exceções ou pedidos web. E pode ver vestígios de registo e eventos que codificou.

(Para consultas mais complexas sobre os seus dados, utilize [a Analytics](../log-query/log-analytics-tutorial.md).)

## <a name="where-do-you-see-search"></a>Onde vê o Search?

### <a name="in-the-azure-portal"></a>No portal do Azure

Pode abrir a pesquisa de diagnóstico a partir do separador 'Insights Overview' da sua aplicação (localizado na barra superior) ou sob investigação à esquerda.

![Separador de pesquisa](./media/diagnostic-search/view-custom-events.png)

Vá ao menu suspenso dos tipos de Eventos para ver uma lista de itens de telemetria- pedidos de servidor, visualizações de página, eventos personalizados que codificou, e assim por diante. No topo da lista de resultados, está um gráfico de resumo que mostra as contagens de eventos ao longo do tempo.

Clique fora do menu suspenso ou Refresque-se para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela de pesquisa de insights de aplicação. É mais útil para exibir eventos de telemetria gerados pela aplicação que está a depurar. Mas também pode mostrar os eventos recolhidos a partir da sua aplicação publicada no portal Azure.

Abra a janela de pesquisa no Estúdio Visual:

![Pesquisa de Insights de Aplicação abertos do Estúdio Visual](./media/diagnostic-search/32.png)

A janela 'Pesquisa' tem funcionalidades semelhantes ao portal web:

![Janela de pesquisa visual Studio Application Insights](./media/diagnostic-search/34.png)

O separador 'Operação 'Faixa' está disponível quando abre um pedido ou uma vista de página. Uma 'operação' é uma sequência de eventos que está associada a um único pedido ou visualização de página. Por exemplo, chamadas de dependência, exceções, registos de vestígios e eventos personalizados podem fazer parte de uma única operação. O separador Track Operation mostra graficamente o tempo e a duração destes eventos em relação ao pedido ou visualização da página.

## <a name="inspect-individual-items"></a>Inspecione os itens individuais

Selecione qualquer item de telemetria para ver campos-chave e itens relacionados.

![Screenshot de um pedido de dependência individual](./media/diagnostic-search/telemetry-item.png)

Isto lançará a visão de detalhes de transações de ponta a ponta.

## <a name="filter-event-types"></a>Tipos de eventos de filtro

Abra o menu suspenso dos tipos de Eventos e escolha os tipos de eventos que deseja ver. (Se, mais tarde, pretender restaurar os filtros, clique em Reset.)

Os tipos de eventos são:

* **Vestígios**  -  [Registos de diagnóstico](./asp-net-trace-logs.md) incluindo TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Pedido** - PEDIDO - PEDIDOS HTTP recebidos pela aplicação do seu servidor, incluindo páginas, scripts, imagens, ficheiros de estilo e dados. Estes eventos são usados para criar os gráficos de visão geral de pedido e resposta.
* **Ver página**  -  [Telemetria enviada pelo cliente web,](./javascript.md)usada para criar relatórios de visualização de página.
* **Evento Personalizado** - Se inseriu chamadas para TrackEvent() para monitorizar a [utilização,](./api-custom-events-metrics.md)pode revistá-las aqui.
* **Exceção** - [Desacompressão exceções no servidor](./asp-net-exceptions.md), e aquelas que regista utilizando trackException().
* **Dependência**  -  [Chamadas da sua aplicação de servidor](./asp-net-dependencies.md) para outros serviços, tais como APIs rest ou bases de dados, e chamadas AJAX a partir do [seu código de cliente](./javascript.md).
* **Disponibilidade** - Resultados dos [testes de disponibilidade.](./monitor-web-app-availability.md)

## <a name="filter-on-property-values"></a>Filtrar os valores da propriedade

Pode filtrar eventos sobre os valores das suas propriedades. As propriedades disponíveis dependem dos tipos de eventos que selecionou. Clique no ícone do filtro ![Ícone de filtro](./media/diagnostic-search/filter-icon.png) para começar.

Escolher nenhum valor de um determinado imóvel tem o mesmo efeito que escolher todos os valores. Desliga a filtragem daquela propriedade.

Note que as contagens à direita dos valores do filtro mostram quantas ocorrências existem no conjunto filtrado atual.

## <a name="find-events-with-the-same-property"></a>Encontre eventos com a mesma propriedade

Para encontrar todos os itens com o mesmo valor de propriedade, escreva-o na barra de pesquisa ou clique na caixa de verificação quando estiver a ver propriedades no separador do filtro.

![Clique na caixa de verificação de uma propriedade no separador filtro](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra [**Os Registos (Analytics)**](../log-query/log-analytics-tutorial.md) a partir do topo da lâmina de busca.
>

Pode pesquisar por termos em qualquer um dos valores da propriedade. Isto é útil se tiver escrito [eventos personalizados](./api-custom-events-metrics.md) com valores de propriedade.

É melhor definir um intervalo de tempo, já que as pesquisas ao longo de um alcance mais curto são mais rápidas.

![Pesquisa de diagnóstico aberta](./media/diagnostic-search/search-property.png)

Procure palavras completas, não sublagem. Utilize aspas para incluir caracteres especiais.

| String | *Não* encontrado | Localizado |
| --- | --- | --- |
| HomeController.Sobre |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Estados Unidos da América|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Aqui estão as expressões de pesquisa que pode utilizar:

| Consulta de exemplo | Efeito |
| --- | --- |
| `apple` |Encontre todos os eventos no intervalo de tempo cujos campos incluem a palavra "maçã" |
| `apple AND banana` <br/>`apple banana` |Encontre eventos que contenham ambas as palavras. Use maiúscula "E", não "e". <br/>Forma curta. |
| `apple OR banana` |Encontre eventos que contenham qualquer palavra. Use "OR", não "ou". |
| `apple NOT banana` |Encontre eventos que contenham uma palavra, mas não a outra. |

## <a name="sampling"></a>Amostragem

Se a sua aplicação gerar uma grande quantidade de telemetria (e estiver a utilizar a versão 2.0.0-beta3 da ASP.NET SDK ou posterior), o módulo de amostragem adaptativa reduz automaticamente o volume que é enviado para o portal enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados com o mesmo pedido são selecionados ou desescolados como um grupo, para que possa navegar entre eventos relacionados.

[Saiba mais sobre a amostragem](./sampling.md).

## <a name="create-work-item"></a>Criar artigo de trabalho

Pode criar um bug no GitHub ou no Azure DevOps com os detalhes de qualquer item de telemetria.

Aceda à vista de detalhes de transação de ponta a ponta clicando em qualquer item de telemetria e selecione **Criar artigo de trabalho**.

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/diagnostic-search/work-item.png)

A primeira vez que o fizer, é-lhe pedido que configuure um link para a sua organização e projeto Azure DevOps.

(Também pode configurar o link no separador Itens de Trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para Insights de Aplicação

Além da telemetria fora da caixa enviada pela Application Insights SDK, pode:

* Capture os vestígios de registo da sua estrutura de registo favorita em [.NET](./asp-net-trace-logs.md) ou [Java](./java-trace-logs.md). Isto significa que pode pesquisar através dos seus registos e correlacioná-los com visualizações de página, exceções e outros eventos.
* [Escreva código](./api-custom-events-metrics.md) para enviar eventos personalizados, vistas de página e exceções.

[Saiba como enviar registos e telemetria personalizada para Application Insights](./asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Quantos dados são retidos?

Consulte o [resumo dos Limites.](./pricing.md#limits-summary)

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como posso ver os dados post nos meus pedidos de servidor?

Não registamos automaticamente os dados do POST, mas pode utilizar [chamadas trackTrace ou log](./asp-net-trace-logs.md). Coloque os dados POST no parâmetro da mensagem. Não é possível filtrar a mensagem da mesma forma que pode filtrar as propriedades, mas o limite de tamanho é maior.

## <a name="next-steps"></a><a name="add"></a>Passos seguintes

* [Escreva consultas complexas em Analytics](../log-query/log-analytics-tutorial.md)
* [Enviar registos e telemetria personalizada para Insights de Aplicação](./asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e capacidade de resposta](./monitor-web-app-availability.md)
* [Resolução de problemas](../faq.md)