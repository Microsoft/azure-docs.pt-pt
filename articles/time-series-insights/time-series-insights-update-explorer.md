---
title: Visualizar dados no Explorador de pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve os recursos e as opções disponíveis na aplicação web de explorer da pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399867"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizar dados no Explorador de pré-visualização

Este documento descreve a interface do usuário e a funcionalidades de experiência de utilizador e a interface da pré-visualização do Azure Time Series Insights [aplicação web de demonstração](https://insights.timeseries.azure.com/preview/demo). Especificamente, ele aborda o layout do exemplo hospedado, opções de personalização da interface e navegação por meio da demonstração fornecida.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar com o Explorador de pré-visualização do Azure Time Series Insights, tem de:

* Tem um ambiente de Time Series Insights que configurar. Para saber mais sobre o aprovisionamento de uma instância, experimente o [pré-visualização do Azure Time Series Insights](./time-series-insights-update-create-environment.md) tutorial.
* [Fornecer acesso a dados](./time-series-insights-data-access.md) no ambiente do Time Series Insights que criou para a conta. Pode fornecer acesso a outros utilizadores, bem como para si próprio.
* Adicione uma origem de evento para o ambiente do Time Series Insights para enviar dados para o ambiente:
  * Saiba mais [como ligar a um hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Saiba mais [como ligar a um hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Saiba mais sobre o Explorador de pré-visualização

O Explorador de pré-visualização do Azure Time Series Insights é composta pelos seguintes elementos:

[![A vista de Explorador](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Painel de ambiente</a>: Apresenta os seus ambientes do Azure Time Series Insights.
- <a href="#navigation-menu">Menu de navegação</a>: Utilizá-la para alternar entre o **Analyze** e **modelo** páginas.
- <a href="#hierarchy-tree">Árvore de hierarquia</a>: Utilize-a para selecionar os elementos de modelo e dados específicos para ser colocada em gráficos.
- <a href="#preview-well">Série de tempo bem</a>: Apresenta os elementos de dados atualmente selecionados no formato de tabela com codificação de cores.
- <a href="#preview-chart">Painel de gráfico</a>: Apresenta o gráfico de trabalho atual.
- <a href="#time-editor-panel">Linha cronológica</a>: Usá-lo para modificar o intervalo de tempo de trabalho.
- <a href="#navigation-panel">Barra de aplicativo</a>: Contém as opções de gestão de utilizador, como inquilino atual. Pode usá-lo para alterar as definições de idioma e o tema.

## <a name="environment-drop-down-list"></a>Lista pendente de ambiente

A ambiente na lista pendente apresenta todos os ambientes do Time Series Insights que tem acesso. A lista inclui os ambientes de pay as you go, como a pré-visualização do Time Series Insights. A lista inclui também a ambientes de S1/S2, que estão disponíveis em geral.

1. Selecione a seta de lista pendente junto ao seu ambiente apresentado.

   [![O painel de controle](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Em seguida, selecione o ambiente desejado.

## <a name="navigation-menu"></a>Menu de Navegação

  [![O menu de navegação](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Utilize o menu de navegação para selecionar entre duas exibições:

* **Analisar**: Usá-lo para o gráfico e realizar análises avançadas com seus dados de séries de tempo unmodeled ou modelados.
* **modelo**: Utilizá-lo para enviar tipos, hierarquias e instâncias de pré-visualização do Time Series Insights novo para o seu modelo de Time Series Insights.

## <a name="hierarchy-tree"></a>Árvore de hierarquia

A árvore hierarquia apresenta os elementos de dados selecionados que incluem sensores nos seus dispositivos, modelos e dispositivos específicos.

### <a name="model-search-panel"></a>Painel de pesquisa de modelo

Pode utilizar o painel de pesquisa de modelo para procurar e navegue até a hierarquia de modelo de série de tempo para localizar as instâncias de série de tempo específico que pretende apresentar no gráfico facilmente. Depois de selecionar as instâncias, estes forem adicionados ao gráfico atual e os dados também.

  [![O painel de pesquisa de modelo](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Criação do modelo

O suporte de pré-visualização do Azure Time Series Insights completa cria, ler, atualizar e eliminar operações de (CRUD) em seu modelo de série de tempo.

* **Tipo de modelo de série de tempo**: Pode usar tipos de Time Series Insights para definir as variáveis ou fórmulas para fazer cálculos. Eles são associados a uma determinada instância de Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia do modelo de série de tempo**: Hierarquias são organizações sistemática dos seus dados. Hierarquias retratar as relações entre diferentes entidades nos seus dados do Time Series Insights.
* **Instância do modelo de série de tempo**: As instâncias são a série de tempo propriamente ditas. Na maioria dos casos, eles são os **DeviceID** ou **AssetID**, que é o identificador exclusivo do recurso no ambiente.

Para saber mais sobre o modelo de série de tempo, veja [modelos de série de vezes](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Pré-visualização bem

O bem apresenta os campos de instância e outros metadados associados com instâncias selecionadas do Time Series Insights. Ao selecionar as caixas de verificação no lado direito, pode ocultar ou exibir instâncias específicas de um gráfico atual. Também pode remover elementos de dados específicos dos seus dados atuais bem ao selecionar o vermelho **eliminar** (caixote do lixo) controle no lado esquerdo do elemento.

  [![A pré-visualização bem](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Para reconfigurar o layout de sua **Analyze** página do gráfico, selecione o ícone de reticências no canto superior direito:

  [![Opções de layout de telemetria](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Se vir a mensagem seguinte, a instância não tem quaisquer dados durante o intervalo de tempo selecionado. Para resolver o problema, aumente o intervalo de tempo ou confirmar que a instância está a enviar dados.
>
> ![Nenhuma notificação de dados](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Gráfico de pré-visualização

Com o gráfico, pode exibir as instâncias do Time Series Insights como linhas. Pode fechar o painel de ambiente, o modelo de dados e o painel de controlo de span de tempo ao selecionar os controles da web para aumentar o gráfico.

  [![Descrição geral do gráfico de pré-visualização](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Selecionar intervalo de datas**: Controlos de quais elementos de dados estão disponíveis para visualização.

- **Ferramenta de controlo de deslize de intervalo de datas interna**: Utilize os dois controles de ponto final ao arrastá-los durante o intervalo de tempo que pretende.

- **Tempo o controlo de recolhimento span**: Fecha e expande o editor de span painel de tempo.

- **Controlo de formato do eixo y**: Percorre as opções de vista do eixo y disponíveis:

    * `Default`: Cada linha tem um eixo y individual.
    * `Stacked`: Utilize-o em pilhas, várias linhas no mesmo eixo y, com a alteração de dados de eixo y com base na linha selecionada.
    * `Shared`: Todos os dados do eixo y apresentados em conjunto.

- **Elemento de dados atual**: O elemento de dados atualmente selecionados e os detalhes associados.

Explorar ainda mais num setor de dados específicos, left-click um ponto de dados no gráfico atual e, em seguida, arraste a área selecionada para o ponto final da sua preferência. A área cinza selecionada com o botão direito e selecione **Zoom**, conforme mostrado na imagem seguinte:

  [![Zoom de gráfico de pré-visualização](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Após executar o **Zoom** ação, verá os dados que selecionou definido. Selecione o controlo de formato do eixo y para percorrer as três representações de eixo y dos seus dados do Time Series Insights.

  [![Eixo y do gráfico de pré-visualização](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Aqui, pode ver um exemplo de eixos de Y partilhados:

  [![Pré-visualização partilhado eixos Y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Painel do editor de tempo

Quando trabalha com pré-visualização do Time Series Insights, primeiro selecione um intervalo de tempo. O intervalo de tempo selecionado controla o conjunto de dados que está disponível para a manipulação com os widgets de pré-visualização do Time Series Insights. Os controles da web seguintes estão disponíveis em pré-visualização do Time Series Insights para selecionar o seu intervalo de tempo de trabalho:

  [![Painel de seleção de tempo](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Ferramenta de controlo de deslize de intervalo de datas interna**: Utilize os dois controles de ponto final ao arrastá-los durante o intervalo de tempo que pretende. Este intervalo de datas interna é restrita pelo controlo de deslize de intervalo de datas externa.

1. **Aumentar e diminuir os botões de intervalo de datas**: Aumentar ou diminuir o intervalo de tempo ao selecionar o botão para o intervalo que pretende.

1. **Tempo o controlo de recolhimento span**: Esse controle web permite-lhe ocultar todos os controles, exceto para a ferramenta de controlo de deslize de intervalo de datas interna.

1. **Controlo de deslize de intervalo de datas externa**: Utilize os controlos de ponto final para selecionar o intervalo de datas externa, que estará disponível para o seu controle de intervalo de datas interna.

1. **Vezes rápidas intervalo de datas na lista pendente**: Utilizá-la para alternar rapidamente entre seleções de span de tempo predefinido, como a última **30 minutos**, o **últimas 12 horas**, ou uma **intervalo personalizado**. Alterar este valor também é alterado os intervalos de intervalo disponível discutidos na ferramenta de controlo de deslize do tamanho do intervalo.

1. **Ferramenta de controlo de deslize de intervalo de tamanho**: Usá-lo para ampliar e intervalos de reduzir durante o mesmo intervalo de tempo. Esta ação fornece um controle mais preciso de movimento entre os setores de grandes dimensões de tempo. Apresenta as tendências uniformes para baixo para setores tão pequenas como um milissegundo. Pode usá-lo para ver as reduções de alta resolução, granulares dos seus dados. Predefinição do controlo de deslize ponto de partida é definida como a exibição ideal dos dados da sua seleção, o que equilibra a resolução, velocidade de consulta e granularidade.

1. **Controlo de web de-e-do intervalo de datas**: Com esse controle da web, pode facilmente selecionar os intervalos de data e hora em que pretende. Também pode utilizar o controlo de alternar entre diferentes fusos horários. Depois de efetuar as alterações a aplicar à sua área de trabalho atual, selecione **guardar**.

   [![Para e partir do painel de seleção](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Painel de navegação

O painel de navegação de pré-visualização do Time Series Insights é apresentada na parte superior da sua aplicação do Time Series Insights. Ele fornece as seguintes funcionalidades.

### <a name="current-session-share-link-control"></a>Controlo de sessão atual de ligação de partilha

  [![Ícone partilhar](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selecione a nova **partilhar** ícone para partilhar uma ligação de URL com a sua equipa.

  [![Partilhar o seu URL de instância](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Secção de inquilino

  [![Seleção de inquilino](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Apresenta as informações de conta de início de sessão atuais do Time Series Insights.
* Utilizá-la para alternar entre os temas do Time Series Insights disponíveis.
* Utilize-o para ver a pré-visualização [aplicação web de demonstração](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Seleção de tema

Para selecionar um novo tema, selecione o ícone do perfil localizado no canto superior direito. Em seguida, selecione **Alterar tema**.

  [![Seleção de tema](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Seleção de idioma também está disponível ao selecionar o ícone do perfil.

Pré-visualização de informações de série de tempo do Azure suporta dois temas:

* **Tema claro**: O tema predefinido mostrado em todo este documento.
* **Tema escuro**: Processa o explorer conforme mostrado aqui:

  [![Tema escuro selecionado](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controles de ambiente de S1/S2

### <a name="preview-terms-panel"></a>Painel de termos de pré-visualização

Esta secção aplica-se apenas aos ambientes de S1/S2 existentes que tentam utilizar o Explorador na interface do Usuário atualizada. Pode querer utilizar o produto em disponibilidade geral e pré-visualização em combinação. Adicionámos algumas funcionalidades da interface do Usuário existente para o explorer atualizado, mas pode obter a experiência de interface do Usuário completa para o ambiente de S1/S2 no Explorador do Time Series Insights existente. 

Em vez da hierarquia, verá o painel de termos do Time Series Insights, onde define as consultas no seu ambiente. Usá-lo para seus dados com base num predicado de filtro.

  [![Consultar em que o painel](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

O painel de editor de termos de pré-visualização do Time Series Insights precisa dos seguintes parâmetros:

**Onde**: Utilizar where cláusula para filtrar rapidamente os seus eventos ao utilizar o conjunto de operandos listados na tabela seguinte. Se realizar uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base em que a pesquisa. Tipos de operando suportados incluem:

| Operação | Tipos suportados   | Notas |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Período de tempo de Double, DateTime, | |
| `=`, `!=`, `<>` | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo |
| `IN` | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo | Todos os operandos devem ser do mesmo tipo ou ser constante NULL. |
| `HAS` | String | Literais de cadeia de caracteres constante só são permitidas no lado direito. Cadeia de caracteres vazia e NULL não são permitidos. |

Para saber mais sobre os tipos de dados e as operações de consulta suportados, consulte [expressão de série de tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemplos de onde as cláusulas

  [![Em que os exemplos de cláusula](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Medida**: Uma lista pendente que apresenta todas as colunas numéricas (**duplicatas**) pode utilizar como elementos para o gráfico atual.

**Dividir por**: Esta lista pendente apresenta todas as disponíveis categóricas colunas (cadeias de caracteres) em seu modelo que pode agrupar os seus dados pelo. Pode adicionar até cinco termos para ver no mesmo eixo x. Introduza os parâmetros que pretende e, em seguida, selecione **adicionar** para adicionar um novo termo.

  [![Vista filtrada e consultada um](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Pode mostrar e ocultar elementos no painel de gráfico ao selecionar o ícone visível, conforme mostrado na imagem seguinte. Para remover completamente a consultas, selecione o vermelho **X**.

  [![Vista filtrada e consultada dois](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [armazenamento e de entrada](./time-series-insights-update-storage-ingress.md) na pré-visualização do Azure Time Series Insights.
- Continue a ler o documento de pré-visualização do Time Series Insights [modelação de dados](./time-series-insights-update-tsm.md).
- Saiba mais [como diagnosticar e resolver problemas](./time-series-insights-update-how-to-troubleshoot.md) sua instância do Time Series Insights.
