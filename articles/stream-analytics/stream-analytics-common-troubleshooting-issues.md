---
title: Problemas comuns para resolver problemas no Azure Stream Analytics
description: Este artigo descreve vários problemas comuns no Azure Stream Analytics e passos para resolver esse problema.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521480"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemas comuns no Stream Analytics e passos para resolver problemas

## <a name="troubleshoot-malformed-input-events"></a>Resolver problemas de eventos de entrada com formato incorreto

 Problemas de serialização são causados quando o fluxo de entrada da sua tarefa do Stream Analytics contém mensagens com formato incorreto. Por exemplo, uma mensagem incorretamente formada pode ser causado por um parêntesis de em falta ou uma chave em falta num objeto JSON ou incorreto formato de carimbo de data / num campo de tempo de tempo. 
 
 Quando uma tarefa do Stream Analytics recebe uma mensagem incorretamente formada de entrada, ele ignora a mensagem e notifica o utilizador com um aviso. Um símbolo de aviso é apresentado no **entradas** mosaico da sua tarefa do Stream Analytics (início de sessão este aviso existe, desde que a tarefa está no estado de execução):

![Entradas no mosaico no dashboard do Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Para obter mais informações, ative os registos de diagnóstico para ver os detalhes do aviso. Para eventos de entrada com formato incorreto, os registos de execução contém uma entrada com a mensagem que é semelhante a: "Mensagem: Não foi possível anular a serialização do evento ou eventos entrado de recurso \<URI de blob > como json ". 

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

1. Navegue para o mosaico de entrada e clique para ver os avisos.

2. O mosaico de detalhes de entrada apresenta um conjunto de avisos com detalhes sobre o problema. Segue-se uma mensagem de aviso de exemplo, a mensagem de aviso mostra a partição, o deslocamento e a números de sequência em que há dados JSON com formato incorreto. 

   ![Mensagem de aviso de entrada com o deslocamento](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Para obter os dados JSON que tem um formato incorreto, execute o código de CheckMalformedEvents.cs. Neste exemplo está disponível na [repositório de exemplos do GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código leituras o ID de partição, deslocamento e imprime os dados que estão localizados no deslocamento. 

4. Depois de ler os dados, pode analisar e corrigir o formato de serialização.

5. Também pode [ler eventos a partir de um IoT Hub com o Explorador do Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Saída atrasada

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

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Tratar registos duplicados na saída de base de dados do Azure SQL

Ao configurar a base de dados SQL do Azure como saída para uma tarefa do Stream Analytics, em massa inserir registros na tabela de destino. Em geral, do Azure stream analytics garante [, pelo menos, uma entrega]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) para o sink de saída, uma ainda pode [obter exatamente-uma entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para SQL de saída quando a tabela SQL tem uma restrição exclusiva definida. 

Depois de restrições de chave exclusivas são configuradas na tabela de SQL e existem registos duplicados, sendo inseridos na tabela SQL, o Azure Stream Analytics remove o registo duplicado. Ele divide os dados em lotes e recursivamente inserir os lotes até que seja encontrado um único registo duplicado. Se a tarefa de transmissão em fluxo tem um número considerável de linhas duplicadas, esta divisão e inserir o processo deve ignorar os duplicados individualmente, que é menos eficiente e demorado. Se vir várias mensagens de aviso de violação de chave no seu registo de atividades dentro de última hora, é provável que a saída SQL está diminuindo o trabalho inteiro. 

Para resolver este problema, deve [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação de chave ao ativar a opção IGNORE_DUP_KEY. Ativar esta opção permite que os valores duplicados ignorada pelo SQL durante inserções em massa e do SQL Azure simplesmente gera uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produzir mais erros de violação de chave primária.

Quando configurar IGNORE_DUP_KEY para vários tipos de índices, tenha em atenção as seguintes observações:

* Não é possível definir IGNORE_DUP_KEY numa chave primária ou uma restrição exclusiva que utilize ALTER INDEX, terá de remover e recriar o índice.  
* Pode definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição de chave primária/recursos exclusivos e criados com a definição de índice ou CREATE INDEX.  
* IGNORE_DUP_KEY não se aplica a índices de arquivo de colunas, uma vez que é possível impor a exclusividade nesses índices.  

## <a name="next-steps"></a>Passos Seguintes
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
