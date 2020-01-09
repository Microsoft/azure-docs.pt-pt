---
title: Solucionar problemas Azure Stream Analytics saídas
description: Este artigo descreve técnicas para solucionar problemas de conexões de saída em trabalhos Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 65d01c5c4dd852cb424c75f170ce52156f1633cc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354113"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Solucionar problemas Azure Stream Analytics saídas

Esta página descreve problemas comuns com conexões de saída e como solucioná-los e solucioná-los.

## <a name="output-not-produced-by-job"></a>Saída não produzida pelo trabalho 
1.  Verifique a conectividade com as saídas usando o botão **testar conexão** para cada saída.

2.  Examine as [**métricas de monitoramento**](stream-analytics-monitoring.md) na guia **Monitor** . Como os valores são agregados, as métricas são atrasadas em alguns minutos.
    - Se os eventos de entrada > 0, o trabalho será capaz de ler os dados de entrada. Se os eventos de entrada não forem > 0, então:
      - Para ver se a fonte de dados tem dados válidos, verifique-os usando o [Gerenciador do barramento de serviço](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Essa verificação se aplica se o trabalho estiver usando o Hub de eventos como entrada.
      - Verifique se o formato de serialização de dados e a codificação de dados estão conforme o esperado.
      - Se o trabalho estiver usando um hub de eventos, verifique se o corpo da mensagem é *nulo*.
      
    - Se os erros de conversão de dados > 0 e escalada, o seguinte pode ser verdadeiro:
      - O evento de saída não está de acordo com o esquema do coletor de destino. 
      - O esquema de evento pode não corresponder ao esquema definido ou esperado dos eventos na consulta.
      - Os tipos de texto de alguns dos campos no evento podem não corresponder às expectativas.
      
    - Se os erros de tempo de execução > 0, isso significa que o trabalho pode receber os dados, mas está gerando erros durante o processamento da consulta.
      - Para localizar os erros, vá para os [logs de auditoria](../azure-resource-manager/resource-group-audit.md) e filtre em status de *falha* .
      
    - Se InputEvents > 0 e OutputEvents = 0, isso significa que uma das seguintes opções é verdadeira:
      - O processamento da consulta resultou em zero eventos de saída.
      - Os eventos ou seus campos podem estar malformados, resultando na saída zero após o processamento da consulta.
      - O trabalho não pôde enviar dados por push para o coletor de saída por motivos de conectividade ou autenticação.
      
    - Em todos os casos de erro mencionados anteriormente, as mensagens de log de operações explicam detalhes adicionais (incluindo o que está acontecendo), exceto nos casos em que a lógica de consulta filtrou todos os eventos. Se o processamento de vários eventos gerar erros, Stream Analytics registrará em log as três primeiras mensagens de erro do mesmo tipo dentro de 10 minutos nos logs de operações. Em seguida, ele suprime erros idênticos adicionais com uma mensagem que lê "os erros estão ocorrendo muito rapidamente, eles estão sendo suprimidos".
    
## <a name="job-output-is-delayed"></a>A saída do trabalho está atrasada

### <a name="first-output-is-delayed"></a>Saída primeiro é atrasada
Quando é iniciado um trabalho do Stream Analytics, os eventos de entrada são lidos, mas pode haver um atraso na produção da saída, em determinadas circunstâncias.

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação de chave com saída do banco de dados SQL do Azure

Ao configurar a base de dados SQL do Azure como saída para uma tarefa do Stream Analytics, em massa inserir registros na tabela de destino. Em geral, do Azure stream analytics garante [, pelo menos, uma entrega](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) para o sink de saída, uma ainda pode [obter exatamente-uma entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para SQL de saída quando a tabela SQL tem uma restrição exclusiva definida. 

Depois de restrições de chave exclusivas são configuradas na tabela de SQL e existem registos duplicados, sendo inseridos na tabela SQL, o Azure Stream Analytics remove o registo duplicado. Ele divide os dados em lotes e recursivamente inserir os lotes até que seja encontrado um único registo duplicado. Se a tarefa de transmissão em fluxo tem um número considerável de linhas duplicadas, esta divisão e inserir o processo deve ignorar os duplicados individualmente, que é menos eficiente e demorado. Se vir várias mensagens de aviso de violação de chave no seu registo de atividades dentro de última hora, é provável que a saída SQL está diminuindo o trabalho inteiro. 

Para resolver este problema, deve [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação de chave ao ativar a opção IGNORE_DUP_KEY. Ativar esta opção permite que os valores duplicados ignorada pelo SQL durante inserções em massa e do SQL Azure simplesmente gera uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produzir mais erros de violação de chave primária.

Quando configurar IGNORE_DUP_KEY para vários tipos de índices, tenha em atenção as seguintes observações:

* Não é possível definir IGNORE_DUP_KEY numa chave primária ou uma restrição exclusiva que utilize ALTER INDEX, terá de remover e recriar o índice.  
* Pode definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição de chave primária/recursos exclusivos e criados com a definição de índice ou CREATE INDEX.  
* IGNORE_DUP_KEY não se aplica a índices de arquivo de colunas, uma vez que é possível impor a exclusividade nesses índices.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Os nomes de coluna são minúsculos por Azure Stream Analytics
Ao usar o nível de compatibilidade original (1,0), Azure Stream Analytics usado para alterar nomes de coluna para letras minúsculas. Esse comportamento foi corrigido em níveis de compatibilidade posteriores. Para preservar o caso, aconselhamos os clientes a migrar para o nível de compatibilidade 1,1 e posterior. Você pode encontrar mais informações sobre o [nível de compatibilidade para trabalhos de Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Obter ajuda

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
