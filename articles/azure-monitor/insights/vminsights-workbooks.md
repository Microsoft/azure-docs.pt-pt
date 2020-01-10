---
title: Criar relatórios interativos Azure Monitor para VMs com pastas de trabalho | Microsoft Docs
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas para Azure Monitor para VMs.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: b89e61f0c268df0ed6dd69ae1c6d97f00678fc32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365925"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Criar relatórios interativos Azure Monitor para VMs com pastas de trabalho

As pastas de trabalho combinam texto, [consultas de log](../log-query/query-language.md), métricas e parâmetros em relatórios interativos sofisticados. As pastas de trabalho são editáveis por outros membros da equipe que têm acesso aos mesmos recursos do Azure.

As pastas de trabalho são úteis para cenários como:

* Explorando o uso de sua máquina virtual quando você não conhece as métricas de interesse com antecedência: utilização da CPU, espaço em disco, memória, dependências de rede etc. Ao contrário de outras ferramentas de análise de uso, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
* Explicando à sua equipe como uma VM provisionada recentemente está sendo executada, mostrando métricas para contadores-chave e outros eventos de log.
* Compartilhando os resultados de um teste de redimensionamento de sua VM com outros membros de sua equipe. Você pode explicar as metas para o experimento com texto e, em seguida, mostrar cada métrica de uso e as consultas de análise usadas para avaliar o experimento, juntamente com as chamadas claras para se cada métrica estava acima ou abaixo do destino.
* Relatar o impacto de uma interrupção no uso de sua VM, combinar dados, explicação de texto e uma discussão das próximas etapas para evitar interrupções no futuro.

O Azure Monitor para VMs inclui várias pastas de trabalho para você começar e a tabela a seguir as resume.

| Livro | Descrição | Âmbito |
|----------|-------------|-------|
| Desempenho | Fornece uma versão personalizável do nosso modo de exibição de lista e gráficos em uma única pasta de trabalho que aproveita todos os contadores de desempenho de Log Analytics que você habilitou.| Em escala |
| Contadores de desempenho | Uma exibição de gráfico N superior em um amplo conjunto de contadores de desempenho. | Em escala |
| Ligações | Conexões fornece uma exibição detalhada das conexões de entrada e saída de suas VMs monitoradas. | Em escala |
| Portas Ativas | Fornece uma lista dos processos que foram vinculados às portas nas VMs monitoradas e suas atividades no período de tempo escolhido. | Em escala |
| Portas Abertas | Fornece o número de portas abertas em suas VMs monitoradas e os detalhes sobre essas portas abertas. | Em escala |
| Ligações com Falhas | Exiba a contagem de conexões com falha em suas VMs monitoradas, a tendência de falha e se a porcentagem de falhas estiver aumentando com o passar do tempo. | Em escala |
| Segurança e Auditoria | Uma análise de seu tráfego TCP/IP que relata sobre conexões gerais, conexões mal-intencionadas, em que os pontos de extremidade de IP residem globalmente.  Para habilitar todos os recursos, será necessário habilitar a detecção de segurança. | Em escala |
| Tráfego TCP | Um relatório classificado para suas VMs monitoradas e seus tráfegos de rede enviados, recebidos e totais em uma grade e exibidos como uma linha de tendência. | Em escala |
| Comparação de Tráfego | Essas pastas de trabalho permitem comparar as tendências de tráfego de rede para um único computador ou um grupo de computadores. | Em escala |
| Desempenho | Fornece uma versão personalizável do nosso modo de exibição de desempenho que aproveita todos os contadores de desempenho de Log Analytics que você habilitou. | VM única | 
| Ligações | Conexões fornece uma exibição detalhada das conexões de entrada e saída de sua VM. | VM única |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Iniciando com um modelo ou pasta de trabalho salva

Uma pasta de trabalho é composta por seções que consistem em gráficos, tabelas, texto e controles de entrada independentemente de serem editados. Para entender melhor as pastas de trabalho, vamos começar abrindo um modelo e percorrer a criação de uma pasta de trabalho personalizada. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Selecione **máquinas virtuais**.

3. Na lista, selecione uma VM.

4. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)** .

5. Na página de informações da VM, selecione a guia **desempenho** ou **mapas** e, em seguida, selecione **exibir pastas de trabalho** no link na página. 

    ![Captura de tela de navegação para pastas de trabalho](media/vminsights-workbooks/workbook-option-01.png)

