---
title: Criar relatórios interativos com pastas de trabalho do Azure Monitor | Documentos da Microsoft
description: Simplifica a complexa relatórios com pastas de trabalho parametrizadas predefinidas e personalizadas para o Azure Monitor para as VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288715"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Criar relatórios interativos com pastas de trabalho do Azure Monitor

Pastas de trabalho do texto, de combinar [registar as consultas](../log-query/query-language.md), métricas e parâmetros em relatórios interativos avançados. Pastas de trabalho são editáveis por quaisquer outros membros da Equipe que têm acesso aos mesmos recursos do Azure.

Pastas de trabalho são úteis para cenários, tais como:

* Explorar a utilização da sua máquina virtual, quando não sabe de antemão as métricas de interesse: Utilização da CPU, espaço em disco, memória, as dependências de rede, etc. Ao contrário de outras ferramentas de análise de utilização, as pastas de trabalho permitem-lhe combinar vários tipos de visualizações e análises, tornando-os excelentes para esse tipo de exploração de forma livre.
* Para a sua equipa, que explica como uma VM recentemente aprovisionada está sendo executada, mostrando as métricas dos principais contadores e outros eventos de registo.
* Compartilhamento dos resultados de uma experimentação de redimensionamento da sua VM com outros membros da sua equipa. Pode explicar os objetivos para a experimentação com o texto, em seguida, mostrar cada utilização de consultas de análises e métrica utilizada para avaliar a experimentação, juntamente com claras explicativos para se cada uma foi acima ou abaixo-soltar.
* O impacto de uma indisponibilidade de relatórios sobre a utilização da sua VM, a combinação de dados, a explicação de texto e uma discussão das próximas etapas para evitar interrupções no futuro.

Monitor do Azure para VMs inclui várias pastas de trabalho para começar e a tabela seguinte resume-los.

| Livro | Descrição | Âmbito |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável de nossa lista de N principais e exibição de gráficos numa única pasta de trabalho que aproveita todos os contadores de desempenho do Log Analytics que ativou.| Em escala |
| Contadores de desempenho | Uma vista de gráfico de Top N num grande conjunto de contadores de desempenho. | Em escala |
| Ligações | As ligações fornecem uma visão detalhada das ligações de entrada e saídas suas VMS monitorizadas. | Em escala |
| Portas Ativas | Fornece uma lista dos processos que tenha associado às portas em VMs monitorizadas e da respetiva atividade no período de tempo escolhido. | Em escala |
| Portas Abertas | Fornece o número de portas abertas nas suas VMs monitorizadas e os detalhes nos abrir portas. | Em escala |
| Ligações com Falhas | Mostra a contagem de ligações com falhas nas suas VMs monitorizadas, a tendência de falha, e se a percentagem de falhas está a aumentar ao longo do tempo. | Em escala |
| Segurança e Auditoria | Uma análise do tráfego TCP/IP que relate ligações geral, ligações maliciosas, onde os pontos de extremidade IP residem globalmente.  Para ativar todas as funcionalidades, terá de ativar a deteção de segurança. | Em escala |
| Tráfego TCP | Um relatório classificado para as suas VMs monitorizadas e respetiva rede enviado, recebido e total de tráfego numa grade e apresentados como uma linha de tendência. | Em escala |
| Comparação de Tráfego | Este pastas de trabalho permite-lhe comparar tendências de tráfego de rede para uma única máquina ou um grupo de máquinas. | Em escala |
| Desempenho | Fornece uma versão personalizável de nossa vista de desempenho que aproveita todos os contadores de desempenho do Log Analytics que ativou. | VM única | 
| Ligações | As ligações fornecem uma visão detalhada das ligações de entrada e saídas da VM. | VM única |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Começando com um modelo ou de pasta de trabalho guardada

Uma pasta de trabalho é constituída por secções que consiste de forma independente editáveis gráficos, tabelas, texto e os controles de entrada. Para compreender melhor as pastas de trabalho, vamos começar abrindo um modelo e como criar uma pasta de trabalho personalizada. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **máquinas virtuais**.

3. Na lista, selecione uma VM.

4. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)**.

5. Na página de informações VM, selecione **desempenho** ou **Maps** separador e, em seguida, selecione **pastas de trabalho do modo de exibição** da ligação na página. 

    ![Captura de ecrã de navegação para pastas de trabalho](media/vminsights-workbooks/workbook-option-01.png)

