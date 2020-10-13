---
title: Criar relatórios interativos do Azure Monitor para VMs com livros
description: Simplificar relatórios complexos com livros de trabalho parametrizados predefinidos e personalizados para o Azure Monitor para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 8ae8c633bdfca72d6e383715cac9b1bcdddd9d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449790"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Criar relatórios interativos do Azure Monitor para VMs com livros

Os livros combinam texto, [consultas de registo,](../log-query/query-language.md)métricas e parâmetros em ricos relatórios interativos. Os livros de trabalho são editáveis por quaisquer outros membros da equipa que tenham acesso aos mesmos recursos Azure.

Os livros de trabalho são úteis para cenários como:

* Explorar o uso da sua máquina virtual quando não conhece as métricas de interesse com antecedência: utilização de CPU, espaço em disco, memória, dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, os livros permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para este tipo de exploração de forma livre.
* Explicando à sua equipa como está a funcionar um VM recentemente aprovisionado, mostrando métricas para contadores-chave e outros eventos de registo.
* Partilhar os resultados de uma experiência de redimensionamento do seu VM com outros membros da sua equipa. Você pode explicar os objetivos para a experiência com texto, em seguida, mostrar cada métrica de uso e consultas de análise usadas para avaliar a experiência, juntamente com chamadas claras para se cada métrica estava acima ou abaixo do alvo.
* Relatar o impacto de uma paragem no uso do seu VM, combinando dados, explicação de texto, e uma discussão dos próximos passos para evitar interrupções no futuro.

A tabela que se segue resume os livros que o Azure Monitor para VMs inclui para começar.

| Livro | Descrição | Âmbito |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável da nossa visão top N List e Charts num único livro que aproveita todos os contadores de desempenho do Log Analytics que ativou.| Em escala |
| Contadores de desempenho | Uma vista de gráfico top N através de um vasto conjunto de contadores de desempenho. | Em escala |
| Ligações | As ligações proporcionam uma visão aprofundada das ligações de entrada e saída dos seus VMs monitorizados. | Em escala |
| Portas Ativas | Fornece uma lista dos processos que têm ligado às portas nos VMs monitorizados e sua atividade no prazo escolhido. | Em escala |
| Portas Abertas | Fornece o número de portas abertas nos seus VMs monitorizados e os detalhes sobre as portas abertas. | Em escala |
| Ligações com Falhas | Exiba a contagem de ligações falhadas nos seus VMs monitorizados, a tendência de falha, e se a percentagem de falhas estiver a aumentar ao longo do tempo. | Em escala |
| Segurança e Auditoria | Uma análise do tráfego TCP/IP que reporta sobre ligações globais, ligações maliciosas, onde os pontos finais ip residem globalmente.  Para ativar todas as funcionalidades, terá de ativar a Deteção de Segurança. | Em escala |
| Tráfego TCP | Um relatório classificado para os seus VMs monitorizados e o seu tráfego de rede enviado, recebido e total numa rede e apresentado como uma linha de tendência. | Em escala |
| Comparação de Tráfego | Estes livros permitem comparar as tendências de tráfego de rede de uma única máquina ou de um grupo de máquinas. | Em escala |
| Desempenho | Fornece uma versão personalizável da nossa visão Performance que aproveita todos os contadores de desempenho do Log Analytics que ativou. | VM única | 
| Ligações | As ligações proporcionam uma visão aprofundada das ligações de entrada e saída do seu VM. | VM única |
 
## <a name="creating-a-new-workbook"></a>Criação de um novo livro

Um livro é composto por secções compostas por gráficos, tabelas, texto e controlos de entrada independentes. Para melhor entender os livros de trabalho, vamos começar por abrir um modelo e caminhar através da criação de um livro personalizado. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Máquinas Virtuais.**

3. Na lista, selecione uma VM.

4. Na página VM, na secção **de Monitorização,** selecione **Insights**.

