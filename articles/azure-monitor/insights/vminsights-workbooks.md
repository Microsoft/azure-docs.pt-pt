---
title: Criar relatórios interativos do Azure Monitor para VMs com livros
description: Simplificar relatórios complexos com livros parametrizados pré-definidos e personalizados para O Monitor Azure para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480458"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Criar relatórios interativos do Azure Monitor para VMs com livros

Os livros de trabalho combinam texto, consultas de [registo,](../log-query/query-language.md)métricas e parâmetros em relatórios interativos ricos. Os livros de recção são editáveis por quaisquer outros membros da equipa que tenham acesso aos mesmos recursos Do Azure.

Os livros são úteis para cenários como:

* Explorar o uso da sua máquina virtual quando não conhece antecipadamente as métricas de interesse: utilização de CPU, espaço de disco, memória, dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, os livros de livros permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para este tipo de exploração em forma livre.
* Explicando à sua equipa como está a funcionar um VM recentemente provisionado, mostrando métricas para contadores-chave e outros eventos de log.
* Partilhar os resultados de uma experiência de redimensionamento do seu VM com outros membros da sua equipa. Você pode explicar os objetivos para a experiência com texto, em seguida, mostrar cada métrica de uso e consultas de análise usadas para avaliar a experiência, juntamente com chamadas claras para se cada métrica estava acima ou abaixo do alvo.
* Reportar o impacto de uma interrupção no uso do seu VM, combinando dados, explicação de texto e uma discussão sobre os próximos passos para evitar interrupções no futuro.

A tabela que se segue resume os livros que o Azure Monitor para VMs inclui para começar.

| Livro | Descrição | Âmbito |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável da nossa visão top n list e gráficos num único livro que aproveita todos os contadores de desempenho do Log Analytics que você ativou.| À escala |
| Contadores de desempenho | Uma vista de gráfico top N através de um vasto conjunto de contadores de desempenho. | À escala |
| Ligações | As ligações proporcionam uma visão aprofundada das ligações de entrada e saída dos seus VMs monitorizados. | À escala |
| Portas Ativas | Fornece uma lista dos processos que se vincularam às portas nos VMs monitorizados e à sua atividade no prazo escolhido. | À escala |
| Portas Abertas | Fornece o número de portas abertas nos seus VMs monitorizados e os detalhes sobre essas portas abertas. | À escala |
| Ligações com Falhas | Mostre a contagem de ligações falhadas nos seus VMs monitorizados, a tendência de falha e se a percentagem de falhas estiver a aumentar ao longo do tempo. | À escala |
| Segurança e Auditoria | Uma análise do seu tráfego TCP/IP que reporta sobre ligações globais, conexões maliciosas, onde os pontos finais ip residem globalmente.  Para ativar todas as funcionalidades, terá de ativar a Deteção de Segurança. | À escala |
| Tráfego TCP | Um relatório classificado para os seus VMs monitorizados e os seus vms enviados, recebidos e tráfego total de rede numa rede e apresentado como uma linha de tendência. | À escala |
| Comparação de Tráfego | Estes livros permitem comparar as tendências de tráfego de rede para uma única máquina ou um grupo de máquinas. | À escala |
| Desempenho | Fornece uma versão personalizável da nossa visão de Desempenho que aproveita todos os contadores de desempenho do Log Analytics que você ativou. | VM única | 
| Ligações | As ligações proporcionam uma visão aprofundada das ligações de entrada e saída do seu VM. | VM única |
 
## <a name="creating-a-new-workbook"></a>Criação de um novo livro

Um livro é composto por secções compostas por gráficos, tabelas, texto e controlos de entrada independentes. Para entender melhor os livros, vamos começar por abrir um modelo e caminhar através da criação de um livro personalizado. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Selecione **Máquinas Virtuais**.

3. Na lista, selecione uma VM.

4. Na página VM, na secção **monitora,** selecione **Insights**.

