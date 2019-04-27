---
title: Resolver problemas relacionados com o Azure Stream Analytics saídas
description: Este artigo descreve técnicas para resolver problemas de suas ligações de saída em tarefas do Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 92cb427149e6e6cbddfb96c6e4488017641e6482
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761748"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Resolver problemas relacionados com o Azure Stream Analytics saídas

Esta página descreve problemas comuns com as ligações de saída e como solucionar problemas e resolvê-los.

## <a name="output-not-produced-by-job"></a>Saída não são produzida por tarefa 
1.  Verificar a conectividade saídas ao utilizar o **Testar ligação** botão para cada resultado.

2.  Examinar [ **métricas de monitorização** ](stream-analytics-monitoring.md) sobre o **Monitor** separador. Uma vez que os valores são agregados, as métricas são atrasadas por alguns minutos.
    - Se eventos de entrada > 0, a tarefa é capaz de ler dados de entrada. Se os eventos de entrada não > 0, em seguida:
      - Para ver se a origem de dados tem dados válidos, verifique-lo usando [Explorador do Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Esta verificação aplica-se se a tarefa estiver a utilizar o Hub de eventos como entrada.
      - Verifique se o formato de serialização de dados e, em seguida, codificação de dados são conforme o esperado.
      - Se a tarefa está a utilizar um Hub de eventos, verifique se o corpo da mensagem está *nulo*.
      
    - Se os erros de conversão de dados > 0 e aumentarem, poderá acontecer o seguinte:
      - O evento de saída não obedece ao esquema do coletor de destino. 
      - O esquema de eventos pode não corresponder ao esquema definido ou esperado dos eventos na consulta.
      - Os tipos de dados de alguns dos campos de eventos podem não corresponder às expetativas.
      
    - Se os erros de tempo de execução > 0, significa que a tarefa pode receber os dados, mas está a gerar erros ao processar a consulta.
      - Para localizar os erros, vá para o [registos de auditoria](../azure-resource-manager/resource-group-audit.md) e filtre *falha* estado.
      
    - Se InputEvents > 0 e OutputEvents = 0, isso significa que um dos seguintes é verdadeira:
      - O processamento da consulta resultou em zero eventos de saída.
      - Eventos ou os respetivos campos a poderão estar incorreto, resultando em zero saídas após o processamento de consulta.
      - A tarefa não foi possível enviar dados para o sink de saída por motivos de conectividade ou autenticação.
      
    - Em todos os casos de erro mencionado anteriormente, as mensagens de registo de operações explicam os detalhes adicionais (incluindo o que está a acontecer), exceto em casos em que a lógica de consulta filtrou todos os eventos. Se o processamento de vários eventos gerar erros, o Stream Analytics regista as primeiras mensagens de três erro do mesmo tipo em 10 minutos nos registos de operações. Em seguida, suprime os erros idênticos adicionais com uma mensagem que diz "Erros estavam a acontecer demasiado depressa, que estes estão a ser suprimidos."
    
## <a name="job-output-is-delayed"></a>Resultado da tarefa é atrasado

### <a name="first-output-is-delayed"></a>Saída primeiro é atrasada
Quando uma tarefa do Stream Analytics é iniciada, os eventos de entrada são lidas, mas pode haver um atraso na saída a ser produzida em determinadas circunstâncias.

Valores de tempo grande em elementos de consulta temporal podem contribuir com o atraso de saída. Para produzir o resultado correto durante as janelas de tempo grande, a tarefa de transmissão em fluxo é iniciado, lendo dados a partir da mais recente possível de tempo (até sete dias atrás) para preencher a janela de tempo. Durante esse período, nenhuma saída é produzida até que a leitura catch-up de eventos de entrada pendentes esteja concluída. Esse problema pode surgir quando o sistema atualiza as tarefas de transmissão em fluxo, portanto, reiniciar a tarefa. Essas atualizações ocorram, geralmente, uma vez cada dois meses. 

Por conseguinte, utilize critério ao conceber a sua consulta do Stream Analytics. Se utilizar uma janela de tempo grande (mais de várias horas, até sete dias) para elementos temporais na sintaxe de consulta da tarefa, ele pode levar a um atraso na saída primeiro quando a tarefa é iniciada ou reiniciada.  

Uma atenuação para esse tipo de atraso de saída primeira é usar técnicas de paralelização de consultas (criação de partições de dados) ou adicionar mais unidades de transmissão em fluxo de mensagens em fila para melhorar o débito até que a tarefa de captura a cópia de segurança.  Para obter mais informações, consulte [considerações ao criar tarefas do Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

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

Para ver os detalhes, no portal do Azure, selecione a tarefa de transmissão em fluxo e selecione o **diagrama de tarefas**. Para cada entrada, há um por métrica de eventos de registo de segurança de partição. Se a aumentar a métrica de eventos do registo de segurança, é um indicador de que os recursos do sistema são restringidos. Potencialmente que é devido a limitação de sink de saída ou elevada da CPU. Para obter mais informações sobre como utilizar o diagrama de tarefas, consulte [condicionada por dados de depuração usando o diagrama de tarefas](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação de chave com a saída de base de dados do Azure SQL

Ao configurar a base de dados SQL do Azure como saída para uma tarefa do Stream Analytics, em massa inserir registros na tabela de destino. Em geral, do Azure stream analytics garante [, pelo menos, uma entrega]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) para o sink de saída, uma ainda pode [obter exatamente-uma entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para SQL de saída quando a tabela SQL tem uma restrição exclusiva definida. 

Depois de restrições de chave exclusivas são configuradas na tabela de SQL e existem registos duplicados, sendo inseridos na tabela SQL, o Azure Stream Analytics remove o registo duplicado. Ele divide os dados em lotes e recursivamente inserir os lotes até que seja encontrado um único registo duplicado. Se a tarefa de transmissão em fluxo tem um número considerável de linhas duplicadas, esta divisão e inserir o processo deve ignorar os duplicados individualmente, que é menos eficiente e demorado. Se vir várias mensagens de aviso de violação de chave no seu registo de atividades dentro de última hora, é provável que a saída SQL está diminuindo o trabalho inteiro. 

Para resolver este problema, deve [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação de chave ao ativar a opção IGNORE_DUP_KEY. Ativar esta opção permite que os valores duplicados ignorada pelo SQL durante inserções em massa e do SQL Azure simplesmente gera uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produzir mais erros de violação de chave primária.

Quando configurar IGNORE_DUP_KEY para vários tipos de índices, tenha em atenção as seguintes observações:

* Não é possível definir IGNORE_DUP_KEY numa chave primária ou uma restrição exclusiva que utilize ALTER INDEX, terá de remover e recriar o índice.  
* Pode definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição de chave primária/recursos exclusivos e criados com a definição de índice ou CREATE INDEX.  
* IGNORE_DUP_KEY não se aplica a índices de arquivo de colunas, uma vez que é possível impor a exclusividade nesses índices.  

## <a name="get-help"></a>Obter ajuda

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
