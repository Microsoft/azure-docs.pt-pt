---
title: Saídas de Troubleshoot Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas as suas ligações de saída em empregos de Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364576"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Saídas de Troubleshoot Azure Stream Analytics

Esta página descreve problemas comuns com ligações de saída e como resolver problemas e endereçá-los.

## <a name="output-not-produced-by-job"></a>Saída não produzida por trabalho
1.  Verifique a conectividade com as saídas utilizando o botão **de ligação** de teste para cada saída.

2.  Veja as [**métricas de monitorização**](stream-analytics-monitoring.md) no separador **Monitor.** Como os valores são agregados, as métricas são adiadas por alguns minutos.
    - Se input events > 0, o trabalho é capaz de ler os dados de entrada. Se os Eventos de Entrada não forem > 0, então:
      - Para ver se a fonte de dados tem dados válidos, verifique-os utilizando o [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Este cheque aplica-se se o trabalho estiver a usar o Event Hub como entrada.
      - Verifique se o formato de serialização de dados e a codificação de dados são como esperado.
      - Se o trabalho estiver a usar um Centro de Eventos, verifique se o corpo da mensagem é *nulo*.

    - Se erros de conversão de dados > 0 e escalada, o seguinte pode ser verdade:
      - O evento de saída não está em conformidade com o esquema do afundador-alvo.
      - O esquema do evento pode não corresponder ao esquema definido ou esperado dos eventos na consulta.
      - Os tipos de dados de alguns dos campos do evento podem não corresponder às expectativas.

    - Se o Runtime Errors > 0 significa que o trabalho pode receber os dados, mas está a gerar erros durante o processamento da consulta.
      - Para encontrar os erros, aceda aos [Registos de Auditoria](../azure-resource-manager/management/view-activity-logs.md) e filtre no estado *falhado.*

    - Se InputEvents > 0 e OutputEvents = 0, significa que um dos seguintes é verdadeiro:
      - O processamento da consulta resultou em zero eventos de saída.
      - Os eventos ou os seus campos podem estar mal formados, resultando em saída zero após o processamento de consultas.
      - O trabalho não foi capaz de empurrar os dados para o lavatório de saída por razões de conectividade ou autenticação.

    - Em todos os casos de erro anteriormente mencionados, as mensagens de registo de operações explicam detalhes adicionais (incluindo o que está a acontecer), exceto nos casos em que a lógica da consulta filtrava todos os eventos. Se o processamento de vários eventos gerar erros, o Stream Analytics regista as três primeiras mensagens de erro do mesmo tipo dentro de 10 minutos para os registos de Operações. Em seguida, suprime erros idênticos adicionais com uma mensagem que diz "Os erros estão a acontecer muito rapidamente, estes estão a ser suprimidos."

## <a name="job-output-is-delayed"></a>A saída de emprego está atrasada

### <a name="first-output-is-delayed"></a>Saída primeiro é atrasada
Quando é iniciado um trabalho do Stream Analytics, os eventos de entrada são lidos, mas pode haver um atraso na produção da saída, em determinadas circunstâncias.

Valores de tempo grande em elementos de consulta temporal podem contribuir com o atraso de saída. Para produzir o resultado correto durante as janelas de tempo grande, a tarefa de transmissão em fluxo é iniciado, lendo dados a partir da mais recente possível de tempo (até sete dias atrás) para preencher a janela de tempo. Durante esse período, nenhuma saída é produzida até que a leitura catch-up de eventos de entrada pendentes esteja concluída. Esse problema pode surgir quando o sistema atualiza as tarefas de transmissão em fluxo, portanto, reiniciar a tarefa. Essas atualizações ocorram, geralmente, uma vez cada dois meses.

Por conseguinte, utilize critério ao conceber a sua consulta do Stream Analytics. Se utilizar uma janela de tempo grande (mais de várias horas, até sete dias) para elementos temporais na sintaxe de consulta da tarefa, ele pode levar a um atraso na saída primeiro quando a tarefa é iniciada ou reiniciada.  

Uma atenuação para esse tipo de atraso de saída primeira é usar técnicas de paralelização de consultas (criação de partições de dados) ou adicionar mais unidades de transmissão em fluxo de mensagens em fila para melhorar o débito até que a tarefa de captura a cópia de segurança.  Para mais informações, consulte [considerações ao criar empregos stream analytics](stream-analytics-concepts-checkpoint-replay.md)

Estes fatores que afetam a pontualidade do primeiro resultado que é gerado:

1. Utilização de agregados em janelas (grupo por de em cascata, saltos e deslizante windows)
   - Para em cascata ou saltos agregados de janela, os resultados são gerados no final do período de tempo de janela.
   - Para uma janela deslizante, os resultados são gerados quando um evento entra ou sai da janela deslizante.
   - Se estiver a planear utilizar o tamanho da janela grandes (> 1 hora), é melhor escolher a janela de salto que remonte há ou deslizante para que pode ver a saída com mais frequência.

2. Utilização de junções temporais (associação com DATEDIFF)
   - Correspondências são geradas, assim como quando chegam a ambos os lados dos eventos correspondentes.
   - Dados que não tem uma correspondência (LEFT OUTER JOIN) são gerados no fim da janela DATEDIFF em relação a cada evento no lado esquerdo.

3. Utilização de funções de análise temporais (ISFIRST, LAST e desfasamento com duração de limite)
   - Para funções de análise, a saída é gerada para cada evento, não existe nenhum atraso.

### <a name="output-falls-behind"></a>Saída se situe atrás
Durante o funcionamento normal do trabalho, se achar que a saída da tarefa é atrasado (latência mais e mais tempo), pode identificar as causas de raiz, examinando esses fatores:
- Se está limitado o jusante sink
- Se está limitada a origem a montante
- Se a lógica de processamento da consulta é a computação intensiva

Para ver esses detalhes, no portal Azure, selecione o trabalho de streaming e selecione o **diagrama**de Trabalho . Para cada entrada, há um por métrica de eventos de registo de segurança de partição. Se a aumentar a métrica de eventos do registo de segurança, é um indicador de que os recursos do sistema são restringidos. Potencialmente que é devido a limitação de sink de saída ou elevada da CPU. Para obter mais informações sobre a utilização do diagrama de trabalho, consulte a [depuração orientada por dados utilizando o diagrama](stream-analytics-job-diagram-with-metrics.md)de trabalho .

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação chave com saída de base de dados Azure SQL

Ao configurar a base de dados SQL do Azure como saída para uma tarefa do Stream Analytics, em massa inserir registros na tabela de destino. Em geral, a análise de fluxo Azure garante [pelo menos uma vez](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) que a entrega ao sumidouro de saída, ainda se pode conseguir [exatamente uma vez a entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) à saída SQL quando a tabela SQL tem uma restrição única definida.

Depois de restrições de chave exclusivas são configuradas na tabela de SQL e existem registos duplicados, sendo inseridos na tabela SQL, o Azure Stream Analytics remove o registo duplicado. Ele divide os dados em lotes e recursivamente inserir os lotes até que seja encontrado um único registo duplicado. Se a tarefa de transmissão em fluxo tem um número considerável de linhas duplicadas, esta divisão e inserir o processo deve ignorar os duplicados individualmente, que é menos eficiente e demorado. Se vir várias mensagens de aviso de violação de chave no seu registo de atividades dentro de última hora, é provável que a saída SQL está diminuindo o trabalho inteiro.

Para resolver este problema, deve [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação da chave, permitindo a opção IGNORE_DUP_KEY. Ativar esta opção permite que os valores duplicados ignorada pelo SQL durante inserções em massa e do SQL Azure simplesmente gera uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produzir mais erros de violação de chave primária.

Quando configurar IGNORE_DUP_KEY para vários tipos de índices, tenha em atenção as seguintes observações:

* Não é possível definir IGNORE_DUP_KEY numa chave primária ou uma restrição exclusiva que utilize ALTER INDEX, terá de remover e recriar o índice.  
* Pode definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição de chave primária/recursos exclusivos e criados com a definição de índice ou CREATE INDEX.  
* IGNORE_DUP_KEY não se aplica a índices de arquivo de colunas, uma vez que é possível impor a exclusividade nesses índices.  

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