5. Na página de insights VM, selecione **o separador Performance** ou **Maps** e, em seguida, selecione Ver Livros de **Trabalho** a partir do link na página. Da lista de drop-down, selecione **Ir à Galeria**.

    ![Screenshot da lista de drop-down do livro](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Isto lança a galeria de livros com uma série de livros pré-construídos para ajudá-lo a começar.

7. Crie um novo livro selecionando **New**.

    ![Screenshot da galeria de livros](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Editar secções do livro

Os livros de trabalho têm dois modos: **modo de edição**e **modo de leitura**. Quando um novo livro é lançado pela primeira vez, abre-se no **modo de edição**. Mostra todo o conteúdo do livro, incluindo quaisquer passos e parâmetros que sejam escondidos de outra forma. **O modo de leitura** apresenta uma visão simplificada do estilo de relatório. O modo de leitura permite-lhe abstrair a complexidade que foi criar um relatório, ao mesmo tempo que ainda tem a mecânica subjacente a apenas alguns cliques de distância quando necessário para modificação.

![Screenshot da secção de workbook das máquinas virtuais no Azure Monitor mostrando um novo livro no modo de edição com os controlos de edição em destaque.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando terminar de editar uma secção, clique em **Edição Feita** no canto inferior esquerdo da secção.

2. Para criar uma duplicação de uma secção, clique no ícone **de secção Clone.** Criar secções duplicadas é um ótimo caminho para iterar numa consulta sem perder iterações anteriores.

3. Para subir uma secção num livro, clique no ícone **Move up** ou **Move down.**

4. Para remover uma secção permanentemente, clique no ícone **Remover.**

## <a name="adding-text-and-markdown-sections"></a>Adicionar secções de texto e Markdown

Adicionar títulos, explicações e comentários aos seus livros ajuda a transformar um conjunto de tabelas e gráficos numa narrativa. Secções de texto em livros suportam a [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) para formatação de texto, como títulos, ousados, itálicos e listas de balas.

Para adicionar uma secção de texto ao seu livro, utilize o botão **de texto Adicionar** na parte inferior do livro ou na parte inferior de qualquer secção.

## <a name="adding-query-sections"></a>Adicionar secções de consulta

![Secção de consulta em livros de trabalho](media/vminsights-workbooks/005-workbook-query-section.png)

Para adicionar a secção de consulta ao seu livro, utilize o botão **de consulta Adicionar** na parte inferior do livro, ou na parte inferior de qualquer secção.

As secções de consulta são altamente flexíveis e podem ser usadas para responder a questões como:

* Como foi a utilização do meu CPU durante o mesmo período de tempo que um aumento do tráfego de rede?
* Qual foi a tendência no espaço disponível em disco ao longo do último mês?
* Quantas falhas na ligação à rede fizeram a minha experiência em VM nas últimas duas semanas? 

Também não se limita apenas a consultar o contexto da máquina virtual a que lançou o livro. Você pode consultar várias máquinas virtuais, bem como espaços de trabalho Log Analytics, desde que você tenha permissão de acesso a esses recursos.

Para incluir dados de outros espaços de trabalho do Log Analytics ou de uma aplicação específica de Insights de Aplicação utilizando o identificador **do espaço de trabalho.** Para saber mais sobre consultas de recursos cruzados, consulte a [orientação oficial.](../log-query/cross-workspace-query.md)

### <a name="advanced-analytic-query-settings"></a>Definições avançadas de consulta analítica

Cada secção tem as suas próprias definições avançadas, que são acessíveis através das definições ![ A secção de edição de livros de trabalho controla ](media/vminsights-workbooks/006-settings.png) o ícone localizado à direita do botão **De adicionar parâmetros.**

![Screenshot do diálogo de Definições Avançadas na secção de livro de máquinas virtuais do Monitor Azure. Destaca-se o ícone que abre o diálogo.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Faz um item de tamanho arbitrário, para que possa encaixar muitos itens numa única linha, permitindo-lhe organizar melhor os seus gráficos e tabelas em relatórios interativos ricos.  |
| **Condicionalmente visível** | Especifique para ocultar passos com base num parâmetro quando estiver no modo de leitura. |
| **Exportar um parâmetro**| Deixe uma linha selecionada na grelha ou na tabela para fazer com que os passos posteriores alterem valores ou se tornem visíveis.  |
| **Mostrar consulta quando não editar** | Exibe a consulta acima do gráfico ou da tabela mesmo quando está no modo de leitura.
| **Mostre-se aberto no botão de análise quando não editar** | Adiciona o ícone analítico azul ao canto direito da tabela para permitir o acesso de um clique.|

A maioria destas configurações são bastante intuitivas, mas para entender **a Exportação um parâmetro** é melhor examinar um livro que faça uso desta funcionalidade.

Um dos livros pré-construídos - **Tráfego TCP**, fornece informações sobre métricas de ligação de um VM.

A primeira secção do livro baseia-se em dados de consulta de registo. A segunda secção também é baseada em dados de consulta de registo, mas selecionar uma linha na primeira tabela irá atualizar interativamente o conteúdo dos gráficos:

![Screenshot da secção máquinas virtuais no Monitor Azure mostrando o tráfego TCP do livro pré-construído.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível através da utilização do **artigo Quando um item é selecionado, exportar um parâmetro** de definições avançadas, que são ativadas na consulta de log da tabela.

![Screenshot do diálogo de Definições Avançadas para um livro de máquinas virtuais com a opção "Quando um item é selecionado, exporte um parâmetro" verificado.](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de registo utiliza então os valores exportados quando uma linha é selecionada para criar um conjunto de valores que são depois utilizados pela posição e gráficos da secção. Se não for selecionada nenhuma linha, oculta o título e os gráficos da secção. 

Por exemplo, o parâmetro oculto na segunda secção utiliza a seguinte referência da linha selecionada na grelha:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionar secções de métricas

As secções de métricas dão-lhe acesso total para incorporar dados de métricas do Azure Monitor nos seus relatórios interativos. No Azure Monitor para VMs, os livros pré-construídos normalmente contêm dados de consulta analítica em vez de dados métricos.  Pode optar por criar livros com dados métricos, permitindo-lhe tirar o máximo partido do melhor de ambas as funcionalidades num só local. Também tem a capacidade de extrair dados métricos de recursos em qualquer uma das subscrições a que tem acesso.

Aqui está um exemplo de dados de máquinas virtuais sendo puxados para um livro para fornecer uma visualização de grelha do desempenho do CPU:

![Screenshot da secção de métricas de um livro de máquinas virtuais no Azure Monitor. O desempenho do CPU para cada máquina virtual é mostrado graficamente.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionar secções de parâmetros

Os parâmetros do livro permitem alterar valores no livro sem ter de editar manualmente as secções de consulta ou texto. Isto elimina a exigência de necessidade de compreender a linguagem de consulta analítica subjacente e expande consideravelmente o público potencial de relatórios baseados em livros de trabalho.

Os valores dos parâmetros são substituídos em secções de consulta, texto ou outros parâmetros, colocando o nome do parâmetro em aparelhos, como ``{parameterName}`` . Os nomes dos parâmetros são limitados a regras semelhantes às de JavaScript identificadores, caracteres alfabéticos ou sublinhados, seguidos por caracteres alfanuméricos ou sublinhados. Por exemplo, **a A1** é permitida, mas **1a** não é permitido.

Os parâmetros são lineares, partindo do topo de um livro e fluindo para os degraus posteriores.  Os parâmetros declarados mais tarde num livro podem sobrepor-se aos parâmetros que foram declarados anteriormente. Isto também permite que os parâmetros que utilizam consultas para aceder aos valores a partir de parâmetros definidos anteriormente. Dentro do passo de um parâmetro em si, os parâmetros também são lineares, da esquerda para a direita, onde os parâmetros à direita podem depender de um parâmetro declarado anteriormente nesse mesmo passo.
 
Existem quatro tipos diferentes de parâmetros, que são atualmente suportados:

|                  |      |
| ---------------- |:-----|
| **Texto**    | Permite ao utilizador editar uma caixa de texto, e pode opcionalmente fornecer uma consulta para preencher o valor predefinido. |
| **Pendente** | Permite ao utilizador escolher entre um conjunto de valores. |
| **Selecionador de intervalo de tempo**| Permite ao utilizador escolher entre um conjunto predefinido de valores de intervalo de tempo, ou escolher de um intervalo de tempo personalizado.|
| **Apanhador de recursos** | Permite ao utilizador escolher entre os recursos selecionados para o livro.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um utilizador escreve na caixa de texto é substituído diretamente na consulta, sem escapatória ou citação. Se o valor necessário for uma corda, a consulta deve ter citações em torno do parâmetro (como **'{parâmetro}'**).

O parâmetro de texto permite que o valor numa caixa de texto seja utilizado em qualquer lugar. Pode ser um nome de mesa, nome de coluna, nome da função, operador, etc.  O tipo de parâmetro de texto tem um **valor predefinido Obter valor predefinido da consulta de análise,** o que permite ao autor do livro usar uma consulta para preencher o valor predefinido para essa caixa de texto.

Ao utilizar o valor predefinido a partir de uma consulta de registo, apenas o primeiro valor da primeira linha (linha 0, coluna 0) é usado como valor predefinido. Por isso, recomenda-se limitar a sua consulta a devolver apenas uma linha e uma coluna. Quaisquer outros dados devolvidos pela consulta são ignorados. 

Qualquer valor que a consulta devolva será substituído diretamente sem escapatória ou citação. Se a consulta não retornar linhas, o resultado do parâmetro é uma corda vazia (se o parâmetro não for necessário) ou indefinida (se for necessário o parâmetro).

### <a name="using-a-drop-down"></a>Usando um drop-down

O tipo de parâmetro de queda permite criar um controlo de queda, permitindo a seleção de um ou muitos valores.

O dropdown é povoado por uma consulta de log ou JSON. Se a consulta devolver uma coluna, os valores nessa coluna são tanto o valor como o rótulo no controlo de queda. Se a consulta devolver duas colunas, a primeira coluna é o valor, e a segunda coluna é a etiqueta mostrada no drop-down. Se a consulta retornar três colunas, a terceira coluna é utilizada para indicar a seleção predefinidora nessa queda. Esta coluna pode ser qualquer tipo, mas o mais simples é usar tipos bool ou numéricos, onde 0 é falso, e 1 é verdade.

Se a coluna for um tipo de corda, a corda nula/vazia é considerada falsa, e qualquer outro valor é considerado verdadeiro. Para as reduções de seleção única, o primeiro valor com um valor verdadeiro é usado como seleção padrão.  Para várias reduções de seleção, todos os valores com um valor verdadeiro são utilizados como o conjunto selecionado por defeito. Os itens na entrega são mostrados em qualquer ordem que a consulta devolva as linhas. 

Vejamos os parâmetros presentes no relatório "Connections Overview". Clique no símbolo de edição ao lado **da Direção**.

![Screenshot da secção para adicionar e editar parâmetros de relatório no Azure Monitor. É selecionado o ícone editar para o parâmetro Direction.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isto lançará o item do menu **Edito Para parâmetro.**

![Screenshot do diálogo do parâmetro de edição. O nome do parâmetro é Direção, o tipo de parâmetro é Drop down, e Obter dados de JSON é selecionado.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite-lhe gerar uma tabela arbitrária povoada de conteúdo. Por exemplo, o seguinte JSON gera dois valores na queda:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável é usar um drop-down para escolher a partir de um conjunto de contadores de desempenho pelo nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta apresentará os resultados da seguinte forma:

![Recuo do contador perf](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

As entregas são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros do intervalo de tempo

Embora possa fazer o seu próprio parâmetro de intervalo de tempo personalizado através do tipo de parâmetro de dropdown, também pode utilizar o tipo de parâmetro de intervalo de tempo fora da caixa se não precisar do mesmo grau de flexibilidade. 

Os parâmetros de intervalo de tempo têm 15 intervalos predefinidos que vão de cinco minutos aos últimos 90 dias. Existe também uma opção para permitir a seleção do intervalo de tempo personalizado, que permite ao operador do relatório escolher valores explícitos de início e paragem para o intervalo de tempo.

### <a name="resource-picker"></a>Apanhador de recursos

O tipo de parâmetro de picker de recursos dá-lhe a capacidade de estender o seu relatório a certos tipos de recursos. Um exemplo de um livro pré-construído que aproveita o tipo de apanhador de recursos é o livro de **performance.**

![Espaço de trabalho em queda](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Guardar e partilhar livros com a equipa

Os livros são guardados dentro de um espaço de trabalho do Log Analytics ou de uma máquina virtual, dependendo da forma como acede à galeria de livros. O livro pode ser guardado na secção **My Reports** que é privada para si ou na secção **Relatórios Partilhados** que é acessível a todos os que tenham acesso ao recurso. Para visualizar todos os livros de trabalho no recurso, clique no botão **Abrir** na barra de ação.

Para partilhar um livro que está atualmente no **My Reports:**

1. Clique em **abrir** na barra de ação
2. Clique no "..." botão ao lado do livro que quer partilhar
3. Clique **em Mover para Relatórios Partilhados.**

Para partilhar um livro com um link ou via e-mail, clique em **Partilhar** na barra de ação. Tenha em mente que os destinatários do link precisam de acesso a este recurso no portal Azure para visualizar o livro. Para edição, os destinatários precisam de pelo menos permissões do Contribuinte para o recurso.

Para fixar uma ligação a um livro a um painel de instrumentos Azure:

1. Clique em **abrir** na barra de ação
2. Clique no "..." botão ao lado do livro que quer fixar
3. Clique em Pin para painel de **instrumentos**.

## <a name="next-steps"></a>Passos seguintes

- Para identificar limitações e desempenho geral do VM, consulte [Ver Azure VM Performance](vminsights-performance.md).

- Para saber mais sobre as dependências de aplicações descobertas, consulte [o Monitor Do Azure para o Mapa de VMs.](vminsights-maps.md)
