---
title: Criar relatórios interativos com Azure Monitor parâmetros personalizados de pastas de trabalho | Microsoft docs
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: eeb6eb5d8b3ad6498ff90a9afe1fa4f2c18d30e5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165956"
---
# <a name="interactive-workbooks"></a>Pastas de trabalho interativas

As pastas de trabalho permitem que os autores criem relatórios e experiências interativos para seus consumidores. Há suporte para interatividade de várias maneiras.

## <a name="parameter-changes"></a>Alterações de parâmetro
Quando um usuário da pasta de trabalho atualiza um parâmetro, qualquer controle que usa o parâmetro é atualizado automaticamente e redesenha para refletir o novo estado. É assim que a maioria dos relatórios de portal do Azure dá suporte à interatividade. As pastas de trabalho fornecem isso de maneira muito simples com o mínimo de esforço do usuário.

Saiba mais sobre [parâmetros em pastas de trabalho](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Cliques na linha de grade
As pastas de trabalho permitem que os autores construam cenários em que clicar em uma linha em uma grade atualiza os gráficos subsequentes com base no conteúdo da linha. 

Por exemplo, um usuário pode ter uma grade que mostra uma lista de solicitações e algumas estatísticas como contagens de falha. Eles poderiam configurá-lo de tal forma que clicar em uma linha correspondente a uma solicitação, resultará em gráficos detalhados abaixo da atualização para filtrar até a mesma solicitação.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Configurando a interatividade na linha de grade clique
1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link _Adicionar consulta_ para adicionar um controle de consulta de log à pasta de trabalho. 
3. Selecione o tipo de consulta como _log_, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` para ver os resultados
6. Clique no ícone _Configurações avançadas_ no rodapé da consulta (o ícone é semelhante a uma engrenagem). Isso abre o painel Configurações avançadas 
7. Verifique a configuração: `When an item is selected, export a parameter`
    1. Campo a ser exportado: `Request`
    2. Nome do parâmetro: `SelectedRequest`
    3. Valor padrão: `All requests`
    
    ![Imagem mostrando o editor avançado com configurações para exportar campos como parâmetros](./media/workbooks-interactive/advanced-settings.png)

8. Clique em `Done Editing`.
9. Adicione outro controle de consulta usando as etapas 2 e 3.
10. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` para ver os resultados.
12. Alterar a _Visualização_ para `Area chart`
12. Clique em uma linha na primeira grade. Observe como o gráfico de área abaixo é filtrado para a solicitação selecionada.

O relatório resultante tem esta aparência no modo de edição:

![Imagem mostrando a criação de uma experiência interativa usando cliques de linha de grade](./media/workbooks-interactive//grid-click-create.png)

A imagem abaixo mostra um relatório interativo mais elaborado no modo de leitura com base nos mesmos princípios. O relatório usa cliques de grade para exportar parâmetros – que, por sua vez, são usados em dois gráficos e um bloco de texto.

![Imagem mostrando a criação de uma experiência interativa usando cliques de linha de grade](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportando o conteúdo de uma linha inteira
Às vezes, é desejável exportar todo o conteúdo da linha selecionada em vez de apenas uma coluna específica. Nesses casos, deixe a propriedade de `Field to export` desdefinida na etapa 7,1 acima. As pastas de trabalho vão exportar todo o conteúdo da linha como um JSON para o parâmetro. 

No controle KQL de referência, use a função `todynamic` para analisar o JSON e acessar as colunas individuais.

 ## <a name="grid-cell-clicks"></a>Cliques de célula de grade
As pastas de trabalho permitem que os autores adicionem interatividade por meio de um tipo especial de renderizador de coluna de grade chamado um `link renderer`. Um renderizador de link converte uma célula de grade em um hiperlink com base no conteúdo da célula. As pastas de trabalho dão suporte a vários tipos de renderizadores de link-incluindo aqueles que permitem abrir folhas de visão geral de recursos, visualizadores de conjunto de propriedades, pesquisa, uso, rastreamento de transações, etc.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Configurando a interatividade usando cliques de célula de grade
1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link _Adicionar consulta_ para adicionar um controle de consulta de log à pasta de trabalho. 
3. Selecione o tipo de consulta como _log_, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` para ver os resultados
6. Clique em _configurações de coluna_ para abrir o painel configurações.
7. Na seção _colunas_ , defina:
    1. _Exemplo_ -renderizador de coluna: `Link`, exibir para abrir: `Cell Details`, rótulo de Link: `Sample`
    2. Processador de _contagem_ de colunas: `Bar`, paleta de cores: `Blue`, valor mínimo: `0`
    3. Renderizador de coluna de _solicitação_ : `Automatic`
    4. Clique em _salvar e fechar_ para aplicar as alterações
8. Clique em um dos links de `Sample` na grade. Isso abre um painel de propriedades com os detalhes de uma solicitação de amostra.

    ![Imagem mostrando a criação de uma experiência interativa usando cliques de células de grade](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Ações do renderizador de link
| Ação do link | Ação ao clicar |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores de linha em uma folha de contexto de grade de propriedades |
| `Cell Details` | Mostra o valor da célula em uma folha de contexto de grade de propriedades. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, JSON com propriedades de solicitação, como local, instância de função, etc.). |
| `Cell Details` | Mostra o valor da célula em uma folha de contexto de grade de propriedades. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, JSON com propriedades de solicitação, como local, instância de função, etc.). |
| `Custom Event Details` | Abre os detalhes da pesquisa Application Insights com a ID do evento personalizado (itemId) na célula |
| `* Details` | Semelhante aos detalhes do evento personalizado, exceto para dependências, exceções, exibições de página, solicitações e rastreamentos. |
| `Custom Event User Flows` | Abre o Application Insights Fluxos dos Usuários experiência dinamizada no nome do evento personalizado na célula |
| `* User Flows` | Semelhante ao Fluxos dos Usuários de eventos personalizado, exceto para exceções, exibições de página e solicitações |
| `User Timeline` | Abre a linha do tempo do usuário com a ID de usuário (user_Id) na célula |
| `Session Timeline` | Abre a experiência de pesquisa Application Insights para o valor na célula (por exemplo, pesquise o texto ' abc ' em que ABC é o valor na célula) |
| `Resource overview` | Abra a visão geral do recurso no portal com base no valor da ID do recurso na célula |

## <a name="conditional-visibility"></a>Visibilidade condicional
A pasta de trabalho permite que os usuários façam com que determinados controles apareçam ou desapareçam com base nos valores dos parâmetros. Isso permite que os autores tenham a aparência de relatórios diferentes com base no estado de entrada ou telemetria do usuário. Um exemplo é mostrar aos consumidores apenas um resumo quando as coisas são boas, mas mostram detalhes completos quando algo está errado.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Configurando a interatividade usando a visibilidade condicional
1. Siga as etapas na seção `Setting up interactivity on grid row click` para configurar dois controles interativos.
2. Adicione um novo parâmetro na parte superior:
    1. Nome: `ShowDetails`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. Obter dados de: `JSON`
    5. Entrada JSON: `["Yes", "No"]`
    6. Salve para confirmar as alterações.
3. Definir valor do parâmetro como `Yes`
4. No controle de consulta com o gráfico de área, clique no ícone _Configurações avançadas_ (ícone de engrenagem)
5. Verifique a configuração `Make this item conditionally visible`
    1. Esse item estará visível se `ShowDetails` valor do parâmetro `equals` `Yes`
6. Clique em _edição concluída_ para confirmar as alterações.
7. Clique em _edição concluída_ na barra de ferramentas da pasta de trabalho para entrar no modo de leitura.
8. Alterne o valor do parâmetro `ShowDetails` para `No`. Observe que o gráfico abaixo desaparece.

A imagem abaixo mostra o caso visível em que `ShowDetails` é `Yes`

![Imagem mostrando a visibilidade condicional em que o gráfico está visível](./media/workbooks-interactive/conditional-visibility.png)

A imagem abaixo mostra o caso oculto em que `ShowDetails` é `No`

![Imagem mostrando a visibilidade condicional em que o gráfico está oculto](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Passos seguintes


* [Comece a aprender mais](workbooks-visualizations.md) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
