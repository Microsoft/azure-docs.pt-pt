---
title: Saídas de Troubleshoot Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas as suas ligações de saída em empregos de Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 5652df0cf142af2ff96590368892530abcb3d667
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133220"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Saídas de Troubleshoot Azure Stream Analytics

Este artigo descreve questões comuns com ligações de saída do Azure Stream Analytics, como resolver problemas de saída e como corrigir os problemas. Muitos passos de resolução de problemas requerem recursos e outros registos de diagnóstico para o seu trabalho de Stream Analytics. Se não tiver registos de recursos ativados, consulte [o Troubleshoot Azure Stream Analytics utilizando registos](stream-analytics-job-diagnostic-logs.md)de recursos .

## <a name="output-not-produced-by-job"></a>Saída não produzida por trabalho

1.  Verifique a conectividade com as saídas utilizando o botão **de ligação** de teste para cada saída.

2.  Veja as [**métricas de monitorização**](stream-analytics-monitoring.md) no separador **Monitor.** Como os valores são agregados, as métricas são adiadas por alguns minutos.
   * Se os Eventos de Entrada forem superiores a 0, o trabalho é capaz de ler os dados de entrada. Se os Eventos de Entrada não forem superiores a 0, então há um problema com a entrada do trabalho. Consulte as ligações de [entrada de Troubleshoot](stream-analytics-troubleshoot-input.md) para aprender a resolver problemas de ligação de entrada.
   * Se os erros de conversão de dados forem superiores a 0 e subirem, consulte erros de dados do [Azure Stream Analytics](data-errors.md) para obter informações detalhadas sobre erros de conversão de dados.
   * Se os Erros de Tempo de Execução forem superiores a 0, o seu trabalho pode receber dados, mas está a gerar erros durante o processamento da consulta. Para encontrar os erros, aceda aos [Registos de Auditoria](../azure-resource-manager/management/view-activity-logs.md) e filtre no estado *falhado.*
   * Se o InputEvents for superior a 0 e os OutputEvents forem iguais a 0, um dos seguintes é verdade:
      * O processamento da consulta resultou em zero eventos de saída.
      * Eventos ou campos podem ser mal formados, resultando em saída zero após processamento de consulta.
      * O trabalho não foi capaz de empurrar os dados para o lavatório de saída por razões de conectividade ou autenticação.

   Em todos os casos de erro anteriormente mencionados, as mensagens de registo de operações explicam detalhes adicionais (incluindo o que está a acontecer), exceto nos casos em que a lógica da consulta filtrava todos os eventos. Se o processamento de vários eventos gerar erros, os erros são agregados a cada 10 minutos.

## <a name="job-output-is-delayed"></a>Job output is delayed (A saída do trabalho está atrasada)

### <a name="first-output-is-delayed"></a>A primeira saída é adiada

Quando é iniciado um trabalho do Stream Analytics, os eventos de entrada são lidos, mas pode haver um atraso na produção da saída, em determinadas circunstâncias.

Grandes valores de tempo em elementos de consulta temporal podem contribuir para o atraso de saída. Para produzir uma saída correta ao longo das grandes janelas de tempo, o trabalho de streaming começa por ler dados do tempo mais recente possível (até há sete dias) para preencher a janela de tempo. Durante esse período, não é produzida qualquer saída até que a leitura de atualização dos eventos de entrada pendentes esteja completa. Este problema pode surgir quando o sistema atualiza os trabalhos de streaming, reiniciando assim o trabalho. Tais atualizações geralmente ocorrem uma vez a cada dois meses.

Portanto, use a discrição ao conceber a sua consulta Stream Analytics. Se utilizar uma grande janela de tempo (mais de várias horas, até sete dias) para elementos temporais na sintaxe de consulta do trabalho, pode levar a um atraso na primeira saída quando o trabalho é iniciado ou reiniciado.  

Uma mitigação para este tipo de primeiro atraso de saída é usar técnicas de paraleção de consultas (divisão dos dados), ou adicionar mais Unidades de Streaming para melhorar a produção até que o trabalho recupere.  Para mais informações, consulte [considerações ao criar empregos stream analytics](stream-analytics-concepts-checkpoint-replay.md)

Estes fatores impactam a oportunidade da primeira saída que é gerada:

