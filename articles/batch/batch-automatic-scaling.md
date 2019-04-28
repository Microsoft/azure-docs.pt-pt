---
title: Dimensionar automaticamente nós num conjunto do Azure Batch computação | Documentos da Microsoft
description: Ative dimensionamento automático num agrupamento de cloud ajustar dinamicamente o número de nós de computação no conjunto.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdc2cd8f2218d50aa49d6b4eab2800eb6c92d9c9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118116"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Criar uma fórmula de dimensionamento automática para dimensionar nós de computação de um conjunto do Batch

O Azure Batch pode dimensionar automaticamente conjuntos com base nos parâmetros definidos por si. Com o dimensionamento automático, Batch adiciona dinamicamente nós a um conjunto de aumento de exigências de tarefa e remove nós de computação, como reduzem. Economizará tempo e dinheiro ao ajustar automaticamente o número de nós de computação utilizada pela sua aplicação Batch. 

Ativar o dimensionamento automático num conjunto de nós de computação ao associá-lo um *fórmula de dimensionamento automático* que definir. O serviço Batch utiliza a fórmula de dimensionamento automático para determinar o número de nós de computação que são necessários para executar a sua carga de trabalho. Nós podem ser dedicados de nós de computação ou [nós de baixa prioridade](batch-low-pri-vms.md). Batch responde aos dados de métricas de serviço que são recolhidos periodicamente. Utilizando estes dados de métricas, o Batch ajusta o número de nós de computação no conjunto com base na sua fórmula e num intervalo configurável.

Pode ativar o dimensionamento automático quando é criado um conjunto, ou num conjunto existente. Também pode alterar uma fórmula existente num conjunto que está configurado para o dimensionamento automático. Batch permite-lhe para avaliar as suas fórmulas antes de atribuí-las aos agrupamentos de e para monitorizar o estado das execuções de dimensionamento automáticos.

Este artigo aborda as várias entidades que formam as suas fórmulas de dimensionamento automático, incluindo variáveis, operadores, operações e as funções. Vamos discutir como obter várias computação recursos e tarefas métricas em lote. Pode utilizar estas métricas para ajustar a contagem de nós do seu conjunto com base no estado de tarefa e utilização de recursos. Que, em seguida, descrevemos como construir uma fórmula e ativar o dimensionamento automático num conjunto com o REST do Batch e de APIs do .NET. Por fim, podemos finalizam com algumas fórmulas de exemplo.

