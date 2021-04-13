---
title: Monitorização dos fluxos de dados de mapeamento
description: Como monitorizar visualmente os fluxos de dados de mapeamento na Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/11/2021
ms.openlocfilehash: 82aba428627cba1a3df26fc67c5da0cde52d368c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309072"
---
# <a name="monitor-data-flows"></a>Monitorize fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Depois de ter concluído a construção e depurando o fluxo de dados, pretende agendar o fluxo de dados para executar num horário no contexto de um pipeline. Pode agendar o gasoduto a partir da Azure Data Factory utilizando Triggers. Ou pode utilizar a opção Trigger Now do Azure Data Factory Pipeline Builder para executar uma execução única para testar o fluxo de dados dentro do contexto do pipeline.

Quando executa o seu oleoduto, pode monitorizar o gasoduto e todas as atividades contidas no oleoduto, incluindo a atividade do Fluxo de Dados. Clique no ícone do monitor no painel de UI da Fábrica de Dados Azure. Pode ver um ecrã semelhante ao abaixo. Os ícones destacados permitem-lhe perfurar as atividades no oleoduto, incluindo a atividade do Fluxo de Dados.

![A screenshot mostra ícones para selecionar para os oleodutos para obter mais informações.](media/data-flow/mon001.png "Monitorização de Fluxo de Dados")

Vê-se estatísticas a este nível, incluindo os tempos de execução e o estado. O ID de execução ao nível de atividade é diferente do ID de execução ao nível do gasoduto. O Run ID no nível anterior é para o gasoduto. A seleção dos óculos dá-lhe detalhes profundos sobre a execução do fluxo de dados.

![A screenshot mostra o ícone dos óculos para ver detalhes da execução do fluxo de dados.](media/data-flow/monitoring-details.png "Monitorização de Fluxo de Dados")

Quando está na vista de monitorização do nó gráfico, pode ver uma versão simplificada apenas do seu gráfico de fluxo de dados.

![A screenshot mostra a versão apenas visualizada do gráfico.](media/data-flow/mon003.png "Monitorização de Fluxo de Dados")

Aqui está uma visão geral de vídeo do desempenho da monitorização dos seus fluxos de dados a partir do ecrã de monitorização ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Ver Planos de Execução de Fluxo de Dados

Quando o seu Fluxo de Dados é executado em Spark, a Azure Data Factory determina caminhos de código ideais com base na totalidade do seu fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de escala e divisórias de dados. Portanto, o gráfico de monitorização representa o design do seu fluxo, tendo em conta o caminho de execução das suas transformações. Quando seleciona nós individuais, pode ver "agrupamentos" que representam código que foi executado em conjunto no cluster. Os timings e contagens que você vê representam esses grupos em oposição aos passos individuais no seu design.

![A screenshot mostra a página para um fluxo de dados.](media/data-flow/mon004.png "Monitorização de Fluxo de Dados")

* Quando selecionar o espaço aberto na janela de monitorização, as estatísticas no painel inferior mostram o tempo e as contagens de linha para cada Pia e as transformações que levaram aos dados do lavatório para a linhagem de transformação.

* Quando seleciona transformações individuais, recebe feedback adicional no painel da direita que mostra estatísticas de partição, contagem de colunas, distorção (quão uniformemente são os dados distribuídos por divisórias) e kurtose (quão espinhosos são os dados).

* Quando selecionar a Pia na vista do nó, pode ver a linhagem da coluna. Existem três métodos diferentes que as colunas são acumuladas ao longo do seu fluxo de dados para aterrar na Pia. A saber:

  * Computação: Utiliza a coluna para processamento condicional ou dentro de uma expressão no fluxo de dados, mas não a aterra na Pia
  * Derivado: A coluna é uma nova coluna que gerou no seu fluxo, isto é, não estava presente na Fonte
  * Mapeado: A coluna originou-se da fonte e está a mapeá-la para um campo de pia
  * Estado do fluxo de dados: O estado atual da sua execução
  * Tempo de arranque do cluster: Quantidade de tempo para adquirir o ambiente computacional JIT Spark para a execução do fluxo de dados
  * Número de transformações: Quantos passos de transformação estão a ser executados no seu fluxo
  
