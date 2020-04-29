---
title: Use o servidor de histórico spark estendido para depurar aplicações - Apache Spark em Azure Synapse
description: Utilize o servidor de histórico spark estendido para depurar e diagnosticar aplicações Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429216"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Utilize um servidor de histórico apache prolongado para depurar e diagnosticar aplicações Apache Spark

Este artigo fornece orientações sobre como usar o servidor de histórico Apache Spark estendido para depurar e diagnosticar aplicações spark completas e executantes.

A extensão inclui um separador de dados, separador gráfico e separador de diagnóstico. Utilize o separador **Dados** para verificar os dados de entrada e saída do trabalho spark. O separador **Graph** mostra-lhe o fluxo de dados e a repetição do gráfico de trabalho. O separador **Diagnóstico** mostra-lhe **Dados Skew,** **Time Skew**e **Executor Usage Analysis**.

## <a name="access-the-apache-spark-history-server"></a>Aceda ao servidor de histórico Apache Spark

O servidor de histórico apache Spark é a interface do utilizador web para aplicações Spark concluídas e executantes. Pode abrir a interface web do servidor de histórico Apache Spark a partir do Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Abra o Web UI do Spark History Server a partir do nó de aplicações de faíscas Apache

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Clique no **Monitor**e, em seguida, selecione **Aplicações de Faísca Supor .**

    ![Clique no monitor e selecione a aplicação de faísca.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecione uma aplicação e abra a consulta de **Registo** clicando nela.

    ![Abra a janela de consulta de log.](./media/apache-spark-history-server/open-application-window.png)

4. Selecione **o servidor de histórico spark**, então o Web UI do Spark History Server aparecerá.

    ![Abrir o servidor de histórico de faíscas.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Abra a UI web do Spark History Server a partir do nó de Dados

1. A partir do seu notebook Azure Synapse Studio, selecione o servidor de **histórico spark** a partir da célula de saída de execução de trabalho ou do painel de estado na parte inferior do documento do portátil. Selecione **Detalhes da sessão**.

   ![Lançar servidor de histórico spark](./media/apache-spark-history-server/launch-history-server2.png "Lançar servidor de histórico spark")

2. Selecione o servidor de **histórico** spark a partir do painel de diapositivos.

   ![Lançar servidor de histórico spark](./media/apache-spark-history-server/launch-history-server.png "Lançar servidor de histórico spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Explore o separador Dados no servidor de histórico spark

Selecione o ID de trabalho para o trabalho que pretende ver. Em seguida, selecione **Dados** no menu da ferramenta para obter a visualização de dados. Esta secção mostra-lhe como fazer várias tarefas no separador Dados.

* Verifique as **entradas,** **saídas**e **operações** de mesa selecionando os separadores separadamente.

    ![Dados para separadores de aplicação Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copie todas as linhas selecionando **Copy**.

    ![Cópia de dados para aplicação Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Guarde todos os dados como ficheiro CSV selecionando **csv**.

    ![Dados para salvar aplicações Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Procure inserindo palavras-chave no campo **Procurar**. Os resultados da pesquisa exibem imediatamente.

    ![Dados para pesquisa de aplicações Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selecione o cabeçalho da coluna para classificar a tabela, selecione o sinal de mais para expandir uma linha para mostrar mais detalhes ou selecione o sinal de menos para colapsar uma linha.

    ![Dados para tabela de aplicações Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Faça o download de um único ficheiro selecionando **O Download Parcial**. O ficheiro selecionado é descarregado para local. Se o ficheiro já não existir, aparece um novo separador com uma mensagem de erro.

    ![Data for Spark application download row](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Para copiar um caminho completo ou caminho relativo, selecione as opções **Copy Full Path** ou Copy Relative **Path** que se expandem a partir do menu drop down. Para ficheiros de armazenamento de lagos de dados Azure, **o Open in Azure Storage Explorer** lança o Azure Storage Explorer e localiza a pasta quando está sintetizada.

    ![Dados para o caminho de cópia da aplicação Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecione os números de página abaixo da tabela para navegar nas páginas quando houver muitas filas para exibir numa página.

    ![Página de aplicação de Dados para Faíscas](./media/apache-spark-history-server/apache-spark-data-page.png)

* Paire sobre o ponto de interrogação ao lado **de Data** para mostrar a dica de ferramentas, ou selecione o ponto de interrogação para obter mais informações.

    ![Dados para aplicação Spark mais informações](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Envie feedback com problemas selecionando **Fornecer-nos feedback**.

    ![Gráfico de faísca nos fornece feedback novamente](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Separador de gráfico no servidor de histórico apache Spark

Selecione o ID de trabalho para o trabalho que pretende ver. Em seguida, selecione **Graph** no menu da ferramenta para obter a vista do gráfico de trabalho.

### <a name="overview"></a>Descrição geral

Pode ver uma visão geral do seu trabalho no gráfico de trabalho gerado. Por padrão, o gráfico mostra todos os trabalhos. Pode filtrar esta vista por **Id de trabalho**.

![Aplicação de faísca e id de trabalho gráfico de trabalho](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Apresentar

Por predefinição, o visor **Progress** é selecionado. Pode verificar o fluxo de dados selecionando **Read** ou **Written** na lista de dropdown **do Display.**

![Aplicação de faísca e exibição de gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-display.png)

O nó gráfico exibe as cores mostradas na lenda do mapa de calor.

![Aplicação de faísca e mapa de calor do gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Reproduzir

Para reproduzir o trabalho, selecione **Playback**. Pode selecionar **Parar** a qualquer momento para parar. As cores da tarefa mostram diferentes estados ao reproduzir:

|Cor|Significado|
|-|-|
|Verde|Sucesso: O trabalho completou com sucesso.|
|Orange|Retry: Instâncias de tarefas que falharam mas não afetam o resultado final do trabalho. Estas tarefas tiveram casos duplicados ou recorrentes que podem ter sucesso mais tarde.|
|Azul|A tarefa está a decorrer.|
|Branco|Espera ou ignorado: A tarefa é esperar para correr, ou o palco saltou.|
|Vermelho|Falhada: a tarefa falhou.|

A imagem que se segue mostra as cores verde, laranja e azul do estado.

![Aplicação de faísca e amostra de cor de gráfico de trabalho, execução](./media/apache-spark-history-server/sparkui-graph-color-running.png)

A imagem que se segue mostra as cores do estado verde e branco.

![Aplicação de faísca e amostra de cor do gráfico de trabalho, skip](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

A imagem que se segue mostra as cores do estado vermelho e verde.

![Aplicação de faísca e amostra de cor de gráfico de trabalho, falhou](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> É permitida a reprodução para cada trabalho. Para trabalhos incompletos, a reprodução não é apoiada.

### <a name="zoom"></a>Zoom

Use o pergaminho do rato para fazer zoom no gráfico de trabalho ou **selecione Zoom para o** tornar adequado ao ecrã.

![Aplicação de faísca e zoom gráfico de trabalho para caber](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Descrições

Passe no nó gráfico para ver a ponta da ferramenta quando houver tarefas falhadas e selecione um palco para abrir a sua página de palco.

![Aplicação de faísca e dica de ferramenta de gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

No separador de gráfico de trabalho, os estágios têm uma dica de ferramenta e um pequeno ícone apresentado se tiverem tarefas que satisfaçam as seguintes condições:

|Condição|Descrição|
|-|-|
|Distorção de dados|dados lêem tamanho > dados médios ler tamanho de todas as tarefas dentro desta fase * 2 e dados lêem tamanho > 10 MB|
|O tempo distorce|tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 e tempo de execução > 2 minutos|
   
![Aplicação de faísca e ícone de gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrição do nó gráfico

O nó gráfico de trabalho apresenta as seguintes informações de cada fase:

  * IDENTIFICAÇÃO.
  * Nome ou descrição.
  * Número total de tarefa.
  * Os dados são lidos: a soma do tamanho da entrada e do tamanho da leitura.
  * Os dados escrevem: a soma do tamanho da saída e da baralhada escreve o tamanho.
  * Tempo de execução: o tempo entre o tempo de início da primeira tentativa e o tempo de conclusão da última tentativa.
  * Contagem de filas: a soma dos registos de entrada, os registos de saída, a baralhada de registos e a confusão de registos.
  * O progresso.

    > [!NOTE]  
    > Por padrão, o nó gráfico de trabalho exibe informações da última tentativa de cada fase (exceto o tempo de execução do estágio). No entanto, durante a reprodução, o nó gráfico mostra informações de cada tentativa.
    >  
    > O tamanho dos dados de leitura e escrita é 1MB = 1000 KB = 1000 * 1000 Bytes.

### <a name="provide-feedback"></a>Enviar comentários

Envie feedback com problemas selecionando **Fornecer-nos feedback**.

![Aplicação de faísca e feedback do gráfico de trabalho](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Explore o separador Diagnóstico no servidor de histórico Apache Spark

Para aceder ao separador Diagnóstico, selecione um ID de trabalho. Em seguida, selecione **Diagnóstico** no menu de ferramentas para obter a visão de diagnóstico de trabalho. O separador de diagnóstico inclui **Data Skew,** **Time Skew**e **Executor Usage Analysis**.

Verifique a **Análise de Desvio de Dados,** Tempo **e**Utilização do **Executor** selecionando os separadores respectivamente.

![SparkUI diagnóstico dados distorcidos separador novamente](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorte de dados

Quando seleciona o separador **Data Skew,** as tarefas distorcidas correspondentes são apresentadas com base nos parâmetros especificados.

* **Especificar Parâmetros** - A primeira secção apresenta os parâmetros, que são utilizados para detetar dados skew. A regra padrão é: A Leitura de Dados de Tarefas é superior a três vezes a média de dados de tarefas lidas, e os dados de tarefas lidos são superiores a 10 MB. Se quiser definir a sua própria regra para tarefas distorcidas, pode escolher os seus parâmetros, as secções **skewed Stage** e **Skew Char** são refrescadas em conformidade.

* **Fase distorcida** - A segunda secção apresenta fases, que têm tarefas distorcidas que satisfaçam os critérios acima especificados. Se houver mais de uma tarefa distorcida numa fase, a tabela de palco distorcida apenas apresenta a tarefa mais distorcida (por exemplo, os maiores dados para a distorção de dados).

    ![sparkui diagnóstico dados skew tab](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Gráfico de skew** – Quando uma linha na tabela de palco skew é selecionada, o gráfico de skew apresenta mais detalhes de distribuição de tarefas com base no tempo de leitura e execução de dados. As tarefas distorcidas são marcadas a vermelho e as tarefas normais são marcadas em azul. O gráfico apresenta até 100 tarefas de amostra, e os detalhes da tarefa são apresentados no painel inferior direito.

    ![gráfico de skew sparkui para a fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tempo skew

O separador **Time Skew** apresenta tarefas distorcidas com base no tempo de execução da tarefa.

* **Especificar Parâmetros** - A primeira secção apresenta os parâmetros, que são utilizados para detetar o Tempo Skew. Os critérios padrão para detetar o tempo distorcido é: o tempo de execução da tarefa é superior a três vezes o tempo médio de execução e o tempo de execução da tarefa é superior a 30 segundos. Pode alterar os parâmetros com base nas suas necessidades. O **Skewed Stage** e **o Skew Chart** exibem as fases e as informações correspondentes das tarefas, tal como o separador Data **Skew** acima.

* Selecione **Tempo Skew**, em seguida, o resultado filtrado é apresentado na secção **Desviada** de Palco de acordo com os parâmetros definidos na secção **Especte Parâmetros**. Selecione um item na secção **Skewed Stage,** em seguida, o gráfico correspondente é redigido na secção 3, e os detalhes de tarefa são mostrados no painel inferior direito.

    ![sparkui diagnóstico tempo secção distorcida](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de Utilização do Executor

O Gráfico de Utilização do Executor visualiza a atribuição e o estado de funcionamento do executor spark.  

1. Selecione **Análise de Utilização**do Executor , em seguida, quatro tipos de curvas sobre o uso do executor são redigidos, incluindo **Executores Atribuídos,** **Executores de Execução,** **Executores Ociosos**e Instâncias de **Executor Max**. No que diz respeito aos executores atribuídos, cada evento "Executor adicionado" ou "Executor removido" aumenta ou diminui os executores atribuídos. Pode consultar "Event Timeline" no separador "Jobs" para obter mais comparação.

   ![tab de executores de diagnóstico sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecione o ícone de cor para selecionar ou desseleccionar o conteúdo correspondente em todos os rascunhos.

    ![sparkui diagnostica gráfico selecionado](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemas conhecidos

Os dados de entrada/saída utilizando conjuntos de dados distribuídos resilientes (RDDs) não aparecem no separador de dados.

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

