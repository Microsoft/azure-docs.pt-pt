---
title: Os livros de trabalho do Monitor Azure ligam ações
description: Como utilizar ações de ligação em livros de trabalho do Monitor Azure
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100616041"
---
# <a name="link-actions"></a>Ações de ligação

As ações de ligação podem ser acedidas através de etapas de link workbook ou através de configurações de colunas de [grelhas,](../visualize/workbooks-grid-visualizations.md) [títulos](../visualize/workbooks-tile-visualizations.md)ou [gráficos](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Ações de ligação geral

| Ação de ligação | Ação ao clique |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores de linha numa vista de contexto de grelha de propriedade. |
| `Cell Details` | Mostra o valor do telemóvel numa vista de contexto de grelha de propriedade. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, json com propriedades de pedido como localização, instância de função, etc.). |
| `Url` | Espera-se que o valor da célula seja um url http válido, e a célula será um link que abre esse url num novo separador.|

## <a name="application-insights"></a>Application Insights

| Ação de ligação | Ação ao clique |
|:------------- |:-------------|
| `Custom Event Details` | Abre os detalhes de pesquisa de Insights de Aplicação com o ID do evento personalizado `itemId` na célula. |
| `* Details` | Semelhante a Detalhes de Eventos Personalizados, exceto dependências, exceções, visualizações de página, pedidos e vestígios. |
| `Custom Event User Flows` | Abre a experiência Application Insights User Flows apostada no nome do evento personalizado na célula. |
| `* User Flows` | Semelhante aos Fluxos personalizados do utilizador do evento, exceto exceções, visualizações de página e pedidos. |
| `User Timeline` | Abre a linha do tempo do utilizador com o ID do utilizador `user_Id` na célula. |
| `Session Timeline` | Abre a experiência de pesquisa de Insights de Aplicação para o valor na célula (por exemplo, procure por texto 'abc' onde o abc é o valor na célula). |

`*` denota um wildcard para a tabela acima

## <a name="azure-resource"></a>Recurso do Azure