> [!IMPORTANT]
> Quando cria uma conta do Batch, pode especificar a [configuração da conta](batch-api-basics.md#account), que determina se os conjuntos são alocados numa subscrição do serviço do Batch (predefinição) ou na sua subscrição de utilizador. Se tiver criado a sua conta do Batch com a configuração de serviço do Batch predefinido, em seguida, sua conta está limitada a um número máximo de núcleos que pode ser utilizado para processamento. O serviço Batch dimensiona nós de computação apenas até esse limite de núcleos. Por esse motivo, o serviço Batch pode não contactar com o número de destino de nós de computação especificado por uma fórmula de dimensionamento automático. Ver [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter informações sobre a visualização e aumentar as suas quotas de conta.
>
>Se criou a conta com a configuração de subscrição de utilizador, em seguida, sua conta partilhas na quota de núcleos da subscrição. Para obter mais informações, veja [Virtual Machines limits](../azure-subscription-service-limits.md#virtual-machines-limits) (Limites das Máquinas Virtuais), em [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md) (Limites, quotas e limitações das subscrições e serviços do Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Fórmulas de dimensionamento automáticas
Uma fórmula de dimensionamento automática é um valor de cadeia de caracteres que define que contém uma ou mais instruções. A fórmula de dimensionamento automático é atribuída a um agrupamento [autoScaleFormula] [ rest_autoscaleformula] elemento (REST do Batch) ou [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propriedade (.NET do Batch). O serviço Batch utiliza sua fórmula para determinar o número de destino de nós de computação no conjunto para o próximo intervalo de processamento. A cadeia de caracteres de fórmula não pode ter mais de 8 KB, pode incluir até 100 instruções que são separadas por ponto e vírgula e podem incluir quebras de linha e seus comentários.

Pode pensar fórmulas de dimensionamento automáticas como um dimensionamento automático do Batch "idioma". Instruções de fórmulas são expressões de formatação livre de mensagens em fila que podem incluir variáveis definidas pelo serviço (variáveis definidas pelo serviço Batch) e variáveis definidas pelo utilizador (variáveis definidos por si). Eles podem executar várias operações estes valores através da utilização de funções, operadores e tipos internos. Por exemplo, uma instrução poderá demorar a seguinte forma:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Fórmulas geralmente contêm várias instruções que realizam operações nos valores que são obtidos em instruções anteriores. Por exemplo, vamos obter primeiro um valor para `variable1`, em seguida, passá-lo para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua estas declarações na sua fórmula de dimensionamento automático para chegar um número de destino de nós de computação. Nós dedicados e nós de baixa prioridade têm suas próprias definições de destino, para que pode definir um destino para cada tipo de nó. Uma fórmula de dimensionamento automático pode incluir um valor de destino para nós dedicados, um valor de destino para nós de baixa prioridade ou ambos.

O número de destino de nós pode ser superior, inferior ou igual ao número atual de nós desse tipo no conjunto. Batch avalia a fórmula de dimensionamento automático de um conjunto num intervalo específico (consulte [intervalos de dimensionamento automático](#automatic-scaling-interval)). Batch ajusta o número de destino de cada tipo de nó no conjunto para o número que especifica de sua fórmula de dimensionamento automático no momento da avaliação.

### <a name="sample-autoscale-formula"></a>Fórmula de dimensionamento automático de exemplo

Eis um exemplo de uma fórmula de dimensionamento automático que pode ser ajustado para funcionar na maioria dos cenários. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` no exemplo a fórmula pode ser ajustada às suas necessidades. Esta fórmula dimensiona nós dedicados, mas pode ser modificada para aplicar a dimensionar nós de baixa prioridade também. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Com esta fórmula de dimensionamento automático, o conjunto inicialmente é criado com uma única VM. O `$PendingTasks` métrica define o número de tarefas que estão em execução ou em fila. A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e conjuntos de `$TargetDedicatedNodes` variável em conformidade. A fórmula garante que o número de destino de nós dedicados nunca excede 25 VMs. Conforme novas tarefas submetidas, o conjunto automaticamente cresce. Como tarefas são concluídas, as VMs se tornar um gratuito por um e a fórmula de dimensionamento automático diminui o conjunto.

## <a name="variables"></a>Variáveis
Pode utilizar ambas **definidas pelo serviço** e **definidas pelo utilizador** variáveis nas suas fórmulas de dimensionamento automático. As variáveis definidas pelo serviço estão incorporadas para o serviço Batch. Algumas variáveis definidas pelo serviço são leitura / escrita e alguns são só de leitura. As variáveis definidas pelo utilizador são variáveis que definir. A fórmula de exemplo mostrado na secção anterior, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço. Variáveis `startingNumberOfVMs` e `maxNumberofVMs` são variáveis definidas pelo utilizador.

> [!NOTE]
> As variáveis definidas pelo serviço sempre são precedidas por um sinal de dólar ($). Para as variáveis definidas pelo utilizador, o cifrão é opcional.
>
>

As tabelas seguintes mostram as variáveis de leitura / escrita e só de leitura que estão definidas pelo serviço Batch.

Pode obter e definir os valores destas variáveis definidas pelo serviço para gerir o número de nós de computação num conjunto:

| Variáveis definidas pelo serviço de leitura / escrita | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número de destino dedicado de nós de computação para o conjunto. O número de nós dedicado é especificado como destino porque um conjunto não pode alcançar sempre o número de nós pretendido. Por exemplo, se o número de nós dedicados de destino for modificado por uma edição de avaliação do dimensionamento automático, antes do conjunto atingiu o destino inicial, em seguida, o conjunto pode não alcançar o destino. <br /><br /> Um conjunto numa conta criada com a configuração do serviço Batch não pode alcançar seu destino, se o destino exceder uma quota de nó ou core de conta do Batch. Um conjunto numa conta criada com a configuração de subscrição de utilizador não poderá atingir seu destino se o destino exceder a quota de núcleo compartilhado para a subscrição.|
| $TargetLowPriorityNodes |O número de destino de baixa prioridade nós de computação para o conjunto. O número de nós de baixa prioridade é especificado como destino porque um conjunto não pode alcançar sempre o número de nós pretendido. Por exemplo, se o número de destino de nós de baixa prioridade é modificado por uma edição de avaliação do dimensionamento automático, antes do conjunto atingiu o destino inicial, em seguida, o conjunto pode não alcançar o destino. Um conjunto pode também não alcançar o destino se o destino exceder uma quota de nó ou core de conta do Batch. <br /><br /> Para obter mais informações sobre nós de computação de baixa prioridade, consulte [utilizar VMs de baixa prioridade no Batch (pré-visualização)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando nós de computação são removidos de um conjunto de mensagens em fila. Os valores possíveis são:<ul><li>**recolocação na fila**– termina tarefas imediatamente e os coloca na fila de tarefas para que eles são reagendados.<li>**terminar**– termina a tarefas imediatamente e remove-os da fila de tarefas.<li>**taskcompletion**– aguarda para atualmente em execução de tarefas a concluir e, em seguida, remove o nó do conjunto.<li>**retaineddata**– espera até que todos os locais retidos por tarefa dados no nó ser limpos antes de remover o nó do conjunto.</ul> |

Pode obter o valor destas variáveis definidas pelo serviço para fazer ajustes que se baseiam em métricas do serviço Batch:

| Variáveis definidas pelo serviço de só de leitura | Descrição |
| --- | --- |
| $CPUPercent |A percentagem média de utilização da CPU. |
| $WallClockSeconds |O número de segundos consumidos. |
| $MemoryBytes |O número médio de megabytes utilizados. |
| $DiskBytes |O número médio de gigabytes utilizado nos discos locais. |
| $DiskReadBytes |O número de bytes lidos. |
| $DiskWriteBytes |O número de bytes escritos. |
| $DiskReadOps |A contagem de operações de leitura do disco executadas. |
| $DiskWriteOps |Contagem de operações de escrita de disco executadas. |
| $NetworkInBytes |O número de bytes de entrada. |
| $NetworkOutBytes |O número de bytes de saída. |
| $SampleNodeCount |A contagem de nós de computação. |
| $ActiveTasks |O número de tarefas que estão prontos para execução, mas ainda não estiver executando. A contagem de $ActiveTasks inclui todas as tarefas que estão no Estado ativo e cujas dependências foram satisfeitas. Todas as tarefas que estão no Estado ativo, mas não foram satisfeitas cujas dependências estão excluídas da contagem $ActiveTasks.|
| $RunningTasks |O número de tarefas num Estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que foram concluídas com êxito. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicatedNodes |O número atual de dedicadas nós de computação. |
| $CurrentLowPriorityNodes |O número atual de baixa prioridade nós de computação, incluindo quaisquer nós que tenham sido suplantadas. |
| $PreemptedNodeCount | O número de nós no conjunto que estão num Estado suplantado. |

> [!TIP]
> As variáveis só de leitura, definidas pelo serviço, que são mostradas na tabela anterior são *objetos* que fornecem vários métodos para aceder a dados associados a cada um. Para obter mais informações, consulte [obter dados de exemplo](#getsampledata) mais adiante neste artigo.
>
>

## <a name="types"></a>Tipos
Esses tipos são suportados numa fórmula:

* double
* doubleVec
* doubleVecList
* string
* Timestamp – timestamp é uma estrutura composta que contém os seguintes membros:

  * ano
  * mês (1-12)
  * dia (1-31)
  * dia da semana (no formato de número; por exemplo, 1, de segunda)
  * hora (no formato de número de 24 horas; por exemplo, 13 significa 1 PM)
  * minuto (00 59)
  * segundo (00 59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operações
Estas operações são permitidas sobre os tipos que estão listados na secção anterior.

| Operação | Operadores suportados | Tipo de resultado |
| --- | --- | --- |
| duplo *operador* duplo |+, -, *, / |double |
| duplo *operador* timeinterval |* |timeinterval |
| doubleVec *operador* duplo |+, -, *, / |doubleVec |
| doubleVec *operador* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operador* duplo |*, / |timeinterval |
| TimeInterval *operador* timeinterval |+, - |timeinterval |
| TimeInterval *operador* timestamp |+ |timestamp |
| Timestamp *operador* timeinterval |+ |timestamp |
| Timestamp *operador* timestamp |- |timeinterval |
| *operador*duplo |-, ! |double |
| *operator*timeinterval |- |timeinterval |
| duplo *operador* duplo |<, <=, ==, >=, >, != |double |
| cadeia de caracteres *operador* cadeia |<, <=, ==, >=, >, != |double |
| Timestamp *operador* timestamp |<, <=, ==, >=, >, != |double |
| TimeInterval *operador* timeinterval |<, <=, ==, >=, >, != |double |
| duplo *operador* duplo |&&, &#124;&#124; |double |

Ao testar um valor de duplo com um operador Ternário (`double ? statement1 : statement2`), diferente de zero é **verdadeira**, e é de zero **falso**.

## <a name="functions"></a>Funções
Estes predefinidos **funções** estão disponíveis para utilização na definição de uma fórmula de dimensionamento automática.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| avg(doubleVecList) |double |Devolve o valor médio para todos os valores no doubleVecList. |
| len(doubleVecList) |double |Devolve o comprimento do vetor criada a partir do doubleVecList. |
| LG(Double) |double |Devolve o registo de base 2 do valor de duplo. |
| lg(doubleVecList) |doubleVec |Devolve o registo de component-wise base 2 do doubleVecList. Um vec(double) devem ser passados explicitamente para o parâmetro. Caso contrário, é assumida a versão de lg(double) duplo. |
| ln(Double) |double |Devolve o registo natural do double. |
| ln(doubleVecList) |doubleVec |Devolve o registo de component-wise base 2 do doubleVecList. Um vec(double) devem ser passados explicitamente para o parâmetro. Caso contrário, é assumida a versão de lg(double) duplo. |
| log(Double) |double |Devolve o registo de base 10 do double. |
| log(doubleVecList) |doubleVec |Devolve o registo de component-wise base 10 do doubleVecList. Um vec(double) devem ser passados explicitamente para o parâmetro de duplicata único. Caso contrário, é assumida a versão de log(double) duplo. |
| max(doubleVecList) |double |Devolve o valor máximo o doubleVecList. |
| min(doubleVecList) |double |Devolve o valor mínimo no doubleVecList. |
| norm(doubleVecList) |double |Devolve a norma de dois do vetor criada a partir do doubleVecList. |
| percentil (v doubleVec, double p) |double |Devolve o elemento de percentil do vetor v. |
| rand() |double |Devolve um valor aleatório entre 0,0 e 1,0. |
| range(doubleVecList) |double |Devolve a diferença entre os valores mínimo e máximo no doubleVecList. |
| std(doubleVecList) |double |Devolve o desvio-padrão de exemplo dos valores no doubleVecList. |
| stop() | |Deixa de avaliação da expressão dimensionamento automático. |
| sum(doubleVecList) |double |Devolve a soma de todos os componentes do doubleVecList. |
| time(string dateTime="") |timestamp |Devolve o carimbo de hora da hora atual se não existem parâmetros são transmitidos ou o carimbo de data / hora da cadeia de caracteres dateTime se ela é passada. Formatos suportados dateTime são W3C DTF e RFC 1123. |
| VAL (v doubleVec, double i) |double |Devolve o valor do elemento que se encontra localização i em vetor v, com um índice de início de zero. |

Algumas das funções que são descritas na tabela anterior podem aceitar uma lista como um argumento. A lista separada por vírgulas é qualquer combinação dos *duplo* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O *doubleVecList* valor é convertido para um único *doubleVec* antes de avaliação. Por exemplo, se `v = [1,2,3]`, em seguida, chamar `avg(v)` é equivalente a chamar `avg(1,2,3)`. A invocar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo
Fórmulas de dimensionamento automático agirem sobre dados de métricas (exemplos) que são fornecidos pelo serviço Batch. Uma fórmula aumenta ou diminui o tamanho do conjunto com base nos valores que obtém a partir do serviço. As variáveis definidas pelo serviço que foram descritas anteriormente são objetos que fornecem vários métodos para aceder aos dados que está associados esse objeto. Por exemplo, a expressão a seguir mostra um pedido para obter os últimos cinco minutos de utilização da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() |O `GetSample()` método retorna um vetor de amostras de dados.<br/><br/>Um exemplo é 30 segundos de dados de métricas. Em outras palavras, os exemplos são obtidos a cada 30 segundos. Mas, como indicado abaixo, existe um atraso entre quando um exemplo é recolhido e quando se encontra disponível como uma fórmula. Como tal, nem todos os exemplos para um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras para obter as mais recentes amostras que foram recolhidas.<br/><br/>`GetSample(1)` Devolve o último exemplo disponível. Como de métricas `$CPUPercent`, no entanto, isso não deve ser utilizado porque é impossível saber *quando* o exemplo foi recolhido. Poderá ser recente, ou, devido a problemas de sistema, ele pode ser muito mais antigo. É melhor em tais casos, para utilizar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para a recolha de dados de exemplo. Opcionalmente, também especifica a percentagem de exemplos que tem de estar disponível no período de tempo de pedido.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` retornaria 20 amostras se todos os exemplos nos últimos 10 minutos estão presentes no histórico de CPUPercent. Se não estava disponível no último minuto do histórico, no entanto, apenas 18 exemplos seriam retornados. Neste caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` não serviria porque apenas 90 por cento dos exemplos estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` serão bem-sucedidas.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para a recolha de dados, com uma hora de início e uma hora de fim.<br/><br/>Conforme mencionado acima, existe um atraso entre quando um exemplo é recolhido e quando se encontra disponível como uma fórmula. Considere este atraso quando utiliza o `GetSample` método. Consulte `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Devolve o período de amostras que foram executadas num conjunto de dados de exemplo históricas. |
| Count() |Devolve o número total de amostras no histórico de métrica. |
| HistoryBeginTime() |Devolve o carimbo de data / hora do exemplo de dados disponíveis mais antigo para a métrica. |
| GetSamplePercent() |Devolve a percentagem de exemplos que estão disponíveis para um determinado intervalo. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Uma vez que o `GetSample` método falha se a percentagem de amostras devolvido é menos do que o `samplePercent` especificado, pode usar o `GetSamplePercent` método para verificar primeiro. Em seguida, pode executar uma ação alternativa se amostras insuficientes estiverem presentes, sem parar a avaliação de dimensionamento automática. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemplos, percentagem de exemplo e o *GetSample()* método
O funcionamento essencial de uma fórmula de dimensionamento automático é obter dados de métrica de tarefa e os recursos e, em seguida, ajustar o tamanho do conjunto com base nesses dados. Como tal, é importante ter uma compreensão clara de como o dimensionamento automático fórmulas interagirem com dados de métricas (exemplos).

**Amostras**

O serviço Batch periodicamente demora exemplos das métricas de tarefa e os recursos e disponibiliza-os para as suas fórmulas de dimensionamento automático. Estes exemplos são gravados a cada 30 segundos pelo serviço Batch. No entanto, normalmente, existe um atraso entre quando essas amostras foram registradas e quando eles são disponibilizados com (e podem ser lidos por) suas fórmulas de dimensionamento automático. Além disso, devido a vários fatores, como a rede ou outros problemas de infraestrutura, os exemplos podem não ser registados para um determinado intervalo.

**Percentagem de exemplo**

Quando `samplePercent` é passado para o `GetSample()` método ou o `GetSamplePercent()` método é chamado, _por cento_ refere-se para uma comparação entre o número total possível de exemplos que são registados pelo serviço Batch e o número de exemplos que estão disponíveis para a sua fórmula de dimensionamento automático.

Vamos examinar um intervalo de tempo de 10 minutos como exemplo. Como exemplos são registrados dentro de um intervalo de tempo de 10 minutos a cada 30 segundos, o número máximo total de exemplos que são registados pelo Batch seria 20 amostras (2 por minuto). No entanto, devido a latência inerente do mecanismo de geração de relatórios e outros problemas no Azure, pode haver apenas 15 exemplos que estão disponíveis para a sua fórmula de dimensionamento automático para leitura. Então, por exemplo, durante esse período de 10 minutos, apenas 75% do número total de amostras registadas podem estar disponíveis para a sua fórmula.

**Intervalos de exemplo e GetSample()**

As fórmulas de dimensionamento automático vão ser crescente e decrescente seus conjuntos &mdash; adicionar nós ou remover nós. Porque nós são caros, pretender certificar-se de que as suas fórmulas usar um método inteligente de análise com base nos dados suficientes. Por conseguinte, recomendamos que utilize uma análise do tipo de tendências nas suas fórmulas. Este tipo aumenta e diminui a seus conjuntos com base numa variedade de exemplos recolhidos.

Para tal, utilize `GetSample(interval look-back start, interval look-back end)` para retornar um vetor de amostras:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada por lote, ele retorna uma variedade de exemplos de como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois de já recolhidos o vetor de amostras, em seguida, pode utilizar funções como `min()`, `max()`, e `avg()` derivar significativos valores de intervalo recolhido.

Para segurança adicional, pode forçar uma avaliação de fórmula para falhar caso inferior a uma determinada percentagem de exemplo está disponível durante um período de tempo específico. Quando forçar uma avaliação de fórmula para efetuar a ativação, instrui Batch pare ainda mais a avaliação da fórmula se a percentagem especificada de exemplos não estiver disponível. Neste caso, nenhuma alteração é feita para o tamanho do conjunto. Para especificar uma porcentagem necessária de exemplos para a avaliação com êxito, especifique-o como o terceiro parâmetro `GetSample()`. Aqui, é especificado um requisito de 75% das amostras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Como pode haver um atraso na disponibilidade de exemplo, é importante sempre especificar um intervalo de tempo com uma hora de início de repetição de aparência mais antigo do que um minuto. Ele leva aproximadamente um minuto para exemplos propagar através do sistema, por isso, exemplos no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` poderá não estar disponível. Mais uma vez, pode utilizar o parâmetro de percentagem de `GetSample()` para forçar um requisito de percentagem de exemplo em particular.

> [!IMPORTANT]
> Estamos **altamente recomendável** que **evitar da entidade confiadora *apenas* no `GetSample(1)` nas suas fórmulas de dimensionamento automático**. Isto acontece porque `GetSample(1)` essencialmente diz para o serviço Batch, "Dê-me o último exemplo tem, não importa como há muito tempo que obteve." Como é apenas um exemplo único, e pode ser um exemplo mais antigo, pode não ser representativo do quadro de tarefas recentes ou estado do recurso. Se utilizar `GetSample(1)`, certifique-se de que ele faz parte de uma instrução maior e não o ponto de dados única que depende de sua fórmula.
>
>

## <a name="metrics"></a>Métricas
Pode utilizar as métricas de recurso e tarefas durante a definição de uma fórmula. Ajustar o número de destino de nós dedicados em conjunto com base nos dados de métricas que obtenha e avaliar. Consulte a [variáveis](#variables) secção acima para obter mais informações sobre cada uma.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>Métricas de recurso baseiam-se a CPU, a largura de banda, o uso de memória de nós de computação e o número de nós.</p>
        <p> Estas variáveis definidas pelo serviço são úteis para fazendo ajustes com base na contagem de nó:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo serviço são úteis para fazendo ajustes com base na utilização de recursos de nó:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarefa</b></td>
    <td><p>Métricas de tarefa são baseiam o estado das tarefas, como ativa, pendente e foi concluídas. As seguintes variáveis definidas pelo serviço são úteis para fazer ajustes de tamanho do conjunto com base em métricas de tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escrever uma fórmula de dimensionamento automático
Criar uma fórmula de dimensionamento automático por formem instruções que utilizar os componentes acima e depois combinar essas instruções numa fórmula completa. Nesta secção, vamos criar uma fórmula de dimensionamento automático de exemplo que pode realizar algumas decisões de dimensionamento do mundo real.

Em primeiro lugar, vamos definir os requisitos para a nova fórmula de dimensionamento automático. A fórmula deve:

1. Aumente o número de destino de nós de computação dedicados de um conjunto se a utilização da CPU é alta.
2. Diminua o número de destino de nós de computação dedicados de um conjunto quando a utilização de CPU é baixa.
3. Sempre restringir o número máximo de nós dedicados a 400.

Para aumentar o número de nós durante a utilização elevada da CPU, definir a instrução que preenche uma variável definida pelo utilizador (`$totalDedicatedNodes`) com um valor que é 110 percentagem do número de destino atual de nós dedicados, mas apenas se a utilização média da CPU mínima durante o últimos 10 minutos foi superior a 70 por cento. Caso contrário, utilize o valor para o número atual de nós dedicados.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para *diminuir* o número de nós dedicado durante a utilização da CPU de baixa, a próxima instrução em nossa fórmula define os mesmos `$totalDedicatedNodes` variável a 90 por cento de atual número de nós dedicados de destino, se a utilização média da CPU no passado 60 minutos foi em 20 por cento. Caso contrário, utilize o valor atual do `$totalDedicatedNodes` que preenchemos na instrução acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora, limite o número de destino de nós de computação dedicados a um máximo de 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Eis a fórmula completa:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Criar um conjunto de dimensionamento automático ativado com .NET

Para criar um conjunto com o dimensionamento automático ativado no .NET, siga estes passos:

1. Criar o conjunto com [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Definir o [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) propriedade `true`.
3. Definir o [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) propriedade com a sua fórmula de dimensionamento automático.
4. (Opcional) Definir o [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) propriedade (a predefinição é 15 minutos).
5. Consolidar o conjunto com [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O fragmento de código seguinte cria um conjunto de dimensionamento automático ativado no .NET. Fórmula de dimensionamento automático do conjunto define o número de destino de nós dedicados para 5 às segundas-feiras e 1 em todos os outros dias da semana. O [intervalo de dimensionamento automático](#automatic-scaling-interval) é definido como 30 minutos. Neste e os outros trechos de c# neste artigo, `myBatchClient` é uma instância corretamente inicializada do [BatchClient] [ net_batchclient] classe.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Quando cria um conjunto de dimensionamento automático ativado, não especifique o _targetDedicatedNodes_ parâmetro ou o _targetLowPriorityNodes_ parâmetro na chamada para **CreatePool** . Em vez disso, especifique a **AutoScaleEnabled** e **AutoScaleFormula** propriedades no conjunto. Os valores para estas propriedades determinam o número de destino de cada tipo de nó. Além disso, para manualmente uma capacidade de dimensionamento automático de redimensionamento conjunto (por exemplo, com [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), primeiro **desativar** dimensionamento automático sobre o agrupamento e, em seguida, redimensione-o.
>
>

Além de .NET do Batch, pode utilizar qualquer uma das outras [SDKs do Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [REST do Batch](https://docs.microsoft.com/rest/api/batchservice/), [cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)e a [Batch CLI](batch-cli-get-started.md) para Configure o dimensionamento automático.


### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático
Por predefinição, o serviço Batch ajusta o tamanho de um conjunto, de acordo com sua fórmula de dimensionamento automático a cada 15 minutos. Este intervalo é configurável ao utilizar as seguintes propriedades do conjunto:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (.NET do Batch)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST API)

O intervalo mínimo é de cinco minutos e o máximo é 168 horas. Se não for especificado um intervalo fora deste intervalo, o serviço Batch devolve um erro de pedido inválido (400).

> [!NOTE]
> Dimensionamento automático não atualmente destina-se para responder a alterações em menos de um minuto, mas em vez disso, se destina a ajustar o tamanho do seu conjunto gradualmente como executar uma carga de trabalho.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Ativar o dimensionamento automático num conjunto existente

Cada SDK do Batch fornece uma forma de ativar o dimensionamento automático. Por exemplo:

* [BatchClient.PoolOperations.EnableAutoScaleAsync] [ net_enableautoscaleasync] (.NET do Batch)
* [Ativar dimensionamento automático num conjunto] [ rest_enableautoscale] (REST API)

Quando ativar o dimensionamento automático num conjunto existente, tenha em atenção os seguintes pontos:

* Se o dimensionamento automático está atualmente desativada no conjunto quando emitir o pedido para ativar o dimensionamento automático, tem de especificar uma fórmula de dimensionamento automático válido quando emitir o pedido. Opcionalmente, pode especificar um intervalo de avaliação do dimensionamento automático. Se não especificar um intervalo, é utilizado o valor predefinido de 15 minutos.
* Se o dimensionamento automático está ativado atualmente no conjunto, pode especificar uma fórmula de dimensionamento automático, um intervalo de avaliação ou ambos. Tem de especificar, pelo menos, uma destas propriedades.

  * Se especificar um novo intervalo de avaliação de dimensionamento automático, em seguida, a agenda de avaliação existente está parada e uma nova agenda é iniciada. Hora de início da nova agenda é a hora em que foi emitido o pedido para ativar o dimensionamento automático.
  * Se omitir a fórmula de dimensionamento automático ou intervalo de avaliação, o serviço Batch continua a utilizar o valor atual dessa definição.

> [!NOTE]
> Se tiver especificado os valores para o *targetDedicatedNodes* ou *targetLowPriorityNodes* parâmetros do **CreatePool** método quando criou o conjunto no .NET, ou para o parâmetros comparáveis em outro idioma, em seguida, esses valores serão ignorados durante a fórmula de dimensionamento automática é avaliada.
>
>

Este fragmento de código do c# utiliza a [.NET do Batch] [ net_api] biblioteca para ativar o dimensionamento automático num conjunto existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de dimensionamento automático

Para atualizar a fórmula num conjunto de dimensionamento automático ativado existente, chame a operação para ativar o dimensionamento automático novamente com a fórmula de novo. Por exemplo, se o dimensionamento automático já está ativado na `myexistingpool` quando o seguinte código do .NET é executado, a sua fórmula de dimensionamento automático é substituída com o conteúdo de `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de dimensionamento automático

Para atualizar o intervalo de avaliação de dimensionamento automático de um conjunto de dimensionamento automático ativado existente, chame a operação para ativar o dimensionamento automático com o intervalo de novo. Por exemplo, para definir o intervalo de avaliação do dimensionamento automático a 60 minutos para um conjunto que já está ativada para dimensionamento automático no .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de dimensionamento automático

Pode avaliar uma fórmula antes de aplicá-lo a um conjunto. Dessa forma, pode testar a fórmula para ver como seu instruções avaliar antes de colocar a fórmula em produção.

Para avaliar uma fórmula de dimensionamento automático, primeiro tem de ativar o dimensionamento automático no conjunto com uma fórmula válida. Para testar uma fórmula num conjunto que ainda não tem o dimensionamento automático ativado, utilize a fórmula de uma linha `$TargetDedicatedNodes = 0` quando ativar primeiro o dimensionamento automático. Em seguida, utilize um dos seguintes para avaliar a fórmula que pretende testar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Esses métodos de .NET do Batch requerem o ID de um conjunto existente e uma cadeia de caracteres que contém a fórmula de dimensionamento automático avaliar.

* [Avaliar uma fórmula de dimensionamento automática](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Neste pedido de REST API, especifique o ID do conjunto no URI e a fórmula de dimensionamento automático no *autoScaleFormula* elemento de corpo do pedido. A resposta da operação contém quaisquer informações de erro que podem estar relacionado com a fórmula.

Nesta [.NET do Batch] [ net_api] trecho de código, vamos avaliar uma fórmula de dimensionamento automático. Se o conjunto não tem o dimensionamento automático ativado, podemos ativar primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Avaliação concluída com êxito da fórmula mostrada neste fragmento de código produz resultados semelhantes a:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre execuções de dimensionamento automático

Para garantir que a sua fórmula está a funcionar conforme esperado, recomendamos que verifique periodicamente os resultados das execuções de dimensionamento automático que o Batch executa no seu conjunto. Portanto, obter (ou atualizar) uma referência para o pool e examinar as propriedades da sua última autoscale executar.

No .NET do Batch, o [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) propriedade tem várias propriedades que fornecem informações sobre as mais recentes dimensionamento automático executar executadas no conjunto:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na API do REST, o [obter informações sobre um conjunto](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) solicitação retorna informações sobre o agrupamento, o que inclui o dimensionamento automático mais recente executar informações no [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) propriedade.

O seguinte trecho de código do c# utiliza a biblioteca .NET do Batch para imprimir informações sobre o executar no conjunto de dimensionamento automático do último _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Saída de exemplo do trecho precedente:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Fórmulas de dimensionamento automático de exemplo
Vamos examinar algumas fórmulas que apresentam diferentes formas para ajustar a quantidade de recursos de computação num conjunto.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: Ajuste baseados no tempo
Suponha que deseja ajustar o tamanho do conjunto com base no dia da semana e hora do dia. Este exemplo mostra como aumentar ou diminuir o número de nós no conjunto em conformidade.

Primeiro, a fórmula obtém a hora atual. Se for um dia da semana (1 a 5) e no horário de trabalho (8 00 às 18:00), o tamanho do conjunto de destino é definido como 20 nós. Caso contrário, ele é definido como 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: Ajuste e baseado em tarefas
Neste exemplo, o tamanho do conjunto é ajustado com base no número de tarefas na fila. Comentários e quebras de linha são aceitáveis em cadeias de caracteres de fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: Gestão de contas para tarefas paralelas
Neste exemplo ajusta o tamanho do conjunto com base no número de tarefas. Esta fórmula também leva em conta a [MaxTasksPerComputeNode] [ net_maxtasks] valor foi definido para o conjunto. Esta abordagem é útil em situações em que [execução de tarefa paralela](batch-parallel-node-tasks.md) foi ativada no seu conjunto.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: Definir um tamanho de pool inicial
Este exemplo mostra um trecho de código do c# com uma fórmula de dimensionamento automático que define o tamanho do conjunto como um número especificado de nós para um período de tempo inicial. Em seguida, ele ajusta o tamanho do conjunto com base no número de execução e Active Directory tarefas depois de decorrido o período de tempo inicial.

A fórmula no seguinte fragmento de código:

* Define o tamanho do conjunto inicial de quatro nós.
* Não ajusta o tamanho do conjunto a primeira 10 minutos do ciclo de vida do conjunto.
* Depois de 10 minutos, obtém o valor máximo do número de tarefas em execução e Active Directory nos últimos 60 minutos.
  * Se ambos os valores são 0 (indicando que não existem tarefas eram em execução ou Active Directory nos últimos 60 minutos), o tamanho do conjunto é definido como 0.
  * Se qualquer valor for superior a zero, nenhuma alteração é feita.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Passos Seguintes
* [Maximize a utilização de recursos de computação do Azure Batch com tarefas de nós simultâneas](batch-parallel-node-tasks.md) contém detalhes sobre como pode executar várias tarefas em simultâneo em nós de computação do conjunto. Além de dimensionamento automático, esta funcionalidade pode ajudar a reduzir a duração de tarefa para algumas cargas de trabalho, logo poupa dinheiro.
* Para outro booster de eficiência, certifique-se de que a aplicação de lote consulta o serviço Batch da forma ideal. Ver [consultar o serviço Azure Batch de forma eficiente](batch-efficient-list-queries.md) para aprender a limitar a quantidade de dados que cruza a conexão ao consultar o estado de potencialmente milhares de nós de computação ou tarefas.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