6. Na lista suspensa, selecione **ir para a Galeria** na parte inferior da lista.

    ![Captura de tela da lista suspensa da pasta de trabalho](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Ele inicia a Galeria de pastas de trabalho com várias pastas de trabalho predefinidas para ajudá-lo a começar.

7. Começaremos com o **modelo padrão**, localizado no **início rápido**do título.

    ![Captura de tela da Galeria de pastas de trabalho](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Editando seções da pasta de trabalho

As pastas de trabalho têm dois modos: **modo de edição**e **modo de leitura**. Quando a pasta de trabalho de modelo padrão é iniciada pela primeira vez, ela é aberta no **modo de edição**. Ele mostra todo o conteúdo da pasta de trabalho, incluindo quaisquer etapas e parâmetros ocultos. O **modo de leitura** apresenta uma exibição de estilo de relatório simplificada. O modo de leitura permite abstrair a complexidade que deu na criação de um relatório enquanto ainda tem a mecânica subjacente apenas alguns cliques quando necessário para modificação.

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Quando terminar de editar uma seção, clique em **edição concluída** no canto inferior esquerdo da seção.

2. Para criar uma duplicata de uma seção, clique no ícone **clonar esta seção** . A criação de seções duplicadas é uma ótima maneira de iterar em uma consulta sem perder as iterações anteriores.

3. Para mover uma seção para cima em uma pasta de trabalho, clique no ícone **mover para cima** ou **mover para baixo** .

4. Para remover uma seção permanentemente, clique no ícone **remover** .

## <a name="adding-text-and-markdown-sections"></a>Adicionando seções de texto e redução

Adicionar títulos, explicações e comentários às suas pastas de trabalho ajuda a transformar um conjunto de tabelas e gráficos em uma narração. As seções de texto em pastas de trabalho dão suporte à [sintaxe de redução](https://daringfireball.net/projects/markdown/) para formatação de texto, como títulos, negrito, itálico e listas com marcadores.

Para adicionar uma seção de texto à pasta de trabalho, use o botão **Adicionar texto** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionando seções de consulta

![Seção de consulta em pastas de trabalho](media/vminsights-workbooks/005-workbook-query-section.png)

Para adicionar a seção de consulta à sua pasta de trabalho, use o botão **Adicionar consulta** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

As seções de consulta são altamente flexíveis e podem ser usadas para responder a perguntas como:

* Qual a minha utilização da CPU durante o mesmo período de tempo que um aumento no tráfego de rede?
* Qual foi a tendência no espaço em disco disponível no último mês?
* Quantas falhas de conexão de rede tinham minha experiência de VM nas últimas duas semanas? 

Você também não está limitado à consulta no contexto da máquina virtual da qual iniciou a pasta de trabalho. Você pode consultar entre várias máquinas virtuais, bem como Log Analytics espaços de trabalho, desde que tenha permissão de acesso a esses recursos.

Para incluir dados de outros espaços de trabalho do Log Analytics ou de um aplicativo Application Insights específico usando o identificador do **espaço de trabalho** . Para saber mais sobre consultas entre recursos, consulte a [orientação oficial](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Configurações avançadas de consulta analítica

Cada seção tem suas próprias configurações avançadas, que podem ser acessadas por meio da seção Configurações ![pastas de trabalho editar controles](media/vminsights-workbooks/006-settings.png) ícone localizado à direita do botão **adicionar parâmetros** .

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largura personalizada**    | Torna um item um tamanho arbitrário, para que você possa ajustar muitos itens em uma única linha, permitindo que você organize melhor seus gráficos e tabelas em relatórios interativos sofisticados.  |
| **Condicionalmente visível** | Especifique para ocultar as etapas com base em um parâmetro no modo de leitura. |
| **Exportar um parâmetro**| Permitir que uma linha selecionada na grade ou no gráfico cause etapas posteriores para alterar valores ou tornar-se visível.  |
| **Mostrar consulta quando não estiver editando** | Exibe a consulta acima do gráfico ou tabela, mesmo quando estiver no modo de leitura.
| **Mostrar botão abrir no Analytics quando não estiver editando** | Adiciona o ícone de análise azul ao canto direito do gráfico para permitir o acesso com um clique.|

A maioria dessas configurações é bastante intuitiva, mas para entender a **exportação de um parâmetro** , é melhor examinar uma pasta de trabalho que usa essa funcionalidade.

Uma das pastas de trabalho predefinidas – **tráfego TCP**, fornece informações sobre métricas de conexão de uma VM.

A primeira seção da pasta de trabalho é baseada em dados de consulta de log. A segunda seção também é baseada em dados de consulta de log, mas a seleção de uma linha na primeira tabela atualizará interativamente o conteúdo dos gráficos:

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

O comportamento é possível por meio do uso de **quando um item é selecionado, exportar um parâmetro** configurações avançadas, que são habilitadas na consulta de log da tabela.

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/009-settings-export.png)

A segunda consulta de log utiliza os valores exportados quando uma linha é selecionada para criar um conjunto de valores que são usados pelo cabeçalho e pelos gráficos da seção. Se nenhuma linha for selecionada, ela ocultará o cabeçalho e os gráficos da seção. 

Por exemplo, o parâmetro Hidden na segunda seção usa a seguinte referência da linha selecionada na grade:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Adicionando seções de métricas

As seções de métricas oferecem acesso completo para incorporar Azure Monitor dados de métricas em seus relatórios interativos. Em Azure Monitor para VMs, as pastas de trabalho predefinidas normalmente conterão dados de consulta analítica em vez de dados de métrica.  Você pode optar por criar pastas de trabalho com dados de métrica, permitindo que você aproveite ao máximo os dois recursos em um só lugar. Você também tem a capacidade de extrair dados de métrica de recursos em qualquer uma das assinaturas às quais você tem acesso.

Aqui está um exemplo de dados da máquina virtual que estão sendo obtidos em uma pasta de trabalho para fornecer uma visualização em grade do desempenho da CPU:

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionando seções de parâmetro

Os parâmetros da pasta de trabalho permitem alterar valores na pasta de trabalho sem precisar editar manualmente as seções de consulta ou texto. Isso elimina a necessidade de entender a linguagem de consulta de análise subjacente e expandir muito o possível público de relatórios baseados em pastas de trabalho.

Os valores dos parâmetros são substituídos em seções de consulta, texto ou outro parâmetro, colocando o nome do parâmetro entre chaves, como ``{parameterName}``. Os nomes de parâmetro são limitados a regras semelhantes como identificadores de JavaScript, caracteres alfabéticos ou sublinhados, seguidos de caracteres alfanuméricos ou sublinhados. Por exemplo, **a1** é permitido, mas **1a** não é permitido.

Os parâmetros são lineares, começando na parte superior de uma pasta de trabalho e fluindo para etapas posteriores.  Parâmetros declarados posteriormente em uma pasta de trabalho podem substituir parâmetros que foram declarados anteriormente. Isso também permite que os parâmetros que usam consultas acessem os valores dos parâmetros definidos anteriormente. Dentro da própria etapa de um parâmetro, os parâmetros também são lineares, da esquerda para a direita, em que os parâmetros à direita podem depender de um parâmetro declarado anteriormente na mesma etapa.
 
Há quatro tipos diferentes de parâmetros, que atualmente têm suporte:

|                  |      |
| ---------------- |:-----|
| **Text** (Texto)    | Permite que o usuário edite uma caixa de texto e, opcionalmente, você pode fornecer uma consulta para preencher o valor padrão. |
| **Lista suspensa** | Permite que o usuário escolha entre um conjunto de valores. |
| **Seletor de intervalo de tempo**| Permite que o usuário escolha entre um conjunto predefinido de valores de intervalo de tempo ou escolha um intervalo de tempo personalizado.|
| **Seletor de recursos** | Permite que o usuário escolha entre os recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um usuário digita na caixa de texto é substituído diretamente na consulta, sem escape ou quot. Se o valor necessário for uma cadeia de caracteres, a consulta deverá ter aspas em volta do parâmetro (como **' {Parameter} '** ).

O parâmetro text permite que o valor em uma caixa de texto seja usado em qualquer lugar. Pode ser um nome de tabela, nome de coluna, nome de função, operador, etc.  O tipo de parâmetro text tem uma configuração **obter valor padrão da consulta Analytics**, que permite que o autor da pasta de trabalho use uma consulta para popular o valor padrão dessa caixa de texto.

Ao usar o valor padrão de uma consulta de log, somente o primeiro valor da primeira linha (linha 0, coluna 0) é usado como o valor padrão. Portanto, é recomendável limitar sua consulta para retornar apenas uma linha e uma coluna. Todos os outros dados retornados pela consulta são ignorados. 

Qualquer valor que a consulta retornar será substituído diretamente sem escape ou quot. Se a consulta não retornar nenhuma linha, o resultado do parâmetro será uma cadeia de caracteres vazia (se o parâmetro não for necessário) ou indefinido (se o parâmetro for necessário).

### <a name="using-a-drop-down"></a>Usando uma lista suspensa

O tipo de parâmetro DropDown permite criar um controle suspenso, permitindo a seleção de um ou vários valores.

A lista suspensa é preenchida por uma consulta de log ou JSON. Se a consulta retornar uma coluna, os valores nessa coluna serão o valor e o rótulo no controle suspenso. Se a consulta retornar duas colunas, a primeira coluna será o valor e a segunda coluna será o rótulo mostrado na lista suspensa. Se a consulta retornar três colunas, a terceira coluna será usada para indicar a seleção padrão nesse menu suspenso. Essa coluna pode ser qualquer tipo, mas a mais simples é usar tipos bool ou numéricos, em que 0 é false e 1 é verdadeiro.

Se a coluna for um tipo de cadeia de caracteres, uma cadeia de caracteres nula/vazia será considerada falsa e qualquer outro valor será considerado verdadeiro. Para menus suspensos de seleção única, o primeiro valor com um valor true é usado como a seleção padrão.  Para menus suspensos com várias seleções, todos os valores com um valor true são usados como o conjunto selecionado padrão. Os itens na lista suspensa são mostrados em qualquer ordem em que a consulta retorna linhas. 

Vamos examinar os parâmetros presentes no relatório visão geral de conexões. Clique no símbolo de edição ao lado de **direção**.

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Isso abrirá o item de menu **Editar parâmetro** .

![Controles de edição da seção Azure Monitor para VMs pastas de trabalho](media/vminsights-workbooks/012-workbook-edit-parameter.png)

O JSON permite gerar uma tabela arbitrária populada com conteúdo. Por exemplo, o JSON a seguir gera dois valores na lista suspensa:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Um exemplo mais aplicável é usar uma lista suspensa para escolher um conjunto de contadores de desempenho por nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A consulta exibirá os resultados da seguinte maneira:

![Lista suspensa do contador de desempenho](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Os menus suspensos são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora seja possível criar seu próprio parâmetro de intervalo de tempo personalizado por meio do tipo de parâmetro DropDown, você também pode usar o tipo de parâmetro de intervalo de tempo pronto para uso se não precisar do mesmo grau de flexibilidade. 

Os tipos de parâmetro de intervalo de tempo têm 15 intervalos padrão que vão de cinco minutos até os últimos 90 dias. Há também uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador do relatório escolha valores explícitos de início e parada para o intervalo de tempo.

### <a name="resource-picker"></a>Seletor de recursos

O tipo de parâmetro do seletor de recursos oferece a capacidade de fazer o escopo do relatório para determinados tipos de recursos. Um exemplo de uma pasta de trabalho predefinida que utiliza o tipo de seletor de recursos é a pasta de trabalho de **desempenho** .

![Menu suspenso espaços de trabalho](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

As pastas de trabalho são salvas em um Log Analytics espaço de trabalho ou em um recurso de máquina virtual, dependendo de como você acessa a Galeria de pastas de trabalho. A pasta de trabalho pode ser salva na seção **meus relatórios** que é particular para você ou na seção **relatórios compartilhados** que é acessível a todos com acesso ao recurso. Para exibir todas as pastas de trabalho no recurso, clique no botão **abrir** na barra de ação.

Para compartilhar uma pasta de trabalho que está atualmente em **meus relatórios**:

1. Clique em **abrir** na barra de ação
2. Clique no botão "..." botão ao lado da pasta de trabalho que você deseja compartilhar
3. Clique em **mover para relatórios compartilhados**.

Para compartilhar uma pasta de trabalho com um link ou por email, clique em **compartilhar** na barra de ação. Tenha em mente que os destinatários do link precisam de acesso a esse recurso no portal do Azure para exibir a pasta de trabalho. Para fazer edições, os destinatários precisam de pelo menos permissões de colaborador para o recurso.

Para fixar um link em uma pasta de trabalho em um painel do Azure:

1. Clique em **abrir** na barra de ação
2. Clique no botão "..." botão ao lado da pasta de trabalho que você deseja fixar
3. Clique em **fixar no painel**.

## <a name="next-steps"></a>Passos seguintes

- Para identificar as limitações e o desempenho geral da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).

- Para saber mais sobre dependências de aplicativo descobertas, consulte [exibir mapa de Azure monitor para VMs](vminsights-maps.md).