![A screenshot mostra a opção Refresh.](media/data-flow/monitornew.png "Monitorização do fluxo de dados novo")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Tempo Total de Processamento de Sinks vs. Tempo de Processamento de Transformações

Cada fase de transformação inclui um tempo total para que essa fase se complete com cada tempo de execução de partição totalizado. Quando clicar na Pia verá "Tempo de Processamento da Pia". Este tempo inclui o total do tempo de transformação *mais* o tempo de E/S que demorou a escrever os seus dados para a sua loja de destino. A diferença entre o tempo de processamento do lavatório e o total da transformação é a hora de E/S para escrever os dados.

Também pode ver um timing detalhado para cada passo de transformação de partição se abrir a saída JSON a partir da sua atividade de fluxo de dados na vista de monitorização do gasoduto ADF. O JSON contém um tempo de milissegundo para cada partição, enquanto que a visão de monitorização UX é um calendário agregado de divisórias adicionadas em conjunto:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="sink-processing-time"></a>Tempo de processamento de pias

Quando selecionar um ícone de transformação de pia no seu mapa, o painel de slide-in à direita mostrará um ponto de dados adicional chamado "tempo de processamento pós-processamento" na parte inferior. Esta é a quantidade de tempo gasto a executar o seu trabalho no cluster Spark *depois de* os seus dados terem sido carregados, transformados e escritos. Desta vez pode incluir o fecho de piscinas de ligação, paragem do condutor, eliminação de ficheiros, ficheiros de açagaçamento, etc. Quando executa ações no seu fluxo como "move ficheiros" e "saída para um único ficheiro", provavelmente verá um aumento no valor do tempo de processamento pós-processamento.

* Duração do estágio de escrita: O tempo para escrever os dados para um local de preparação para Synapse SQL
* Duração da operação de tabela SQL: O tempo gasto a mover dados das tabelas temporárias para a tabela alvo
* Duração pré-SQL & duração do Post SQL: O tempo gasto a correr comandos SQL pré/post
* Duração dos comandos pré & duração dos comandos de registo: O tempo gasto a executar quaisquer operações pré/post para fonte/pias baseadas em ficheiros. Por exemplo, mover ou apagar ficheiros após o processamento.
* Duração da fusão: O tempo gasto a fundir o ficheiro, os ficheiros de fusão são utilizados para lavatórios baseados em ficheiros quando escrevem para um único ficheiro ou quando é utilizado "Nome de ficheiro como dados de coluna". Se for gasto tempo significativo nesta métrica, deve evitar utilizar estas opções.
  
## <a name="error-rows"></a>Linhas de erro

Ativar o manuseamento de linhas de erro no seu lavatório de fluxo de dados será refletido na saída de monitorização. Quando se coloca a pia como "reporte o sucesso do erro", a saída de monitorização mostrará o número de linhas de sucesso e de falhas quando clicar no nó de monitorização da pia.

![A imagem mostra linhas de erro.](media/data-flow/error-row-2.png "Sucesso de monitorização da linha de erro")

Quando selecionar "reportar falha no erro", a mesma saída será mostrada apenas no texto de saída de monitorização da atividade. Isto porque a atividade de fluxo de dados devolverá a falha de execução e a visão de monitorização detalhada não estará disponível.

![A screenshot mostra linhas de erro na atividade.](media/data-flow/error-rows-4.png "Falha de monitorização da linha de erro")

## <a name="monitor-icons"></a>Ícones do monitor

Este ícone significa que os dados de transformação já estavam em cache no cluster, por isso os timings e o caminho de execução têm tido isso em conta:

![A imagem mostra o ícone do disco.](media/data-flow/mon005.png "Monitorização de Fluxo de Dados")

Você também vê ícones do círculo verde na transformação. Representam uma contagem do número de sumidouros em que os dados estão a fluir.
