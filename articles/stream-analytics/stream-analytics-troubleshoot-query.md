---
title: Resolução de problemas Azure Stream Análises
description: Este artigo descreve técnicas para resolver problemas nas suas consultas em trabalhos Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0dc36b817d5b5cdf731edecd64e1879c153d866a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015135"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Resolução de problemas Azure Stream Análises

Este artigo descreve questões comuns com o desenvolvimento de consultas stream analytics e como resolvê-las.

Este artigo descreve questões comuns com o desenvolvimento de consultas Azure Stream Analytics, como resolver problemas de consulta e como corrigir os problemas. Muitas etapas de resolução de problemas requerem registos de recursos para o seu trabalho stream Analytics. Se não tiver registos de recursos ativados, consulte [o Troubleshoot Azure Stream Analytics utilizando registos de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>A consulta não está a produzir a produção esperada

1.  Examinar erros testando localmente:

    - No portal Azure, no **separador 'Consulta',** selecione **Teste**. Utilize os dados da amostra descarregados para [testar a consulta](stream-analytics-test-query.md). Examine quaisquer erros e tente corrigi-los.   
    - Também pode [testar a sua consulta localmente](stream-analytics-live-data-local-testing.md) utilizando ferramentas Azure Stream Analytics para Visual Studio ou Visual Studio [Code](visual-studio-code-local-run-live-input.md). 

2.  [Consultas de depuração passo a passo localmente usando diagrama de trabalho](debug-locally-using-job-diagram-vs-code.md) em ferramentas Azure Stream Analytics para Código de Estúdio Visual. O diagrama de trabalho mostra como os dados fluem de fontes de entrada (Event Hub, IoT Hub, etc.) através de múltiplos passos de consulta e, finalmente, para os lavatórios de saída. Cada passo de consulta é mapeado para um conjunto de resultados temporário definido no script usando a declaração COM. Pode ver os dados, bem como as métricas, em cada conjunto de resultados intermédios para encontrar a origem da questão.

    ![Resultado da pré-visualização do diagrama de trabalho](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Se utilizar [**o Timetamp By,**](/stream-analytics-query/timestamp-by-azure-stream-analytics)verifique se os eventos têm prazos superiores aos da [hora de início](./stream-analytics-time-handling.md)do trabalho .

4.  Eliminar armadilhas comuns, tais como:
    - Uma cláusula [**WHERE**](/stream-analytics-query/where-azure-stream-analytics) na consulta filtrada todos os eventos, impedindo que qualquer saída seja gerada.
    - Uma função [**CAST**](/stream-analytics-query/cast-azure-stream-analytics) falha, fazendo com que o trabalho falhe. Para evitar falhas de tipo de molde, utilize [**TRY_CAST.**](/stream-analytics-query/try-cast-azure-stream-analytics)
    - Quando utilizar funções de janela, aguarde toda a duração da janela para ver uma saída da consulta.
    - O tempo de espera para eventos antecede a hora de início do trabalho e os eventos são retirados.
    - [**As**](/stream-analytics-query/join-azure-stream-analytics) condições de junção não coincidem. Se não houver fósforos, não haverá saída zero.

5.  Certifique-se de que as políticas de ordenação de eventos estão configuradas como esperado. Vá a **Definições** e [**selecione Pedido de Eventos**](./stream-analytics-time-handling.md). A apólice *não* é aplicada quando se utiliza o botão **Teste** para testar a consulta. Este resultado é uma diferença entre testar no navegador e executar o trabalho em produção. 

6. Depurar utilizando registos de atividade e de recursos:
    - Utilize [Registos de Atividades](../azure-resource-manager/management/view-activity-logs.md)e filtre para identificar e depurar erros.
    - Utilize [registos de recursos de trabalho](stream-analytics-job-diagnostic-logs.md) para identificar e depurar erros.

## <a name="resource-utilization-is-high"></a>A utilização de recursos é elevada

Certifique-se de que tira partido da paralelização no Azure Stream Analytics. Você pode aprender a [escalar com a paralelização](stream-analytics-parallelization.md) de consulta de trabalhos stream Analytics configurando divisórias de entrada e sintonizando a definição de consulta de análise.

Se a utilização dos recursos for consistentemente superior a 80%, o atraso da marca de água está a aumentar e o número de eventos recuados está a aumentar, considere o aumento das unidades de streaming. A elevada utilização indica que o trabalho está a utilizar perto dos recursos máximos atribuídos.

## <a name="debug-queries-progressively"></a>Debug queries progressively (Depurar consultas progressivamente)

No processamento de dados em tempo real, saber como são os dados no meio da consulta pode ser útil. Pode ver isto usando o diagrama de trabalho no Estúdio Visual. Se não tiver o Visual Studio, pode tomar medidas adicionais para obter dados intermédios.

Como as entradas ou passos de um trabalho Azure Stream Analytics podem ser lidos várias vezes, pode escrever declarações extra SELECT INTO. Ao fazê-lo, produz dados intermédios para armazenamento e permite-lhe inspecionar a correção dos *dados,* tal como as variáveis do relógio fazem quando depuram um programa.

A seguinte consulta de exemplo num trabalho Azure Stream Analytics tem uma entrada de fluxo, duas entradas de dados de referência e uma saída para o Azure Table Storage. A consulta junta dados do centro do evento e duas bolhas de referência para obter o nome e informações da categoria:

![Exemplo Stream Analytics SELECT INTO consulta](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Note que o trabalho está em andamento, mas nenhum evento está sendo produzido na produção. No azulejo **de monitorização,** mostrado aqui, pode ver que a entrada está a produzir dados, mas não sabe qual o passo do **JOIN** que fez com que todos os eventos fossem retirados.

![O azulejo de monitorização do stream analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Nesta situação, pode adicionar algumas declarações select into extra para "registar" os resultados intermédios do JOIN e os dados que são lidos a partir da entrada.

Neste exemplo, adicionámos duas novas "saídas temporárias". Podem ser qualquer pia que quiser. Aqui usamos o Azure Storage como exemplo:

![Adicionar declarações extra SELECT INTO à consulta Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Em seguida, pode reescrever a consulta desta forma:

![Consulta reescrita SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora começa o trabalho de novo, e deixa-o funcionar por alguns minutos. Em seguida, consultar a temperatura1 e a temperatura2 com o Visual Studio Cloud Explorer para produzir as seguintes tabelas:

 
 tabela ![ temporária1 SELECIONE EM consulta de stream de tabela1](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

 
 tabela ![ temporária2 SELECIONE EM consulta de stream de tabela temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como pode ver, a temperatura1 e a temperatura2 têm dados, e a coluna de nomes é povoada corretamente em temperatura2. No entanto, como ainda não há dados na produção, algo está errado:

![SELECIONE PARA a tabela de saída1 sem consulta de Stream Analytics de dados](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Ao recolher amostras dos dados, pode ter quase a certeza de que a questão está na segunda JOIN. Pode descarregar os dados de referência da bolha e dar uma olhada:

![SELECIONE PARA ref tabela Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como pode ver, o formato do GUID nestes dados de referência é diferente do formato da coluna [de] em temp2. É por isso que os dados não chegaram na saída 1 como esperado.

Pode corrigir o formato de dados, carregá-lo para a blob de referência e tentar novamente:

![SELECIONE EM tabela temporária Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Desta vez, os dados na saída são formatados e povoados como esperado.

![SELECIONE PARA a mesa final Stream Analytics consulta](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)