1. Utilização de agregados com janelas (GROUP BY de janelas tumbling, hopping e sliding)
   - Para agregados de janelas caindo ou saltando, os resultados são gerados no final do período de tempo da janela.
   - Para uma janela deslizante, os resultados são gerados quando um evento entra ou sai da janela deslizante.
   - Se planeia utilizar o tamanho grande da janela (> 1 hora), é melhor escolher a janela de salto ou de deslizamento para que possa ver a saída com mais frequência.

2. Utilização de juntas temporais (JUNTE-SE com DATEDIFF)
   - Os jogos são gerados assim que ambos os lados dos eventos combinados chegam.
   - Os dados que carecem de uma correspondência (LEFT OUTER JOIN) são gerados no final da janela DATEDIFF no que diz respeito a cada evento do lado esquerdo.

3. Utilização de funções analíticas temporais (ISFIRST, LAST e LAG com DURAÇÃO LIMITE)
   - Para funções analíticas, a saída é gerada para cada evento, não há atraso.

### <a name="output-falls-behind"></a>A saída fica para trás

Durante o funcionamento normal do trabalho, se descobrir que a saída do trabalho está a ficar para trás (latência mais longa e mais longa), pode identificar as causas da raiz examinando estes fatores:
- Se a pia a jusante está estrangulada
- Se a fonte a montante está acelerada
- Se a lógica de processamento na consulta é intensiva em computação

Para ver esses detalhes, no portal Azure, selecione o trabalho de streaming e selecione o **diagrama**de Trabalho . Para cada entrada, há uma métrica de evento de atraso por divisória. Se a métrica do evento de atraso continuar a aumentar, é um indicador de que os recursos do sistema estão limitados. Potencialmente isso deve-se à aceleração da saída do lavatório, ou ao alto CPU. Para obter mais informações sobre a utilização do diagrama de trabalho, consulte a [depuração orientada por dados utilizando o diagrama](stream-analytics-job-diagram-with-metrics.md)de trabalho .

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação chave com saída de base de dados Azure SQL

Quando configura a base de dados Azure SQL como saída para um trabalho de Stream Analytics, insere registos a granel na tabela de destino. Em geral, a análise de fluxo Azure garante [pelo menos uma vez](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) que a entrega ao sumidouro de saída, ainda se pode conseguir [exatamente uma vez a entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) à saída SQL quando a tabela SQL tem uma restrição única definida.

Uma vez configurados constrangimentos de chave únicos na tabela SQL, e há registos duplicados a serem inseridos na tabela SQL, o Azure Stream Analytics remove o registo duplicado. Divide os dados em lotes e insere os lotes de forma recursiva até que seja encontrado um único registo duplicado. Se o trabalho de streaming tiver um número considerável de linhas duplicadas, este processo de divisão e inserção tem de ignorar os duplicados, um a um, o que é menos eficiente e demorado. Se vir várias mensagens de aviso de violação de chaves no registo de atividade sem hora passada, é provável que a sua saída SQL esteja a atrasar todo o trabalho.

Para resolver este problema, deve [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação da chave, permitindo a opção IGNORE_DUP_KEY. Permitir que esta opção permita que os valores duplicados sejam ignorados pela SQL durante as inserções a granel e o SQL Azure simplesmente produz uma mensagem de aviso em vez de um erro. O Azure Stream Analytics já não produz erros de violação de chaves primárias.

Note as seguintes observações ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Não é possível definir IGNORE_DUP_KEY numa chave primária ou numa restrição única que utiliza o ALTER INDEX, é necessário baixar e recriar o índice.  
* Pode definir a opção IGNORE_DUP_KEY utilizando o ALTER INDEX para um índice único, diferente da chave primária/restrição única e criada utilizando a definição DE ÍNDICE DE CRIAÇÃO ou INDEX.  

* IGNORE_DUP_KEY não se aplica aos índices de lojas de colunas porque não se pode impor uma singularidade nesses índices.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Os nomes das colunas são minúsculos por Azure Stream Analytics
Ao utilizar o nível de compatibilidade original (1.0), o Azure Stream Analytics costumava mudar os nomes das colunas para minúscula. Este comportamento foi corrigido em níveis de compatibilidade posteriores. Para preservar o caso, aconselhamos os clientes a mudarem-se para o nível de compatibilidade 1.1 e posterior. Pode encontrar mais informações sobre o nível de [compatibilidade para trabalhos de Azure Stream Analytics.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)

## <a name="get-help"></a>Obter ajuda

Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
