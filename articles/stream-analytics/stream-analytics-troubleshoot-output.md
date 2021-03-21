---
title: Resolução de problemas Saídas Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas nas suas ligações de saída em trabalhos Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 02a3a7ad73bf0434a215c5ab7a6e89c299e9518b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019861"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Resolução de problemas Saídas Azure Stream Analytics

Este artigo descreve problemas comuns com as ligações de saída do Azure Stream Analytics e como resolver problemas de saída. Muitas etapas de resolução de problemas requerem recursos e outros registos de diagnóstico para o seu trabalho stream Analytics. Se não tiver registos de recursos ativados, consulte [o Troubleshoot Azure Stream Analytics utilizando registos de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>O trabalho não produz produção

1. Verifique a conectividade com as saídas utilizando o botão **de Ligação de Teste** para cada saída.
1. Veja [as métricas de monitorização](stream-analytics-monitoring.md) na conta **monitor.** Como os valores são agregados, as métricas são adiadas por alguns minutos.

   * Se o valor **dos Eventos de Entrada** for superior a zero, o trabalho pode ler os dados de entrada. Se o valor **dos Eventos de Entrada** não for maior do que zero, há um problema com a entrada do trabalho. Consulte [as ligações de entrada de resolução de problemas](stream-analytics-troubleshoot-input.md) para obter mais informações. Se o seu trabalho tiver entrada de dados de referência, aplique a divisão por nome lógico ao olhar para a métrica **de Eventos de Entrada.** Se não houver eventos de entrada apenas a partir dos seus dados de referência, então provavelmente significa que esta fonte de entrada não foi configurada corretamente para obter o conjunto de dados de referência certo.
   * Se o valor de **Erros de Conversão de Dados** for superior a zero e subir, consulte [os erros de dados do Azure Stream Analytics](data-errors.md) para informações detalhadas sobre erros de conversão de dados.
   * Se o valor **de Erros de Execução** for superior a zero, o seu trabalho recebe dados mas gera erros durante o processamento da consulta. Para encontrar os erros, vá aos [registos](../azure-resource-manager/management/view-activity-logs.md)de auditoria e, em seguida, filtre no estado **falhado.**
   * Se o valor **dos Eventos de Entrada** for superior a zero e o valor de **Eventos de Saída** for igual a zero, uma das seguintes declarações é verdadeira:
      * O processamento de consultas resultou em eventos de saída zero.
      * Eventos ou campos podem ser mal formados, resultando numa saída zero após o processamento de consulta.
      * O trabalho não foi capaz de empurrar os dados para o lavatório de saída por razões de conectividade ou autenticação.

   As mensagens de registo de operações explicam detalhes adicionais, incluindo o que está a acontecer, exceto nos casos em que a lógica de consulta filtra todos os eventos. Se o processamento de vários eventos gerar erros, os erros agregam-se a cada 10 minutos.

## <a name="the-first-output-is-delayed"></a>A primeira saída está atrasada

Quando um trabalho de Stream Analytics começa, os eventos de entrada são lidos. Mas, pode haver um atraso na produção, em certas circunstâncias.

Os valores de tempo elevado em elementos de consulta temporal podem contribuir para o atraso de saída. Para produzir a saída correta em grandes janelas de tempo, o trabalho de streaming lê dados da última vez possível para preencher a janela de tempo. Os dados podem ser até sete dias passados. Nenhuma saída produz até que os eventos de entrada pendentes sejam lidos. Este problema pode surgir quando o sistema atualiza os trabalhos de streaming. Quando uma atualização ocorre, o trabalho recomeça. Tais atualizações geralmente ocorrem uma vez a cada dois meses.

Utilize discrição ao conceber a sua consulta Stream Analytics. Se utilizar uma grande janela de tempo para elementos temporais na sintaxe de consulta do trabalho, pode levar a um atraso na primeira saída quando o trabalho começa ou reinicia. Mais de várias horas, até sete dias, é considerada uma grande janela de tempo.

Uma mitigação para este tipo de primeiro atraso de saída é usar técnicas de paralelização de consulta, como a divisão dos dados. Ou, pode adicionar mais Unidades de Streaming para melhorar a produção até que o trabalho se recupere.  Para obter mais informações, consulte [Considerações ao criar trabalhos stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

Estes fatores afetam a oportunidade da primeira saída:

* Utilização de agregados com janelas, como uma cláusula GROUP BY de tumbling, salto e janelas deslizantes:

  * Para os agregados de janelas de tropeçar ou saltar, os resultados geram no final do período de tempo da janela.
  * Para uma janela deslizante, os resultados geram quando um evento entra ou sai da janela deslizante.
  * Se você está planejando usar um grande tamanho de janela, como mais de uma hora, é melhor escolher uma janela pulando ou deslizando. Estes tipos de janelas permitem-lhe ver a saída com mais frequência.

* O uso de uniões temporais, tais como JOIN com DATEDIFF:
  * Os jogos geram assim que ambos os lados dos eventos combinados chegam.
  * Os dados que carecem de uma correspondência, como LEFT OUTER JOIN, são gerados no final da janela DATEDIFF, para cada evento no lado esquerdo.

* Utilização de funções analíticas temporais, tais como ISFIRST, LAST e LAG com DURAÇÃO LIMITE:
  * Para funções analíticas, a saída é gerada para cada evento. Não há atrasos.

## <a name="the-output-falls-behind"></a>A saída fica para trás

Durante o funcionamento normal de um trabalho, a saída pode ter períodos de latência mais longos e longos. Se a saída ficar para trás assim, pode identificar as causas da raiz examinando os seguintes fatores:

* Se a pia a jusante está estrangulada
* Se a fonte a montante é estrangulada
* Se a lógica de processamento na consulta é computacional intensiva

Para ver os detalhes de saída, selecione o trabalho de streaming no portal Azure e, em seguida, selecione **o diagrama de trabalho**. Para cada entrada, há uma métrica de evento por partição. Se a métrica continuar a aumentar, é um indicador de que os recursos do sistema estão limitados. O aumento deve-se potencialmente ao estrangulamento do lavatório de saída ou ao elevado uso do CPU. Para obter mais informações, consulte [a depuração orientada para dados utilizando o diagrama de trabalho](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação chave com saída da Base de Dados Azure SQL

Quando configura uma base de dados Azure SQL como saída para um trabalho stream Analytics, insere registos na tabela de destino. Em geral, o Azure Stream Analytics garante [pelo menos uma vez](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) a entrega ao lavatório de saída. Você ainda pode [obter exatamente uma vez entrega]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para uma saída SQL quando uma mesa SQL tem uma restrição única definida.

Quando configurar restrições únicas na tabela SQL, o Azure Stream Analytics remove registos duplicados. Divide os dados em lotes e insere novamente os lotes até que seja encontrado um único registo duplicado. O processo de divisão e inserção ignora os duplicados um de cada vez. Para um trabalho de streaming que tem muitas linhas duplicadas, o processo é ineficiente e demorado. Se vir várias mensagens de aviso de violação chave no seu registo de Atividade durante a hora anterior, é provável que a sua saída SQL esteja a abrandar todo o trabalho.

Para resolver este problema, [configuure o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação chave, permitindo que a opção IGNORE_DUP_KEY. Esta opção permite que o SQL ignore valores duplicados durante as inserções a granel. A base de dados Azure SQL simplesmente produz uma mensagem de aviso em vez de um erro. Como resultado, o Azure Stream Analytics já não produz erros de violação primários.

Note as seguintes observações ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Não é possível definir IGNORE_DUP_KEY numa chave primária ou numa restrição única que utiliza o ÍNDICE ALTER. Tens de baixar o índice e recriá-lo.  
* Pode definir IGNORE_DUP_KEY utilizando o ÍNDICE ALTER para um índice único. Este caso é diferente de uma restrição CHAVE/UNIQUE PRIMÁRIO e é criado utilizando uma definição DE ÍNDICE OU ÍNDICE CREATE.  
* A opção IGNORE_DUP_KEY não se aplica aos índices de lojas de colunas porque não se pode impor a singularidade neles.

## <a name="sql-output-retry-logic"></a>Lógica de relíndi da saída SQL

Quando um trabalho stream Analytics com saída SQL recebe o primeiro lote de eventos, ocorrem os seguintes passos:

1. O trabalho tenta ligar-se ao SQL.
2. O trabalho vai buscar o esquema da mesa de destino.
3. O trabalho valida nomes e tipos de colunas contra o esquema da tabela de destino.
4. O trabalho prepara uma tabela de dados na memória a partir dos registos de saída do lote.
5. O trabalho escreve a tabela de dados para SQL usando [a BulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver).

Durante estes passos, a saída SQL pode experimentar os seguintes tipos de erros:

* [Erros transitórios](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others) que são novamente julgados usando uma estratégia de recuo exponencial. O intervalo mínimo de retagem depende do código de erro individual, mas os intervalos são tipicamente inferiores a 60 segundos. O limite superior pode ser no máximo cinco minutos. 

   [Falhas de login](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) e [problemas de firewall](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues) são novamente experimentados pelo menos 5 minutos após a tentativa anterior e são novamente julgados até que tenham sucesso.

* Os erros de dados, tais como erros de casting e violações de restrições de esquemas, são tratados com a política de erro de saída. Estes erros são tratados redando os lotes de divisão binária até que o registo individual que causa o erro seja manuseado por salto ou reagem. A violação da restrição de chave única primária é [sempre tratada.](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)

* Erros não transitórios podem ocorrer quando existem problemas de serviço SQL ou defeitos de código internos. Por exemplo, quando erros como (Código 1132) Piscina Elástica que atingem o seu limite de armazenamento, as retró quaisquer pessoas não resolvem o erro. Nestes cenários, o trabalho stream Analytics experimenta [a degradação.](job-states.md)
* `BulkCopy` os intervalos podem acontecer durante `BulkCopy` o passo 5. `BulkCopy` pode experimentar intervalos de operação ocasionalmente. O tempo limite mínimo configurado é de cinco minutos e duplica quando atingido consecutivamente.
Uma vez que o tempo limite seja superior a 15 minutos, o tamanho máximo do lote `BulkCopy` é reduzido para metade até que 100 eventos por lote sejam deixados.

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Os nomes das colunas são minúsculos em Azure Stream Analytics (1.0)

Ao utilizar o nível de compatibilidade original (1.0), o Azure Stream Analytics altera os nomes das colunas para minúsculas. Este comportamento foi corrigido em níveis de compatibilidade posteriores. Para preservar o caso, passe para o nível de compatibilidade 1.1 ou posterior. Para obter mais informações, consulte [o nível de compatibilidade para trabalhos stream analytics](./stream-analytics-compatibility-level.md).

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de idioma de consulta de azure stream](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de Azure Stream Analytics REST API](/rest/api/streamanalytics/)