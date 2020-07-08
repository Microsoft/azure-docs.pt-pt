---
title: Livros do Azure Monitor com parâmetros personalizados
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658276"
---
# <a name="interactive-workbooks"></a>Livros Interativos

Os livros permitem que os autores criem relatórios e experiências interativas para os seus consumidores. A interatividade é apoiada de várias formas.

## <a name="parameter-changes"></a>Alterações de parâmetros
Quando um utilizador de um livro atualiza um parâmetro, qualquer controlo que utilize o parâmetro atualiza e retira automaticamente para refletir o estado novo. É assim que a maioria dos relatórios do portal Azure apoiam a interatividade. Os livros fornecem isto de forma muito simples, com o mínimo de esforço do utilizador.

Saiba mais sobre [Parâmetros em Livros](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Cliques de linha de grelha
Os livros de trabalho permitem que os autores construam cenários onde clicar numa linha numa grelha atualiza gráficos subsequentes com base no conteúdo da linha. 

Por exemplo, um utilizador pode ter uma grelha que mostra uma lista de pedidos e algumas estatísticas como a contagem de falhas. Poderiam configurar de tal forma que clicar numa linha correspondente a um pedido, resultará em gráficos detalhados abaixo da atualização para filtrar apenas esse pedido.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Configurar a interatividade no clique da linha da grelha
1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link _de consulta Adicionar_ para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como _Log_, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`para ver os resultados
6. Clique no ícone _Definições Avançadas_ no rodapé de consulta (o ícone parece uma engrenagem). Isto abre o painel de configurações avançadas 
7. Verifique a definição:`When an item is selected, export a parameter`
    1. Campo para exportar:`Request`
    2. Nome do parâmetro:`SelectedRequest`
    3. Valor predefinido:`All requests`
    
    ![Imagem mostrando o editor avançado com configurações para campos de exportação como parâmetros](./media/workbooks-interactive/advanced-settings.png)

8. Clique em `Done Editing`.
9. Adicione outro controlo de consulta utilizando os passos 2 e 3.
10. Utilize o editor de consulta para introduzir o KQL para a sua análise
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`para ver os resultados.
12. Alterar _visualização_ para`Area chart`
12. Clique numa linha na primeira grelha. Note como o gráfico de área abaixo filtra para o pedido selecionado.

O relatório resultante é o seguinte no modo de edição:

![Imagem mostrando a criação de uma experiência interativa usando cliques de linha de grelha](./media/workbooks-interactive//grid-click-create.png)

A imagem abaixo mostra um relatório interativo mais elaborado em modo de leitura baseado nos mesmos princípios. O relatório utiliza cliques de grelha para exportar parâmetros - que por sua vez são usados em dois gráficos e um bloco de texto.

![Imagem mostrando a criação de uma experiência interativa usando cliques de linha de grelha](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportação do conteúdo de uma linha inteira
Por vezes, é desejável exportar todo o conteúdo da linha selecionada em vez de apenas uma coluna em particular. Nesses casos, deixe a `Field to export` propriedade desaparada no passo 7.1 acima. Os livros de trabalho exportarão todo o conteúdo da linha como um json para o parâmetro. 

No controlo KQL de referência, utilize a `todynamic` função para analisar o json e aceder às colunas individuais.

 ## <a name="grid-cell-clicks"></a>Cliques de células de grelha
Os livros permitem que os autores adicionem interatividade através de um tipo especial de renderizador de colunas de grelha chamado a `link renderer` . Um renderizador de ligação converte uma célula de grelha numa hiperligação baseada no conteúdo da célula. Os livros de trabalho suportam muitos tipos de renderizadores de ligação - incluindo aqueles que permitem abrir lâminas gerais de recursos, espectadores de sacos de propriedade, pesquisa de App Insights, uso, rastreio de transações, etc.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Configuração da interatividade usando cliques de células de rede
1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Utilize o link _de consulta Adicionar_ para adicionar um controlo de consulta de registo ao livro de trabalho. 
3. Selecione o tipo de consulta como _Log_, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`para ver os resultados
6. Clique nas _Definições de Coluna_ para abrir o painel de definições.
7. Na secção _Colunas,_ conjunto:
    1. _Amostra_ - Renderização de Colunas: `Link` , Vista a abrir: , Etiqueta de `Cell Details` Ligação:`Sample`
    2. _Contagem_ - Renderizador de Colunas: `Bar` , Paleta de cores: , Valor `Blue` mínimo:`0`
    3. _Pedido_ - Renderizador de Colunas:`Automatic`
    4. Clique _em Guardar e Fechar_ para aplicar alterações
8. Clique num dos `Sample` links da grelha. Isto abre um painel de propriedades com os detalhes de um pedido amostrado.

    ![Imagem mostrando a criação de uma experiência interativa usando cliques de células de grelha](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Ações de renderizador de ligação
| Ação de ligação | Ação ao clique |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores da linha em uma lâmina de contexto de grelha de propriedade |
| `Cell Details` | Mostra o valor da célula numa lâmina de contexto de grelha de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de pedido como localização, instância de função, etc.). |
| `Cell Details` | Mostra o valor da célula numa lâmina de contexto de grelha de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de pedido como localização, instância de função, etc.). |
| `Custom Event Details` | Abre os detalhes de pesquisa de Insights de Aplicação com o ID do evento personalizado (itemId) na célula |
| `* Details` | Semelhante a Detalhes de Eventos Personalizados, exceto dependências, exceções, visualizações de página, pedidos e vestígios. |
| `Custom Event User Flows` | Abre a experiência Application Insights User Flows apostada no nome do evento personalizado na célula |
| `* User Flows` | Semelhante aos Fluxos personalizados do utilizador do evento, exceto para exceções, visualizações de página e pedidos |
| `User Timeline` | Abre a linha de tempo do utilizador com o ID do utilizador (user_Id) na célula |
| `Session Timeline` | Abre a experiência de pesquisa de Insights de Aplicação para o valor na célula (por exemplo, procure por texto 'abc' onde abc é o valor na célula) |
| `Resource overview` | Abra a visão geral do recurso no portal com base no valor de ID de recurso na célula |

## <a name="conditional-visibility"></a>Visibilidade Condicional
O livro permite que os utilizadores façam com que certos controlos apareçam ou desapareçam com base nos valores dos parâmetros. Isto permite que os autores tenham relatórios diferentes com base na entrada do utilizador ou no estado da telemetria. Um exemplo é mostrar aos consumidores apenas um resumo quando as coisas estão boas, mas mostrar todos os detalhes quando algo está errado.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Criação de interatividade utilizando visibilidade condicional
1. Siga os passos na `Setting up interactivity on grid row click` secção para configurar dois controlos interativos.
2. Adicione um novo parâmetro no topo:
    1. Nome:`ShowDetails`
    2. Tipo de parâmetro:`Drop down`
    3. Necessário:`checked`
    4. Obtenha dados de:`JSON`
    5. Entrada JSON:`["Yes", "No"]`
    6. Poupe para cometer alterações.
3. Definir o valor do parâmetro para`Yes`
4. No controlo de consulta com o gráfico de área, clique no ícone _Definições Avançadas_ (ícone de engrenagem)
5. Verifique a definição`Make this item conditionally visible`
    1. Este item é visível se `ShowDetails` o valor do `equals` parâmetro`Yes`
6. Clique _em Edição Feita_ para cometer alterações.
7. Clique em _Editar_ na barra de ferramentas do livro para entrar no modo de leitura.
8. Mude o valor do parâmetro `ShowDetails` para `No` . Note que o gráfico abaixo desaparece.

A imagem abaixo mostra o caso visível onde `ShowDetails` está`Yes`

![Imagem mostrando a visibilidade condicional onde o gráfico é visível](./media/workbooks-interactive/conditional-visibility.png)

A imagem abaixo mostra o caso escondido onde `ShowDetails` está`No`

![Imagem mostrando a visibilidade condicional onde o gráfico está escondido](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Próximos passos


* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