| Ação de ligação | Ação ao clique |
|:------------- |:-------------|
| `ARM Deployment` | Implemente um modelo de Gestor de Recursos Azure.  Quando este item é selecionado, são apresentados campos adicionais para permitir que o autor configufique qual o modelo do Gestor de Recursos Azure para abrir, parâmetros para o modelo, etc. Ver definições de [link de implementação do Gestor de Recursos Azure](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Cria uma regra de alerta para um recurso.  |
| `Custom View` | Abre uma vista personalizada. Quando este item é selecionado, são apresentados campos adicionais para permitir que o autor configuure a extensão do Ver, ver o nome e quaisquer parâmetros utilizados para abrir o View. [Ver vista personalizada.](#custom-view-link-settings) |
| `Metrics` | Abre uma vista de métricas.  |
| `Resource overview` | Abra a visão do recurso no portal com base no valor de identificação de recursos na célula. O autor também pode configurar opcionalmente um `submenu` valor que abrirá um item de menu específico na vista do recurso. |
| `Workbook (template)` | Abra um modelo de livro.  Quando este item é selecionado, são apresentados campos adicionais para permitir que o autor configuure o modelo a abrir, etc.  |

## <a name="link-settings"></a>Definições de ligação

Ao utilizar o renderizador de links, estão disponíveis as seguintes definições:

![Screenshot das definições de link](./media/workbooks-link-actions/link-settings.png)

| Definição | Explicação |
|:------------- |:-------------|
| `View to open` | Permite ao autor selecionar uma das ações enumeradas acima. |
| `Menu item` | Se for selecionado "Visão Geral do Recurso", este é o item do menu na visão geral do recurso a abrir. Isto pode ser usado para abrir alertas ou registos de atividade em vez da "visão geral" do recurso. Os valores dos artigos do menu são diferentes para cada `Resourcetype` Azure.|
| `Link label` | Se especificado, este valor será apresentado na coluna da grelha. Se este valor não for especificado, o valor da célula será apresentado. Se quiser que seja apresentado outro valor, como um mapa de calor ou ícone, não utilize o `Link` renderizador, utilize o renderizador apropriado e selecione a `Make this item a link` opção. |
| `Open link in Context Blade` | Se especificado, o link será aberto como uma visão pop-up "context" no lado direito da janela em vez de abrir como uma vista completa. |

Ao utilizar a `Make this item a link` opção, estão disponíveis as seguintes definições:

| Definição | Explicação |
|:------------- |:-------------|
| `Link value comes from` | Ao exibir uma célula como um renderizador com um link, este campo especifica de onde provém o valor de "link" a utilizar no link, permitindo ao autor selecionar a partir de um dropdown das outras colunas da grelha. Por exemplo, a célula pode ser um valor de mapa de calor, mas você deseja que o link abra a visão geral do recurso para o ID de recursos na linha. Nesse caso, definiria o valor do link para vir do `Resource Id` campo.
| `View to open` | mesmo como acima. |
| `Menu item` | mesmo como acima. |
| `Open link in Context Blade` | mesmo como acima. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Definições de ligação de implementação do Gestor de Recursos Azure

Se o tipo de ligação selecionado for `ARM Deployment` o autor deve especificar definições adicionais para abrir uma implementação do Gestor de Recursos Azure. Existem dois separadores principais para configurações.

### <a name="template-settings"></a>Configurações do modelo

Esta secção define de onde o modelo deve vir e os parâmetros utilizados para executar a implementação do Gestor de Recursos Azure.

| Origem | Explicação |
|:------------- |:-------------|
|`Resource group id comes from` | O ID de recursos é usado para gerir recursos implantados. A subscrição é utilizada para gerir recursos e custos implantados. Os grupos de recursos são usados como pastas para organizar e gerir todos os seus recursos. Se este valor não for especificado, a implantação falhará. Selecione a partir de `Cell` , , , ou em `Column` `Static Value` `Parameter` [fontes de ligação](#link-sources).|
|`ARM template URI from` | O URI para o próprio modelo do Gestor de Recursos Azure. O modelo URI precisa de ser acessível aos utilizadores que irão implementar o modelo. Selecione a partir de `Cell` , , , ou em `Column` `Parameter` `Static Value` [fontes de ligação](#link-sources). Para começar, dê uma olhada nos [nossos modelos de arranque rápido.](https://azure.microsoft.com/resources/templates/)|
|`ARM Template Parameters` | Esta secção define os parâmetros do modelo utilizados para o modelo URI definido acima. Estes parâmetros serão utilizados para implantar o modelo na página de execução. A grelha contém um botão de barra de ferramentas de expansão para ajudar a preencher os parâmetros utilizando os nomes definidos no modelo URI e defini-lo para valores estáticos vazios. Esta opção só pode ser utilizada quando não existem parâmetros na grelha e o modelo URI foi definido. A secção inferior é uma pré-visualização de como é a saída do parâmetro. Selecione Refresh para atualizar a pré-visualização com as alterações atuais. Os parâmetros são tipicamente valores, enquanto as referências são algo que pode apontar para segredos de teclado a que o utilizador tem acesso. <br/><br/> **Limitação da lâmina do visualizador do modelo** - não torna os parâmetros de referência corretamente e aparecerá como nulo/valor, pelo que os utilizadores não serão capazes de implementar corretamente parâmetros de referência a partir do separador Visualizador de Modelos.|

![Screenshot das definições do modelo do Gestor de Recursos Azure](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Definições de UX

Esta secção configura o que os utilizadores verão antes de executarem a implementação do Gestor de Recursos Azure.

| Origem | Explicação |
|:------------- |:-------------|
|`Title from` | Título usado na vista de corrida. Selecione a partir de `Cell` , , , ou em `Column` `Parameter` `Static Value` [fontes de ligação](#link-sources).|
|`Description from` | Este é o texto de marcação usado para fornecer uma descrição útil aos utilizadores quando eles querem implementar o modelo. Selecione a partir de `Cell` , , , ou em `Column` `Parameter` `Static Value` [fontes de ligação](#link-sources). <br/><br/> **NOTA:** Se `Static Value` for selecionada, aparecerá uma caixa de texto multi-linha. Nesta caixa de texto, pode resolver os parâmetros utilizando `{paramName}` . Também pode tratar as colunas como parâmetros, apecando `_column` após o nome da coluna como `{columnName_column}` . Na imagem de exemplo abaixo, podemos fazer referência à coluna `VMName` `{VMName_column}` escrevendo. O valor após o cólon é o [parâmetro formatter,](../visualize/workbooks-parameters.md#parameter-options)neste caso é `value` .|
|`Run button text from` | Etiqueta utilizada no botão run (executar) para implantar o modelo do Gestor de Recursos Azure. Isto é o que os utilizadores irão selecionar para começar a implementar o modelo Azure Resource Manager.|

![Screenshot das definições de UX do Gestor de Recursos Azure](./media/workbooks-link-actions/ux-settings.png)

Depois de definidas estas configurações, quando os utilizadores selecionarem o link, abrirá a vista com o UX descrito nas definições UX. Se o utilizador `Run button text from` selecionar, implementará um modelo de Gestor de Recursos Azure utilizando os valores das [definições](#template-settings)do modelo . O modelo de visualização abrirá o separador do visualizador do modelo para o utilizador examinar o modelo e os parâmetros antes de ser implementado.

![Screenshot da visão do gestor de recursos Azure](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Definições de link de visualização personalizada

Utilize isto para abrir vistas personalizadas no portal Azure. Verifique todas as configurações e configurações. Valores incorretos causarão erros no portal ou não abrirão corretamente as vistas. Existem duas formas de configurar as definições através do `Form` ou `URL` . .

> [!NOTE]
> As vistas com um menu não podem ser abertas num separador de contexto. Se uma vista com um menu estiver configurada para abrir num separador de contexto, nenhum separador de contexto será mostrado quando o link for selecionado.

### <a name="form"></a>Formulário

| Origem | Explicação |
|:------------- |:-------------|
|`Extension name` | O nome da extensão que acolhe o nome do View.|
|`View name` | O nome do View para abrir.|

#### <a name="view-inputs"></a>Ver entradas

Existem dois tipos de entradas, grelhas e JSON. Utilize `Grid` para entradas simples de teclas e de separador de valor ou selecione para `JSON` especificar uma entrada JSON aninhada.

- Grelha
    - `Parameter Name`: O nome do parâmetro de entrada 'Ver'.
    - `Parameter Comes From`: De onde deve vir o valor do parâmetro 'Ver'. Selecione a partir de `Cell` , , , ou em `Column` `Parameter` `Static Value` [fontes de ligação](#link-sources).
    > [!NOTE]
    > Se `Static Value` for selecionado, os parâmetros podem ser resolvidos utilizando o link dos suportes `{paramName}` na caixa de texto. As colunas podem ser tratadas como colunas de parâmetros por `_column` anexação após o nome da coluna como `{columnName_column}` .

    - `Parameter Value`: `Parameter Comes From` dependendo, este será um recuo dos parâmetros, colunas ou um valor estático disponível.

    ![Screenshot da definição de coluna de edição mostra as definições de visualização personalizada a partir do formulário.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Especifique a entrada do separador num formato JSON no editor. Tal como o `Grid` modo, os parâmetros e colunas podem ser referenciados utilizando `{paramName}` parâmetros e `{columnName_column}` colunas. Selecionando `Show JSON Sample` , mostrará a saída esperada de todos os parâmetros e colunas resolvidos utilizador para a entrada de visualização.

    ![Screenshot mostrando as definições de vista personalizada aberta com entrada de visualização no JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Cole um URL do Portal contendo a extensão, o nome da vista e quaisquer entradas necessárias para abrir a vista. Após a `Initialize Settings` seleção, irá povoar o autor para `Form` adicionar/modificar/remover qualquer uma das entradas de visualização.

![A definição da coluna mostra as definições de visualização personalizada do URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Definições de ligação do livro (modelo)

Se o tipo de ligação selecionado `Workbook (Template)` for, o autor deve especificar definições adicionais para abrir o modelo correto do livro. As definições abaixo têm opções de como a grelha encontrará o valor adequado para cada uma das definições.

| Definição | Explicação |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Este é o ID de recursos do Recurso Azure que "possui" o livro. Geralmente é um recurso Application Insights, ou um Log Analytics Workspace. Dentro do Azure Monitor, esta também pode ser a corda `"Azure Monitor"` literal. Quando o livro estiver guardado, é a isto que o livro estará ligado. |
| `Workbook resources` | Uma matriz de IDs de recursos Azure que especificam o recurso predefinido utilizado no livro. Por exemplo, se o modelo que está a ser aberto mostrar métricas de Máquina Virtual, os valores aqui seriam IDs de recursos de máquina virtual.  Muitas vezes, o proprietário e os recursos são definidos para as mesmas configurações. |
| `Template Id` | Especifique o ID do modelo a abrir. Se este é um modelo comunitário da galeria (o caso mais comum), prefixe o caminho para o modelo com `Community-` , como para o `Community-Workbooks/Performance/Apdex` `Workbooks/Performance/Apdex` modelo. Se este é um link para um livro/modelo guardado, é o ID completo do recurso Azure desse item. |
| `Workbook Type` | Especifique o tipo de modelo de livro para abrir. Os casos mais comuns usam o `Default` ou `Workbook` opção de usar o valor no livro atual. |
| `Gallery Type` | Isto especifica o tipo de galeria que será exibido na vista "Galeria" do modelo que abre. Os casos mais comuns usam o `Default` ou `Workbook` opção de usar o valor no livro atual. |
|`Location comes from` | O campo de localização deve ser especificado se estiver a abrir um recurso específico do Livro de Trabalho. Se a localização não for especificada, encontrar o conteúdo do livro é muito mais lento. Se souber a localização, especifique-a. Se não souber a localização ou estiver a abrir um modelo que sem localização específica, deixe este campo como "Padrão".|
|`Pass specific parameters to template` | Selecione para passar parâmetros específicos para o modelo. Se selecionados, apenas os parâmetros especificados são passados para o modelo de outros 100 parâmetros todos os parâmetros do livro atual são passados para o modelo e, nesse caso, os *nomes* dos parâmetros devem ser os mesmos em ambos os livros para que este valor de parâmetro funcione.|
|`Workbook Template Parameters` | Esta secção define os parâmetros que são passados para o modelo-alvo. O nome deve coincidir com o nome do parâmetro no modelo-alvo. Selecione valor de `Cell` `Column` , , e `Parameter` `Static Value` . O nome e o valor não devem estar vazios para passar esse parâmetro para o modelo-alvo.|

Para cada uma das definições acima, o autor deve escolher de onde virá o valor do livro ligado. Ver [link Fontes](#link-sources)

Quando o link do livro for aberto, a nova visão do livro será passada todos os valores configurados a partir das definições acima.

![Screenshot das definições de link do livro](./media/workbooks-link-actions/workbook-link-settings.png)

![Screenshot das definições de parâmetros do modelo do livro](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Fontes de ligação

| Origem | Explicação |
|:------------- |:-------------|
| `Cell` | Isto usará o valor naquela célula na grelha como o valor de ligação |
| `Column` | Quando selecionado, será apresentado outro campo para permitir que o autor selecione outra coluna na grelha.  O valor dessa coluna para a linha será utilizado no valor do link. Isto é comumente usado para permitir que cada linha de uma grelha abra um modelo diferente, definindo `Template Id` o campo para , ou para abrir o mesmo modelo de livro para `column` diferentes recursos, se o `Workbook resources` campo for definido para uma coluna que contém um ID de recursos Azure |
| `Parameter` | Quando selecionado, será apresentado outro campo para permitir que o autor selecione um parâmetro. O valor desse parâmetro será usado para o valor quando o link for clicado |
| `Static value` | Quando selecionado, será exibido outro campo para permitir que o autor escreva um valor estático que será utilizado no livro de trabalhos ligado. Isto é comumente usado quando todas as linhas da grelha usarão o mesmo valor para um campo. |
| `Step` | Utilize o valor definido no passo atual do livro. Isto é comum em passos de consulta e métricas para definir os recursos do livro de trabalho ligado aos *utilizados na etapa de consulta/métricas*, e não no livro de trabalhos em curso |
| `Workbook` | Utilize o valor definido no livro atual. |
| `Default` | Utilize o valor predefinido que seria utilizado se não fosse especificado qualquer valor. Isto é comum para o Tipo de Galeria, onde a galeria padrão seria definida pelo tipo de recurso proprietário |

## <a name="next-steps"></a>Passos seguintes

- [Controle](../visualize/workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
- Saiba como utilizar [grupos em livros de trabalho.](../visualize/workbooks-groups.md)