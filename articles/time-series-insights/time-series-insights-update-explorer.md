---
title: Visualizar dados no Explorador de pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve os recursos e as opções disponíveis na aplicação web de explorer da pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: ed8a829bd73a53dc94bf8b08648b3d6684f718f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109768"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizar dados no Explorador de pré-visualização

Este artigo descreve as funcionalidades e opções disponíveis na pré-visualização do Azure Time Series Insights [aplicação web de explorer](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Pré-requisitos

Antes de utilizar o Explorador de pré-visualização do Azure Time Series Insights, faça o seguinte:

* Tem um ambiente de Time Series Insights que configurar. Para obter mais informações, consulte [Tutorial: Pré-visualização do Azure Time Series Insights](./time-series-insights-update-create-environment.md).
* Fornece acesso a dados para o ambiente do Time Series Insights que criou para a conta. Pode fornecer acesso a outros utilizadores, bem como para si próprio.
* Adicione uma origem de evento para o ambiente do Time Series Insights para enviar dados para o ambiente.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Saiba mais sobre o Explorador de pré-visualização do Azure Time Series Insights

  ![um Explorador][1]

O Explorador de pré-visualização do Azure Time Series Insights é composta pelos seguintes elementos:

* **Barra de navegação**: Permite-lhe alternar entre as páginas de análise e o modelo.
* **Árvore de hierarquia**: Permite-lhe selecionar os elementos de dados específicos para ser colocada em gráficos.
* **Série de tempo bem**: Apresenta os elementos de dados atualmente selecionados.
* **Painel de gráfico**: Apresenta o gráfico de trabalho atual.
* **Linha cronológica**: Permite-lhe modificar o intervalo de tempo de trabalho.
* **Barra de aplicativo**: Contém as opções de gestão de utilizador, como inquilino atual e permite que altere as definições de idioma e o tema.

## <a name="time-series-insights-preview-environment-panel"></a>Painel de ambiente de pré-visualização de informações de série de tempo

O painel de ambiente apresenta todos os ambientes do Time Series Insights que tem acesso. A lista inclui os ambientes de pay as you go pré-visualização e S1/S2 ambientes (GA). Basta clicar o ambiente do Time Series Insights que pretende utilizar.

  ![Explorador de dois][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Menu de navegação de pré-visualização de informações de série de tempo

  ![três Explorer][3]

Com o menu de navegação, pode alternar entre as aplicações do Time Series Insights:

* **Analisar**: Permite-lhe o gráfico e realizar análises avançadas com seus dados de séries de tempo unmodeled ou modelados.

* **modelo**: Permite-lhe enviar tipos, hierarquias e instâncias de pré-visualização do Time Series Insights novo para o seu modelo de Time Series Insights.

## <a name="time-series-insights-preview-model-authoring"></a>A criação do modelo de pré-visualização de informações de série de tempo

Com esta aplicação, pode efetuar operações de criação, leitura, atualização e exclusão (CRUD) em seu modelo de série de tempo.  

* **Tipo de modelo de série de tempo**: Tipos de informações de série de tempo permitem definir variáveis ou fórmulas para fazer cálculos. Eles estão associados uma determinada instância de Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia do modelo de série de tempo**: Hierarquias são organizações sistemática dos seus dados. Hierarquias retratar as relações entre diferentes entidades nos seus dados do Time Series Insights.
* **Instância do modelo de série de tempo**: As instâncias são a série de tempo propriamente ditas. Na maioria dos casos, eles são o DeviceID ou AssetID, que é o identificador exclusivo do recurso no ambiente.

Para saber mais sobre o modelo de série de tempo, veja [modelos de série de vezes](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Painel de pesquisa de modelo de pré-visualização de informações de série de tempo

O painel de pesquisa de modelo permite-lhe facilmente procurar e navegue até a hierarquia de modelo de série de tempo para localizar as instâncias de série de tempo específico que pretende apresentar no gráfico. Quando seleciona as instâncias, eles também são adicionados para o gráfico atual e os dados.

  ![quatro Explorer][4]

## <a name="time-series-insights-preview-well"></a>Pré-visualização de informações de série de tempo bem

O também apresenta os campos de instância e outros metadados associados com instâncias de série de tempo selecionado. As caixas de verificação no lado direito permitem-lhe ocultar ou exibir instâncias específicas de um gráfico atual. Também pode remover elementos de dados específicos a partir dos seus dados atuais ao clicar no controle o x vermelho à direita do elemento.

  ![cinco Explorer][5]

Também pode destacar o painel de telemetria para obter uma melhor vista vertical dos elementos nos seus dados também.

  ![seis Explorer][6]

> [!NOTE]
> Se vir a mensagem seguinte, a instância não tem quaisquer dados durante o intervalo de tempo selecionado. Para resolver o problema, pode aumentar o intervalo de tempo ou confirmar que a instância está a enviar dados.
>
> ![sete de Explorer][7]

## <a name="time-series-insights-preview-chart"></a>Gráfico de pré-visualização de informações de série de tempo

Com o gráfico, pode exibir as instâncias de série de tempo como linhas. Pode fechar o painel de ambiente, o modelo de dados e o painel de controlo de span de tempo ao clicar em controles da web para aumentar o gráfico.

  ![Explorador de oito][8]

1. **Selecionar intervalo de datas**: Controlos de quais elementos de dados estão disponíveis para visualização.

1. **Ferramenta de controlo de deslize de intervalo de datas interna**: Utilize os dois controles de ponto final ao arrastá-los ao longo do período de tempo pretendido.

1. **Tempo o controlo de recolhimento span**: Fecha e expande o editor de span painel de tempo.

1. **Controlo de formato do eixo y**: Percorre as opções de vista do eixo y disponíveis:

    * `Default`: Cada linha tem um eixo y individual.
    * `Stacked`: Permite-lhe várias linhas no eixo y mesmo, de pilha com a alteração de dados de eixo y com base na linha selecionada.
    * `Shared`: Todos os dados do eixo y apresentados em conjunto.

1. **Elemento de dados atual**: O elemento de dados atualmente selecionados e os detalhes associados.

Podem desagregar ainda mais num setor de dados específico, left-clicking um ponto de dados no gráfico atual e, em seguida, arrastando a área selecionada para o ponto final da sua preferência. Com o botão direito a área de cinzento, selecionada e clique em zoom, conforme mostrado na imagem seguinte:

  ![Explorer e nove][9]

Depois de efetuar a ação de zoom, verá o conjunto de dados selecionado. Clique no controle de formato do eixo y para percorrer as três representações de eixo y dos seus dados do Time Series Insights.

  ![dez Explorer][10]

Aqui pode ver um exemplo de eixos y partilhado:

  ![Explorer 11][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Painel de editor de tempo de pré-visualização de informações de série de tempo

Quando trabalha com pré-visualização do Time Series Insights, primeiro selecione um intervalo de tempo. O intervalo de tempo selecionado controla o conjunto de dados que está disponível para a manipulação com os widgets de pré-visualização do Time Series Insights. Os controles da web seguintes estão disponíveis na pré-visualização do Time Series Insights para a seleção de intervalo de tempo de seu trabalho.

  ![Explorador de doze][12]

1. **Ferramenta de controlo de deslize de intervalo de datas interna**: Utilize os dois controles de ponto final ao arrastá-los ao longo do período de tempo pretendido. Este intervalo de datas de interna é restrita pelo controlo de deslize de intervalo de datas-externa.

1. **Aumentar e diminuir os botões de intervalo de datas**: Aumentar ou diminuir o intervalo de tempo ao selecionar o botão para o intervalo que pretende.

1. **Tempo o controlo de recolhimento span**: Esse controle web permite-lhe ocultar todos os controles, exceto para a ferramenta de controlo de deslize de intervalo de datas interna.

1. **Controlo de deslize de intervalo de datas OUTER**: Utilize os controlos de ponto final para selecionar o intervalo de externa datas, o que estará disponível para o seu controle de intervalo de datas interna.

1. **Vezes rápidas pendente do intervalo de datas**: Permite-lhe alternar rapidamente entre seleções span hora predefinidas, como os últimos 30 minutos, as últimas 12 horas ou um intervalo personalizado. Alterar este valor também é alterado os intervalos de intervalo disponível discutidos na ferramenta de controlo de deslize do tamanho do intervalo.

1. **Ferramenta de controlo de deslize de intervalo de tamanho**: Permite-lhe ampliar e intervalos de reduzir durante o mesmo intervalo de tempo. Esta ação fornece um controle mais preciso de movimento entre os setores de grandes dimensões de tempo. Apresenta as tendências uniformes para baixo para setores tão pequenas como um milissegundo, permitindo que veja reduções de alta resolução, granulares dos seus dados. Predefinição do controlo de deslize ponto de partida é definida como a exibição ideal dos dados da sua seleção, o que equilibra a resolução, velocidade de consulta e granularidade.

1. **Intervalo de e para o controle da web de datas**: Com esse controle da web pode facilmente clique e selecione a data pretendida e os intervalos de tempo. Também pode utilizar o controlo de alternar entre diferentes fusos horários. Depois de efetuar as alterações, para aplicar a sua área de trabalho atual, selecione **guardar**.

   ![treze-Explorer][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Painel de navegação de pré-visualização de informações de série de tempo

O painel de navegação de pré-visualização do Time Series Insights fornece as seguintes funcionalidades:

  ![14-Explorer][14]

### <a name="current-session-share-link-control"></a>Controlo de sessão atual de ligação de partilha

  ![quinze Explorer][15]

Selecione o link controle da web (realçado) para gerar um URL para guardar ou partilhar a sua sessão de trabalho atual do Azure Time Series Insights, que inclui:

* Intervalo de tempo atualmente selecionado
* Tamanho do intervalo selecionado
* Dados atualmente selecionados bem

### <a name="tenant-section"></a>Secção de inquilino

  ![16 de Explorer][16]

* Apresenta as suas informações de conta de início de sessão atuais do Time Series Insights.
* Permite-lhe alternar entre os temas do Time Series Insights disponíveis.

### <a name="theme-selection"></a>Seleção de tema

Pré-visualização de informações de série de tempo do Azure suporta dois temas:

* **Lite tema**: O tema predefinido mostrado em todo este documento.
* **Tema escuro**:  Processa o explorer conforme mostrado aqui:

  ![Explorer seventeen][17]

Aqui, pode também alterar entre idiomas suportados.

## <a name="s1s2-environment-controls"></a>Controles de ambiente de S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Painel de termos de pré-visualização de informações de série de tempo

Esta secção aplica-se apenas aos ambientes de S1/S2 existentes que tentam utilizar o Explorador na interface do Usuário atualizada. Pode querer usar o produto de disponibilidade geral e pré-visualização em combinação. Adicionámos algumas funcionalidades da interface do Usuário existente para o explorer atualizado, mas pode obter a experiência de interface do Usuário completa para o ambiente de S1/S2 no Explorador do Time Series Insights existente.  

Em lugar da hierarquia, verá o painel de termos do Time Series Insights, onde define as consultas no seu ambiente. Permite-lhe filtrar os seus dados com base num predicado.

  ![Explorador de dezoito][18]

O painel de editor de termos de pré-visualização do Time Series Insights precisa dos seguintes parâmetros:

**Onde**: Where cláusula permite-lhe rapidamente filtro seus eventos ao utilizar o conjunto de operandos listados na tabela seguinte. Se realizar uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base em que a pesquisa. Tipos de operando suportados incluem:

| Operação | Tipos suportados   | Notas |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Período de tempo de Double, DateTime, | |
| `=`, `!=`, `<>` | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo |
| `IN` | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo | Todos os operandos devem ser do mesmo tipo ou ser constante NULL. |
| `HAS` | Cadeia | Literais de cadeia de caracteres constante só são permitidas no lado direito. Cadeia de caracteres vazia e NULL não são permitidas. |

Saiba mais sobre os tipos de operações e os dados de consulta suportados lendo [expressão de série de tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemplos de onde as cláusulas

  ![dezenove-Explorer][19]

**Medida**: Esta lista pendente apresenta todas as colunas numéricas (**duplicatas**) que pode utilizar como elementos para o gráfico atual.

**Dividir por**: Esta lista pendente apresenta todas as disponíveis categóricas colunas (cadeias de caracteres) em seu modelo que pode agrupar os seus dados por. Pode adicionar até cinco termos para ver no mesmo eixo x. Introduza os parâmetros pretendidos e, em seguida, selecione **adicionar** para adicionar um novo termo.

  ![vinte Explorer][20]

Pode mostrar e ocultar elementos no painel de gráfico ao selecionar o ícone de visível, conforme mostrado na imagem seguinte. Pode remover por completo consultas ao clicar o vermelho **x**.

  ![Explorer-vinte-um][21]

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos:
* [Armazenamento de pré-visualização do Time Series Insights do Azure e de entrada](./time-series-insights-update-storage-ingress.md)
* [Modelação de dados](./time-series-insights-update-tsm.md)
* [Diagnóstico e resolução de problemas](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