5. Na página de insights vM, selecione o separador **Performance** ou **Maps** e, em seguida, selecione **Ver Livros** de Trabalho a partir do link na página. A partir da lista de lançamentos, selecione **Ir à Galeria**.

    ![Screenshot da lista de drop-down do livro](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Isto lança a galeria do livro com uma série de livros pré-construídos para ajudá-lo a começar.

7. Crie um novo livro selecionando **New**.

    ![Screenshot da galeria do livro](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Editar secções do livro

Os livros têm dois modos: **modo de edição**, e **modo de leitura**. Quando um novo livro é lançado, abre em modo de **edição**. Mostra todo o conteúdo do livro, incluindo quaisquer passos e parâmetros que estejam escondidos de outra forma. **O modo de leitura** apresenta uma visão simplificada do estilo do relatório. O modo de leitura permite-lhe abstrair a complexidade que entrou na criação de um relatório, ao mesmo tempo que ainda tem a mecânica subjacente a poucos cliques de distância quando necessário para modificação.

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando terminar de editar uma secção, clique em **Edição Feita** no canto inferior esquerdo da secção.

2. Para criar uma duplicação de uma secção, clique no **ícone clone desta secção.** Criar secções duplicadas é um ótimo caminho para iterar numa consulta sem perder iterações anteriores.

3. Para subir uma secção num livro, clique no ícone **Mover para cima** ou **mover-se para baixo.**

4. Para remover uma secção permanentemente, clique no ícone **Remover.**

## <a name="adding-text-and-markdown-sections"></a>Adicionar secções de texto e Markdown

Adicionar títulos, explicações e comentários aos seus livros ajuda a transformar um conjunto de tabelas e gráficos numa narrativa. As secções de texto nos livros de livros suportam a [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) para formatação de texto, como títulos, ousados, itálicos e listas com balas.

Para adicionar uma secção de texto ao seu livro de trabalho, utilize o botão **Adicionar** texto na parte inferior do livro ou na parte inferior de qualquer secção.

## <a name="adding-query-sections"></a>Adicionar secções de consulta

![Secção de consulta nos Livros de Trabalho](media/vminsights-workbooks/005-workbook-query-section.png)

Para adicionar a secção de consulta ao seu livro de trabalho, use o botão **adicionar** consulta na parte inferior do livro de trabalho, ou na parte inferior de qualquer secção.

As secções de consulta são altamente flexíveis e podem ser usadas para responder a questões como:

* Como foi a minha utilização da CPU durante o mesmo período de tempo que o aumento do tráfego de rede?
* Qual foi a tendência no espaço de disco disponível no último mês?
* Quantas falhas de ligação à rede experimentaram a minha experiência vm nas últimas duas semanas? 

Também não se limita apenas a consultar o contexto da máquina virtual de onde lançou o livro de trabalho. Você pode consultar várias máquinas virtuais, bem como espaços de trabalho Log Analytics, desde que tenha permissão de acesso a esses recursos.

Para incluir dados de outros espaços de trabalho do Log Analytics ou de uma aplicação específica de Application Insights utilizando o identificador espaço de **trabalho.** Para saber mais sobre consultas de recursos cruzados, consulte a [orientação oficial](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Definições avançadas de consulta analítica

Cada secção tem as suas próprias definições avançadas, que são acessíveis através das definições ![Listas de trabalho, ícone de controlos](media/vminsights-workbooks/006-settings.png) de edição localizado à direita do botão Adicionar **parâmetros.**

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Faz um item de tamanho arbitrário, para que possa encaixar muitos itens numa única linha, permitindo-lhe organizar melhor as suas tabelas e tabelas em relatórios interativos ricos.  |
| **Condicionalmente visível** | Especifique para ocultar passos com base num parâmetro quando estiver em modo de leitura. |
| **Exportar um parâmetro**| Permita que uma linha selecionada na grelha ou gráfico faça com que passos posteriores alterem valores ou se tornem visíveis.  |
| **Mostrar consulta ao não editar** | Exibe a consulta acima do gráfico ou da tabela mesmo quando está em modo de leitura.
| **Mostrar aberto no botão de análise ao não editar** | Adiciona o ícone Azul Analytics ao canto direito da tabela para permitir um clique de acesso.|

A maioria destas configurações são bastante intuitivas, mas para entender **Exportar um parâmetro** é melhor examinar um livro que faz uso desta funcionalidade.

Um dos livros pré-construídos - **TCP Traffic**, fornece informações sobre métricas de ligação a partir de um VM.

A primeira secção do livro baseia-se em dados de consulta de registo. A segunda secção baseia-se também em dados de consulta de registo, mas a seleção de uma linha na primeira tabela irá atualizar interativamente o conteúdo dos gráficos:

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível através da utilização do **When a item is selected, exportar um parâmetro** de definições avançadas, que estão ativadas na consulta de registo da tabela.

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de registo utiliza então os valores exportados quando uma linha é selecionada para criar um conjunto de valores que são depois utilizados pela rubrica e gráficos da secção. Se não for selecionada nenhuma linha, esconde a direção e os gráficos da secção. 

Por exemplo, o parâmetro oculto na segunda secção utiliza a seguinte referência da linha selecionada na grelha:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionar secções de métricas

As secções de métricas dão-lhe acesso total para incorporar dados de métricas do Monitor Azure nos seus relatórios interativos. No Monitor Azure para VMs, os livros pré-construídos normalmente contêm dados de consulta analítica em vez de dados métricos.  Pode optar por criar livros com dados métricos, permitindo-lhe tirar o máximo partido do melhor de ambas as funcionalidades num só local. Também tem a capacidade de retirar dados métricos de recursos em qualquer uma das subscrições a que tem acesso.

Aqui está um exemplo de dados de máquinas virtuais sendo puxados para um livro para fornecer uma visualização da grelha do desempenho do CPU:

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionar secções de parâmetros

Os parâmetros do livro permitem alterar valores no livro sem ter de editar manualmente as secções de consulta ou texto. Isto elimina a exigência de necessidade de compreender a linguagem de consulta de análise subjacente e expande consideravelmente o público potencial de relatórios baseados em livros.

Os valores dos parâmetros são substituídos em secções de consulta, texto ou outros ``{parameterName}``parâmetros colocando o nome do parâmetro em aparelhos, como . Os nomes dos parâmetros limitam-se a regras semelhantes às dos identificadores JavaScript, caracteres alfabéticos ou sublinhados, seguidos por caracteres alfanuméricos ou sublinhados. Por exemplo, a **A1** é permitida, mas **1a** não é permitida.

Os parâmetros são lineares, partindo do topo de um livro e fluindo para passos posteriores.  Os parâmetros declarados posteriormente num livro podem sobrepor-se a parâmetros que foram declarados anteriormente. Isto também permite parâmetros que usam consultas para aceder aos valores a partir de parâmetros definidos anteriormente. Dentro do próprio passo de um parâmetro, os parâmetros também são lineares, da esquerda para a direita, onde os parâmetros à direita podem depender de um parâmetro declarado anteriormente nesse mesmo passo.
 
Existem quatro tipos diferentes de parâmetros, que são atualmente suportados:

|                  |      |
| ---------------- |:-----|
| **Texto**    | Permite ao utilizador editar uma caixa de texto, e pode facultar opcionalmente uma consulta para preencher o valor predefinido. |
| **Pendente** | Permite ao utilizador escolher entre um conjunto de valores. |
| **Picker de intervalo de tempo**| Permite ao utilizador escolher entre um conjunto predefinido de valores de intervalo de tempo, ou escolher a partir de um intervalo de tempo personalizado.|
| **Picker de recursos** | Permite ao utilizador escolher entre os recursos selecionados para o livro.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um utilizador pode escrever na caixa de texto é substituído diretamente na consulta, sem escapar ou citar. Se o valor que precisa é de uma corda, a consulta deve ter citações em torno do parâmetro (como **'{parâmetro}'**).

O parâmetro de texto permite que o valor numa caixa de texto seja utilizado em qualquer lugar. Pode ser um nome de mesa, nome de coluna, nome de função, operador, etc.  O tipo de parâmetro de texto tem uma definição **Obter valor padrão a partir de consulta de análise**, que permite ao autor do livro usar uma consulta para preencher o valor padrão para essa caixa de texto.

Ao utilizar o valor predefinido a partir de uma consulta de registo, apenas o primeiro valor da primeira linha (linha 0, coluna 0) é usado como valor predefinido. Por isso, recomenda-se limitar a sua consulta para devolver apenas uma linha e uma coluna. Quaisquer outros dados devolvidos pela consulta são ignorados. 

Qualquer valor que a consulta devoluções seja substituído diretamente sem escapar ou citar. Se a consulta não devolver linhas, o resultado do parâmetro é uma corda vazia (se o parâmetro não for necessário) ou indefinida (se for necessário o parâmetro).

### <a name="using-a-drop-down"></a>Usando uma queda

O tipo de parâmetro de queda permite criar um controlo de drop-down, permitindo a seleção de um ou muitos valores.

A queda é povoada por uma consulta de log ou JSON. Se a consulta devolver uma coluna, os valores nessa coluna são tanto o valor como a etiqueta no controlo de queda. Se a consulta devolver duas colunas, a primeira coluna é o valor, e a segunda coluna é a etiqueta mostrada na gota para baixo. Se a consulta devolver três colunas, a terceira coluna é utilizada para indicar a seleção predefinida nessa queda. Esta coluna pode ser qualquer tipo, mas o mais simples é usar tipos bool ou numéricos, onde 0 é falso, e 1 é verdade.

Se a coluna for do tipo de corda, a corda nula/vazia é considerada falsa, e qualquer outro valor é considerado verdadeiro. Para uma única seleção, o primeiro valor com um valor real é usado como seleção padrão.  Para várias descidas de seleção, todos os valores com um valor real são usados como o conjunto selecionado por padrão. Os itens na entrega são mostrados em qualquer ordem que a consulta devolveu linhas. 

Vejamos os parâmetros presentes no relatório de visão geral das ligações. Clique no símbolo de edição ao lado da **Direção**.

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isto lançará o item do menu **Edit Parameter.**

![Monitor Azure para vms Comandos de edição de livros de trabalho](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite-lhe gerar uma mesa arbitrária povoada de conteúdo. Por exemplo, o seguinte JSON gera dois valores na queda:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável é usar uma gota para escolher de um conjunto de contadores de desempenho pelo nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta apresentará os resultados da seguinte forma:

![Perf counter dropdown](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

As quedas são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora possa fazer o seu próprio parâmetro de intervalo de tempo personalizado através do tipo de parâmetro de dropdown, também pode utilizar o tipo de parâmetro de intervalo de tempo fora da caixa se não precisar do mesmo grau de flexibilidade. 

Os tipos de parâmetros de intervalo de tempo têm 15 gamas padrão que vão de cinco minutos para os últimos 90 dias. Existe também uma opção para permitir a seleção personalizada do intervalo de tempo, que permite ao operador do relatório escolher valores explícitos de início e paragem para o intervalo de tempo.

### <a name="resource-picker"></a>Picker de recursos

O tipo de parâmetro de recolha de recursos dá-lhe a capacidade de examinar o seu relatório a certos tipos de recursos. Um exemplo de um livro pré-construído que aproveita o tipo de picker de recursos é o livro **de performance.**

![Abandono de espaços de trabalho](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Guardar e partilhar livros com a equipa

Os livros de trabalho são guardados dentro de um Espaço de Trabalho de Log Analytics ou de um recurso virtual de máquina, dependendo da forma como acede à galeria de livros de livros. O livro pode ser guardado na secção **My Reports** que é privada para si ou na secção **Relatórios Partilhados** que é acessível a todos os que tenham acesso ao recurso. Para ver todos os livros de reposição do recurso, clique no botão **Open** na barra de ação.

Para partilhar um livro que está atualmente em **My Reports:**

1. Clique em **Abrir** na barra de ação
2. Clique no "..." botão ao lado do livro que você quer partilhar
3. Clique em **mover-se para relatórios partilhados**.

Para partilhar um livro com um link ou via e-mail, clique em **Partilhar** na barra de ação. Tenha em mente que os destinatários do link precisam de ter acesso a este recurso no portal Azure para visualizar o livro de trabalho. Para edição, os destinatários precisam de pelo menos permissões contributivas para o recurso.

Para fixar um link para um livro de trabalho a um Painel De Instrumentos Azure:

1. Clique em **Abrir** na barra de ação
2. Clique no "..." botão ao lado do livro que você quer fixar
3. Clique **em Pin para painel de instrumentos**.

## <a name="next-steps"></a>Passos seguintes

- Para identificar limitações e desempenho geral de VM, consulte [View Azure VM Performance](vminsights-performance.md).

- Para saber sobre dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).