6. Na lista pendente, selecione **vá para a galeria** na parte inferior da lista.

    ![Captura de ecrã da pasta de trabalho na lista pendente](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Ele inicia a Galeria de pasta de trabalho com um número de pastas de trabalho criados previamente para ajudar a começar.

7. Vamos começar com o **modelo predefinido**, que se encontra sob o cabeçalho **guia de introdução**.

    ![Captura de ecrã da Galeria de livro](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Edição de livro de secções

Pastas de trabalho tem dois modos: **modo de edição**, e **modo de leitura**. Quando a pasta de trabalho de modelo padrão é iniciado pela primeira vez, é aberto no **modo de edição**. Mostra todo o conteúdo da pasta de trabalho, inclusive todos os passos e parâmetros que caso contrário, estão ocultas. **Modo de leitura** apresenta uma vista de estilo do relatório simplificada. Modo de leitura permite-lhe abstrair a complexidade que se seguiu à criação de um relatório enquanto ainda tem o mecanismo subjacente apenas alguns cliques de distância quando necessário para modificação.

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando terminar uma seção de edição, clique em **feito editando** no canto inferior esquerdo da secção.

2. Para criar um duplicado de uma seção, clique a **clonar esta secção** ícone. A criação de secções duplicadas é uma ótima maneira de iterar numa consulta sem perder iterações anteriores.

3. Para mover para cima uma seção numa pasta de trabalho, clique nas **mover para cima** ou **mover para baixo** ícone.

4. Para remover permanentemente uma secção, clique a **remover** ícone.

## <a name="adding-text-and-markdown-sections"></a>Adicionar texto e secções do Markdown

Adicionar títulos, explicações e comentários para os livros o ajuda a transformar um conjunto de tabelas e gráficos numa narrativa. Secções de texto no suporte de pastas de trabalho do [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) para formatação de texto, como cabeçalhos, negrito, itálico e listas com marcas.

Para adicionar uma secção de texto ao seu livro, utilize o **adicionar texto** botão na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionar as secções de consulta

![Secção de consulta em pastas de trabalho](media/vminsights-workbooks/005-workbook-query-section.png)

Para adicionar a secção de consulta ao seu livro, utilize o **adicionar consulta** botão na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

As secções de consulta são altamente flexíveis e podem ser utilizadas para responder a perguntas como:

* Como foi a minha utilização da CPU durante o período de tempo mesmo como um aumento no tráfego de rede?
* Qual era a tendência no espaço disponível no disco durante o mês passado?
* Como muitas falhas de ligação de rede a minha VM experiência durante as últimas duas semanas? 

Também não está apenas limitadas a consulta do contexto da máquina virtual iniciada a pasta de trabalho de. Pode consultar em várias máquinas virtuais, como áreas de trabalho do Log Analytics, desde que tem a permissão de acesso a esses recursos.

Para incluir dados de outras áreas de trabalho do Log Analytics ou de um específico Application Insights de aplicação com o **área de trabalho** identificador. Para saber mais sobre consultas entre recursos, consulte a [obter diretrizes oficiais](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Definições de consulta de análise avançadas

Cada secção tem suas próprias definições avançadas, que podem ser acedidas através das definições ![livros secção controles de edição](media/vminsights-workbooks/006-settings.png) ícone localizado à direita do **adicionar parâmetros** botão.

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Faz com que um item um tamanho arbitrário, pelo que pode ajustar o número de itens numa única linha, permitindo-lhe organizar melhor seus gráficos e tabelas em relatórios interativos avançados.  |
| **Condicionalmente visível** | Especifique para ocultar os passos com base num parâmetro quando no modo de leitura. |
| **Exportar um parâmetro**| Permitir que uma linha selecionada na grelha ou gráfico para fazer com que os passos posteriores alterar os valores ou fiquem visíveis.  |
| **Mostrar a consulta durante a edição não** | Apresenta a consulta acima do gráfico ou na tabela, mesmo quando no modo de leitura.
| **Mostrar abrir no botão análise quando não a editar** | Adiciona o ícone de análise azul para o canto direito do gráfico para permitir o acesso de um clique.|

A maioria destas definições são bastante intuitiva, mas entender **exportar um parâmetro** é melhor examinar uma pasta de trabalho que utiliza essa funcionalidade.

Uma das pastas de trabalho criados previamente - **tráfego TCP**, fornece informações sobre as métricas de ligação a partir de uma VM.

A primeira secção da pasta de trabalho baseia-se nos dados de consulta de registo. A segunda seção também se baseia nos dados de consulta de registo, mas a seleção de uma linha na primeira tabela interativamente irá atualizar o conteúdo dos gráficos:

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível através da utilização do **quando um item é selecionado, exportar um parâmetro** definições, o que são ativadas na consulta de registo da tabela avançadas.

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de registo, em seguida, utiliza os valores exportados quando uma linha está selecionada para criar um conjunto de valores que, em seguida, são utilizados pelo cabeçalho de secção e gráficos. Se nenhuma linha for selecionada, ele oculta o cabeçalho de secção e gráficos. 

Por exemplo, o parâmetro oculto a segunda seção utiliza a seguinte referência da linha selecionada na grade:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionar as secções de métricas

As secções de métricas dão-lhe acesso total ao incorporar dados de métricas do Azure Monitor para os relatórios interativos. No Azure Monitor para as VMs, as pastas de trabalho criados previamente, normalmente, irão conter dados de consulta de análise, em vez de dados de métrica.  Pode optar por criar pastas de trabalho com dados de métrica, permitindo-lhe tirar partido das funcionalidades de ambos os tudo num único local. Também tem a capacidade para enviar dados de métrica de recursos em qualquer uma das subscrições que tem acesso.

Eis um exemplo de dados de máquina virtual que está a ser extraídos numa pasta de trabalho para fornecer uma visualização de grade de desempenho de CPU:

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionar parâmetro secções

Parâmetros de pasta de trabalho permitem-lhe alterar valores na pasta de trabalho sem ter de editar manualmente as secções de consulta ou de texto. Isso remove a necessidade de que precisam de compreender a linguagem de consulta do analytics subjacente e expandindo imensamente o público-alvo potencial de relatórios com base no livro.

Os valores dos parâmetros são substituídos na consulta, texto ou outras seções de parâmetro ao colocar o nome do parâmetro entre chavetas, como ``{parameterName}``. Os nomes dos parâmetros estão limitados a regras semelhantes como identificadores de JavaScript, caracteres alfabéticos ou carateres de sublinhado, seguidos por carateres alfanuméricos ou carateres de sublinhado. Por exemplo, **a1** é permitido, mas **1a** não é permitida.

Os parâmetros são lineares, começando do início de uma pasta de trabalho e que flui para baixo para passos posteriores.  Parâmetros declarados posteriormente numa pasta de trabalho podem substituir parâmetros que foram declarados anteriormente. Isso também permite que os parâmetros que usar consultas para acessar os valores de parâmetros definidos anteriormente. No passo de um parâmetro em si, os parâmetros são também lineares, à esquerda para a direita, em que parâmetros para a direita podem depender de um parâmetro que declarei anteriormente nesse mesmo passo.
 
Existem quatro tipos diferentes de parâmetros, que são atualmente suportados:

|                  |      |
| ---------------- |:-----|
| **Text** (Texto)    | Permite ao utilizador editar uma caixa de texto e, opcionalmente, pode fornecer uma consulta para preencher o valor predefinido. |
| **Lista pendente** | Permite ao utilizador escolher a partir de um conjunto de valores. |
| **Seletor de intervalo de tempo**| Permite ao utilizador escolher a partir de um conjunto predefinido de valores de intervalo de tempo ou escolha entre mais de um intervalo de tempo personalizado.|
| **Seleccionador de recursos** | Permite ao utilizador escolher entre os recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Com um parâmetro de texto

O valor de um usuário digitar na caixa de texto é substituído diretamente na consulta, sem carateres de escape ou aspas. Se o valor que precisa é uma cadeia de caracteres, a consulta deve ter o parâmetro entre aspas (como **'{parameter}'**).

O parâmetro de texto permite que o valor numa caixa de texto a ser utilizado em qualquer lugar. Pode ser um nome de tabela, nome da coluna, nome da função, operador, etc.  O tipo de parâmetro de texto tem uma definição **obter o valor predefinido de consulta do analytics**, que permite que o autor do livro para usar uma consulta para preencher o valor predefinido para essa caixa de texto.

Ao utilizar o valor predefinido de uma consulta de registo, apenas o primeiro valor da primeira linha (0 de linha, coluna 0) é utilizado como o valor predefinido. Portanto, é recomendado para limitar a consulta para devolver apenas uma linha e uma coluna. Todos os dados devolvidos pela consulta são ignorados. 

O valor que a consulta devolve será substituído diretamente sem carateres de escape ou aspas. Se a consulta retornaria nenhuma linha, o resultado do parâmetro é uma cadeia vazia (se o parâmetro não for necessário) ou não definida (se o parâmetro é necessário).

### <a name="using-a-drop-down"></a>Utilizar um lista pendente

O tipo de parâmetro de lista pendente permite-lhe criar um controle de lista pendente, que permite a seleção de um ou vários valores.

A lista pendente é preenchido por uma consulta de registo ou JSON. Se a consulta devolve uma coluna, os valores nessa coluna são o valor e a etiqueta no controle de lista pendente. Se a consulta devolve duas colunas, a primeira coluna é o valor e a segunda coluna é a etiqueta mostrada a lista pendente. Se a consulta devolve três colunas, a terceira coluna é utilizada para indicar a seleção predefinida nessa lista suspensa. Esta coluna pode ser qualquer tipo, mas o mais simples é usar bool ou tipos numéricos, em que 0 é false e 1 é verdadeiro.

Se a coluna é um tipo de cadeia de caracteres, cadeia de caracteres nulos/vazios é considerada FALSO e qualquer outro valor é considerado verdadeira. Para seleção única suspensas, o primeiro valor com um valor VERDADEIRO é utilizado como a seleção predefinida.  Para vários seleção mais listas pendentes, todos os valores com um valor VERDADEIRO são utilizados como o conjunto predefinido selecionado. Os itens na lista pendente são apresentados em qualquer ordem, a consulta devolveu linhas. 

Vamos examinar os parâmetros presentes no relatório de descrição geral das ligações. Clique no símbolo de edição junto a **direção**.

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isso iniciará o **Edit Parameter** item de menu.

![O Azure Monitor para a secção de pastas de trabalho de VMs, controles de edição](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite-lhe gerar uma tabela arbitrária preenchida com conteúdo. Por exemplo, o JSON seguinte gera dois valores na lista pendente:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável está a utilizar um lista pendente para escolher a partir de um conjunto de contadores de desempenho por nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta mostra os resultados da seguinte forma:

![Lista pendente de contador de desempenho](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Listas pendentes são incrivelmente poderosas ferramentas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Enquanto pode fazer o seu parâmetro de intervalo de tempo personalizado através do tipo de parâmetro de lista pendente, também pode utilizar o tipo de parâmetro de intervalo de tempo de out-of-box, se não tiver o mesmo grau de flexibilidade. 

Tipos de parâmetros de intervalo de tempo tem 15 intervalos predefinidos que vão de cinco minutos para os últimos 90 dias. Também é uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador do relatório para escolha explícita iniciar e parar valores para o intervalo de tempo.

### <a name="resource-picker"></a>Selecionador de recursos

O tipo de parâmetro de Seletor de recurso dá-lhe a capacidade de definir o âmbito do relatório para determinados tipos de recursos. Um exemplo de uma pasta de trabalho pré-criados que aproveita o tipo de Seletor de recurso é o **desempenho** pasta de trabalho.

![Lista pendente de áreas de trabalho](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>A guardar e partilhar pastas de trabalho com a sua equipa

Pastas de trabalho são salvas dentro de uma área de trabalho do Log Analytics ou um recurso de máquina virtual, dependendo de como acessa a Galeria de pastas de trabalho. A pasta de trabalho pode ser guardada para o **os meus relatórios** secção é privada para ou na **relatórios partilhados** secção que esteja acessível para todos os utilizadores com acesso ao recurso. Para ver todas as pastas de trabalho no recurso, clique a **aberto** botão na barra de ação.

Para partilhar uma pasta de trabalho que se encontra atualmente num **os meus relatórios**:

1. Clique em **aberto** na barra de ação
2. Clique no botão "..." ao lado da pasta de trabalho que pretende partilhar
3. Clique em **mover para relatórios partilhados**.

Para partilhar uma pasta de trabalho com uma ligação ou através de e-mail, clique em **partilhar** na barra de ação. Tenha em atenção que os destinatários da ligação precisam de acesso a este recurso no portal do Azure para ver a pasta de trabalho. Para fazer edições, os destinatários precisam, pelo menos, permissões de Contribuidor para o recurso.

Para afixar uma ligação para uma pasta de trabalho a um Dashboard do Azure:

1. Clique em **aberto** na barra de ação
2. Clique no botão "..." ao lado da pasta de trabalho que pretende afixar
3. Clique em **afixar ao dashboard**.

## <a name="next-steps"></a>Passos Seguintes
Para saber como utilizar a funcionalidade de estado de funcionamento, veja [Ver estado de funcionamento do Azure VM](vminsights-health.md), ou para ver dependências de aplicações detetadas, consulte [vista de Azure Monitor para o mapa de VMs](vminsights-maps.md). 