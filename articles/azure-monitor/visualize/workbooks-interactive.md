---
title: Livros do Azure Monitor com parâmetros personalizados
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723015"
---
# <a name="interactive-workbooks"></a>Livros Interativos

Os livros permitem que os autores criem relatórios e experiências interativas para os seus consumidores. A interatividade é apoiada de várias formas.

## <a name="parameter-changes"></a>Alterações de parâmetros

Quando um utilizador de um livro atualiza um parâmetro, qualquer controlo que utilize o parâmetro atualiza e retira automaticamente para refletir o estado novo. É assim que a maioria dos relatórios do portal Azure apoiam a interatividade. Os livros de trabalho fornecem-no de forma direta com o mínimo de esforço do utilizador.

Saiba mais sobre [Parâmetros em Livros](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Grelha, azulejo, seleções de gráficos

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

5. `Run query` para ver os resultados
6. Selecione o ícone _Definições Avançadas_ no rodapé de consulta (o ícone parece uma engrenagem). Isto abre o painel de configurações avançados.
7. Verifique a definição: `When an item is selected, export a parameter` .
8. Na definição que verificou, selecione *Add Parâmetro* e preencha-o com as informações abaixo.
    1. Campo para exportar: `Request`
    2. Nome do parâmetro: `SelectedRequest`
    3. Valor predefinido: `All requests`
9. Selecionar Guardar

    ![Screenshot mostrando o editor avançado com configurações para campos de exportação como parâmetros.](./media/workbooks-interactive/export-parameters-add.png)

10. Selecione `Done Editing`.
11. Adicione outro controlo de consulta utilizando os passos 2 e 3.
12. Utilize o editor de consulta para introduzir o KQL para a sua análise.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` para ver os resultados.
14. Alterar _visualização_ para `Area chart` .
15. Escolha uma linha para selecionar na primeira grelha. Note como o gráfico de área abaixo filtra para o pedido selecionado.

O relatório resultante é o seguinte no modo de edição:

![Screenshot das duas primeiras consultas no modo de edição.](./media/workbooks-interactive//interactivity-grid-create.png)

A imagem abaixo mostra um relatório interativo mais elaborado em modo de leitura baseado nos mesmos princípios. O relatório utiliza cliques de grelha para exportar parâmetros - que por sua vez são usados em dois gráficos e um bloco de texto.

![Screenshot um relatório usando cliques de grelha no modo de leitura.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

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

5. `Run query` para ver os resultados
6. Selecione _Definições de colunas_ para abrir o painel de definições.
7. Na secção _Colunas,_ conjunto:
    1. _Amostra_ - Renderização de Colunas: `Link` , Vista a abrir: , Etiqueta de `Cell Details` Ligação: `Sample`
    2. _Contagem_ - Renderizador de Colunas: `Bar` , Paleta de cores: , Valor `Blue` mínimo: `0`
    3. _Pedido_ - Renderizador de Colunas: `Automatic`
    4. _Selecione Guardar e Fechar_ para aplicar alterações

    ![Screenshot do separador da definição da coluna.](./media/workbooks-interactive/column-settings.png)

8. Clique num dos `Sample` links da grelha. Isto abre um painel com os detalhes de um pedido amostrado.

    ![Screenshot do painel de detalhes do pedido amostrado.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Ações de renderizador de ligação

| Ação de ligação | Ação ao clique |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores da linha num separador de contexto de grelha de propriedade |
| `Cell Details` | Mostra o valor do telemóvel num separador de contexto de grelha de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de pedido como localização, instância de função, etc.). |
| `Cell Details` | Mostra o valor do telemóvel num separador de contexto de grelha de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de pedido como localização, instância de função, etc.). |
| `Custom Event Details` | Abre os detalhes de pesquisa de Insights de Aplicação com o ID do evento personalizado na `itemId` célula |
| `* Details` | Semelhante a Detalhes de Eventos Personalizados, exceto dependências, exceções, visualizações de página, pedidos e vestígios. |
| `Custom Event User Flows` | Abre a experiência Application Insights User Flows apostada no nome do evento personalizado na célula |
| `* User Flows` | Semelhante aos Fluxos personalizados do utilizador do evento, exceto para exceções, visualizações de página e pedidos |
| `User Timeline` | Abre a linha de tempo do utilizador com o ID do utilizador (user_Id) na célula |
| `Session Timeline` | Abre a experiência de pesquisa de Insights de Aplicação para o valor na célula (por exemplo, procure por texto 'abc' onde abc é o valor na célula) |
| `Resource overview` | Abra a visão geral do recurso no portal com base no valor de ID de recurso na célula |

## <a name="conditional-visibility"></a>Visibilidade Condicional

O livro permite que os utilizadores façam com que certos controlos apareçam ou desapareçam com base nos valores dos parâmetros. Isto permite que os autores tenham relatórios diferentes com base na entrada do utilizador ou no estado da telemetria. Um exemplo é mostrar aos consumidores apenas um resumo quando as coisas estão boas, mas mostrar todos os detalhes quando algo está errado.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Criação de interatividade utilizando visibilidade condicional

1. Siga os passos na secção [de interatividade de Configuração na](#setting-up-interactivity-on-grid-row-click) secção de clique sonoro da grelha para configurar dois controlos interativos.
2. Adicione um novo parâmetro no topo:
    1. Nome: `ShowDetails`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. Obtenha dados de: `JSON`
    5. Entrada JSON: `["Yes", "No"]`
    6. Poupe para cometer alterações.

    ![Depois de selecionar o botão de parâmetro de adicionar, é apresentado o painel de parâmetros de edição.](./media/workbooks-interactive/edit-parameter.png)

3. Definir o valor do parâmetro para `Yes`

    ![Acima do botão de edição feito é a queda que lhe permitirá definir o valor do parâmetro](./media/workbooks-interactive/set-parameter.png)

4. No controlo de consultas com o gráfico de área, selecione o ícone _Definições Avançadas_ (ícone de engrenagem)
5. Verifique a definição `Make this item conditionally visible`
    1. Este item é visível se `ShowDetails` o valor do `equals` parâmetro `Yes`
6. Selecione _'Editar'_ para cometer alterações.
7. Selecione _'Editar'_ na barra de ferramentas do livro para entrar no modo de leitura.
8. Mude o valor do parâmetro `ShowDetails` para `No` . Note que o gráfico abaixo desaparece.

A imagem abaixo mostra o caso visível onde `ShowDetails` está `Yes`

![Screenshot mostrando a visibilidade condicional onde o gráfico é visível](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

A imagem abaixo mostra o caso escondido onde `ShowDetails` está `No`

![Screenshot mostrando a visibilidade condicional onde o gráfico está escondido](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interatividade com múltiplas seleções em grelhas e gráficos

Os passos de consulta e métricas também podem exportar um ou mais parâmetros quando uma linha (ou várias linhas) é selecionada.

![Screenshot mostrando as definições de parâmetros de exportação com vários parâmetros. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. No passo de consulta que mostra a grelha, vá para as definições avançadas.
2. Verifique a `When items are selected, export parameters` caixa de verificação. Aparecerão controlos adicionais.
3. Verifique a `allow selection of multiple values` caixa de verificação.
    1. A visualização visualizada permitirá que os valores do parâmetro multi-selecionado e exportado sejam matrizes de valores, como quando se utilizam parâmetros de queda multi-selecção.
    2. Se não for verificada, a visualização do visor só respeitará o último item selecionado. Exportando apenas um único valor de cada vez.
4. Para cada parâmetro que pretende exportar, utilize o botão *Adicionar Parâmetro.* Aparecerá uma janela pop-up contendo as definições para o parâmetro a exportar.

Quando a seleção única estiver ativada, o autor pode especificar qual o campo dos dados originais a exportar. Os campos incluem o nome do parâmetro, o tipo de parâmetro e o valor predefinido a utilizar se nada for selecionado (opcional).

Quando a multi-selecção está ativada, o autor especifica qual o campo dos dados originais a exportar. Os campos incluem nome de parâmetro, tipo de parâmetro, citação com e delimiter. Os valores de cotação e delimiter são utilizados ao transformar os valores da seta em texto quando são substituídos numa consulta. Em multi-selecção se não forem selecionados valores, o valor predefinido é um conjunto vazio.

> [!NOTE]
> Para multi seleções, apenas valores únicos serão exportados, você não verá valores de matriz de saída como " 1,1,2,1" você receberá "1,2" como os valores de saída.

Pode deixar o "Campo para exportar" vazio nas definições de exportação. Se o fizer, todos os campos disponíveis nos dados serão exportados como um objeto JSON de par de chaves:valor. Para grelhas e títulos, este será todo o campo da grelha. Para gráficos, os campos disponíveis serão x,y,série e etiqueta (dependendo do tipo de gráfico).

Embora o comportamento padrão seja exportar um parâmetro como texto, se souber que o campo é uma subscrição ou ID de recursos, use-o como o tipo de parâmetro de exportação. Isto permitirá que o parâmetro seja utilizado a jusante em locais que exijam este tipo de parâmetros.

## <a name="next-steps"></a>Passos seguintes

* [Começar a](./workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.