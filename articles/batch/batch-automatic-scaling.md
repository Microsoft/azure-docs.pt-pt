---
title: Dimensionar automaticamente nós de computação em um pool do lote do Azure | Microsoft Docs
description: Habilite o dimensionamento automático em um pool de nuvem para ajustar dinamicamente o número de nós de computação no pool.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: c3c94805c18b0a7a3052158871c5fafce2dd5a33
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660720"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Criar uma fórmula automática para dimensionar nós de computação em um pool do lote

O lote do Azure pode dimensionar automaticamente os pools com base nos parâmetros que você definir. Com o dimensionamento automático, o lote adiciona nós dinamicamente a um pool conforme as demandas de tarefas aumentam e remove os nós de computação à medida que eles diminuem. Você pode economizar tempo e dinheiro ajustando automaticamente o número de nós de computação usados pelo aplicativo do lote.

Você habilita o dimensionamento automático em um pool de nós de computação associando a ele uma *fórmula de dimensionamento automático* definida por você. O serviço de lote usa a fórmula de dimensionamento automático para determinar o número de nós de computação necessários para executar sua carga de trabalho. Os nós de computação podem ser nós dedicados ou [nós de baixa prioridade](batch-low-pri-vms.md). O lote responde a dados de métricas de serviço que são coletados periodicamente. Usando esses dados de métricas, o lote ajusta o número de nós de computação no pool com base em sua fórmula e em um intervalo configurável.

Você pode habilitar o dimensionamento automático quando um pool é criado ou em um pool existente. Você também pode alterar uma fórmula existente em um pool configurado para dimensionamento automático. O lote permite que você avalie suas fórmulas antes de atribuí-las a pools e para monitorar o status das execuções de dimensionamento automático.

Este artigo discute as várias entidades que compõem suas fórmulas de dimensionamento automático, incluindo variáveis, operadores, operações e funções. Discutiremos como obter várias métricas de tarefa e recurso de computação no lote. Você pode usar essas métricas para ajustar a contagem de nós do pool com base no uso de recursos e no status da tarefa. Em seguida, descrevemos como construir uma fórmula e habilitar o dimensionamento automático em um pool usando as APIs REST e .NET do lote. Finalmente, terminamos com algumas fórmulas de exemplo.

> [!IMPORTANT]
> Ao criar uma conta do lote, você pode especificar a [configuração da conta](batch-api-basics.md#account), que determina se os pools são alocados em uma assinatura do serviço de lote (o padrão) ou em sua assinatura de usuário. Se você criou sua conta do lote com a configuração padrão do serviço de lote, sua conta será limitada a um número máximo de núcleos que podem ser usados para processamento. O serviço de lote dimensiona os nós de computação somente até esse limite de núcleo. Por esse motivo, o serviço de lote pode não alcançar o número de destino de nós de computação especificado por uma fórmula de dimensionamento automático. Consulte [cotas e limites para o serviço do lote do Azure](batch-quota-limit.md) para obter informações sobre como exibir e aumentar suas cotas de conta.
>
>Se você criou sua conta com a configuração de assinatura do usuário, sua conta será compartilhada na cota principal da assinatura. Para obter mais informações, veja [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Limites das Máquinas Virtuais), em [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Limites, quotas e limitações das subscrições e serviços do Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Fórmulas de dimensionamento automático

Uma fórmula de dimensionamento automático é um valor de cadeia de caracteres que você define que contém uma ou mais instruções. A fórmula de dimensionamento automático é atribuída ao elemento [autoScaleFormula][rest_autoscaleformula] do pool (REST do lote) ou à propriedade [CloudPool. autoScaleFormula][net_cloudpool_autoscaleformula] (.net do lote). O serviço de lote usa sua fórmula para determinar o número de destino dos nós de computação no pool para o próximo intervalo de processamento. A cadeia de caracteres da fórmula não pode exceder 8 KB, pode incluir até 100 instruções separadas por ponto e vírgula e pode incluir quebras de linha e comentários.

Você pode considerar as fórmulas de dimensionamento automático como um "idioma" de autoescala do lote. As instruções de fórmula são expressões de forma livre que podem incluir variáveis definidas pelo serviço (variáveis definidas pelo serviço de lote) e variáveis definidas pelo usuário (variáveis que você define). Eles podem executar várias operações nesses valores usando tipos, operadores e funções internos. Por exemplo, uma instrução pode assumir o seguinte formato:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

As fórmulas geralmente contêm várias instruções que executam operações em valores que são obtidos nas instruções anteriores. Por exemplo, primeiro obtemos um valor para `variable1`e, em seguida, o passamos para uma função para popular `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua essas instruções em sua fórmula de dimensionamento automático para chegar a um número de nós de computação de destino. Nós dedicados e nós de baixa prioridade têm suas próprias configurações de destino, para que você possa definir um destino para cada tipo de nó. Uma fórmula de dimensionamento automático pode incluir um valor de destino para nós dedicados, um valor de destino para nós de baixa prioridade ou ambos.

O número de nós de destino pode ser maior, menor ou igual ao número atual de nós desse tipo no pool. O lote avalia a fórmula de dimensionamento automático de um pool em um intervalo específico (consulte [intervalos de dimensionamento automático](#automatic-scaling-interval)). O lote ajusta o número de destino de cada tipo de nó no pool para o número que sua fórmula de dimensionamento automático especifica no momento da avaliação.

### <a name="sample-autoscale-formulas"></a>Fórmulas de autoescala de exemplo

Abaixo estão exemplos de duas fórmulas de dimensionamento automático, que podem ser ajustadas para funcionar na maioria dos cenários. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` nas fórmulas de exemplo podem ser ajustadas às suas necessidades.

#### <a name="pending-tasks"></a>Tarefas pendentes

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Com essa fórmula de autoescala, o pool é inicialmente criado com uma única VM. A métrica de `$PendingTasks` define o número de tarefas que estão em execução ou na fila. A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define a variável de `$TargetDedicatedNodes` de acordo. A fórmula garante que o número de destino de nós dedicados nunca exceda 25 VMs. À medida que novas tarefas são enviadas, o pool aumenta automaticamente. À medida que as tarefas são concluídas, as VMs tornam-se gratuitas uma a uma e a fórmula de dimensionamento automático reduz o pool.

Essa fórmula dimensiona os nós dedicados, mas pode ser modificada para se aplicar para dimensionar nós de baixa prioridade também.

#### <a name="preempted-nodes"></a>Nós admitidos 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Este exemplo cria um pool que começa com 25 nós de baixa prioridade. Toda vez que um nó de baixa prioridade é preempção, ele é substituído por um nó dedicado. Assim como no primeiro exemplo, a variável `maxNumberofVMs` impede que o pool exceda 25 VMs. Este exemplo é útil para aproveitar as VMs de baixa prioridade, garantindo também que apenas um número fixo de preempçãos ocorrerá durante o tempo de vida do pool.

## <a name="variables"></a>Variáveis

Você pode usar as variáveis definidas **pelo serviço** e **definidas pelo usuário** em suas fórmulas de dimensionamento automático. As variáveis definidas pelo serviço são internas ao serviço de lote. Algumas variáveis definidas pelo serviço são de leitura/gravação e outras são somente leitura. Variáveis definidas pelo usuário são variáveis que você define. Na fórmula de exemplo mostrada na seção anterior, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` são variáveis definidas pelo usuário.

> [!NOTE]
> As variáveis definidas pelo serviço são sempre precedidas por um cifrão ($). Para variáveis definidas pelo usuário, o cifrão é opcional.
>
>

As tabelas a seguir mostram as variáveis de leitura/gravação e somente leitura que são definidas pelo serviço de lote.

Você pode obter e definir os valores dessas variáveis definidas pelo serviço para gerenciar o número de nós de computação em um pool:

| Variáveis definidas pelo serviço de leitura/gravação | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número alvo de nós de computação dedicados para o pool. O número de nós dedicados é especificado como um destino, pois um pool nem sempre pode atingir o número de nós desejado. Por exemplo, se o número de destino de nós dedicados for modificado por uma avaliação de dimensionamento automático antes que o pool tenha atingido o destino inicial, o pool poderá não alcançar o destino. <br /><br /> Um pool em uma conta criada com a configuração do serviço de lote poderá não atingir seu destino se o destino exceder um nó de conta do lote ou uma cota de núcleo. Um pool em uma conta criada com a configuração de assinatura do usuário poderá não atingir seu destino se o destino exceder a cota de núcleos compartilhada para a assinatura.|
| $TargetLowPriorityNodes |O número de destino de nós de computação de baixa prioridade para o pool. O número de nós de baixa prioridade é especificado como um destino porque um pool nem sempre consegue obter o número desejado de nós. Por exemplo, se o número de destino de nós de baixa prioridade for modificado por uma avaliação de dimensionamento automático antes que o pool tenha atingido o destino inicial, o pool poderá não alcançar o destino. Um pool também pode não alcançar seu destino se o destino exceder um nó de conta do lote ou uma cota de núcleo. <br /><br /> Para obter mais informações sobre nós de computação de baixa prioridade, consulte [usar VMs de baixa prioridade com o lote](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando nós de computação são removidos de um pool. Os valores possíveis são:<ul><li>recolocar na **fila**--o valor padrão. Encerra as tarefas imediatamente e as coloca novamente na fila de trabalho para que elas sejam reagendadas. Essa ação garante que o número de destino dos nós seja atingido o mais rápido possível, mas pode ser menos eficiente, pois qualquer tarefa em execução será interrompida e precisará ser reiniciada, desperdiçando qualquer trabalho que já tenha feito. <li>**terminar**– encerra as tarefas imediatamente e as remove da fila de trabalhos.<li>**taskcompletion**--aguarda a conclusão das tarefas em execução no momento e, em seguida, remove o nó do pool. Use essa opção para evitar que tarefas sejam interrompidas e recolocadas na fila, desperdiçando qualquer trabalho que a tarefa tenha feito. <li>**retaineddata**--aguarda que todos os dados de tarefa local retidos no nó sejam limpos antes de remover o nó do pool.</ul> |

> [!NOTE]
> A variável `$TargetDedicatedNodes` também pode ser especificada usando o alias `$TargetDedicated`. Da mesma forma, a variável `$TargetLowPriorityNodes` pode ser especificada usando o alias `$TargetLowPriority`. Se a variável totalmente nomeada e seu alias forem definidos pela fórmula, o valor atribuído à variável totalmente nomeada terá precedência.
>
>

Você pode obter o valor dessas variáveis definidas pelo serviço para fazer ajustes que se baseiam em métricas do serviço de lote:

| Variáveis definidas pelo serviço somente leitura | Descrição |
| --- | --- |
| $CPUPercent |A porcentagem média de uso da CPU. |
| $WallClockSeconds |O número de segundos consumidos. |
| $MemoryBytes |O número médio de megabytes usados. |
| $DiskBytes |O número médio de gigabytes usados nos discos locais. |
| $DiskReadBytes |O número de bytes lidos. |
| $DiskWriteBytes |O número de bytes gravados. |
| $DiskReadOps |A contagem de operações de leitura de disco executadas. |
| $DiskWriteOps |A contagem de operações de gravação de disco executadas. |
| $NetworkInBytes |O número de bytes de entrada. |
| $NetworkOutBytes |O número de bytes de saída. |
| $SampleNodeCount |A contagem de nós de computação. |
| $ActiveTasks |O número de tarefas que estão prontas para serem executadas, mas que ainda não estão em execução. A contagem de $ActiveTasks inclui todas as tarefas que estão no estado ativo e cujas dependências foram satisfeitas. Todas as tarefas que estão no estado ativo, mas cujas dependências não foram satisfeitas, são excluídas da contagem de $ActiveTasks.|
| $RunningTasks |O número de tarefas em um estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que foram concluídas com êxito. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicatedNodes |O número atual de nós de computação dedicados. |
| $CurrentLowPriorityNodes |O número atual de nós de computação de baixa prioridade, incluindo todos os nós que foram substituídas. |
| $PreemptedNodeCount | O número de nós no pool que estão em um estado substituídas. |

> [!TIP]
> As variáveis definidas pelo serviço e somente leitura que são mostradas na tabela anterior são *objetos* que fornecem vários métodos para acessar os dados associados a cada um. Para obter mais informações, consulte [obter dados de exemplo](#getsampledata) mais adiante neste artigo.
>
>

## <a name="types"></a>Tipos

Esses tipos têm suporte em uma fórmula:

* double
* doubleVec
* doubleVecList
* string
* Timestamp--carimbo de data/hora é uma estrutura composta que contém os seguintes membros:

  * ano
  * mês (1-12)
  * dia (1-31)
  * dia da semana (no formato de número; por exemplo, 1 para segunda-feira)
  * hora (no formato de número de 24 horas; por exemplo, 13 significa 1 PM)
  * minuto (00-59)
  * segundo (00-59)
* timeInterval

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

## <a name="operations"></a>Operations

Essas operações são permitidas nos tipos listados na seção anterior.

| Operação | Operadores com suporte | Tipo de resultado |
| --- | --- | --- |
| duplo de *operador* duplo |+, -, *, / |double |
| *operador* de intervalo duplo |* |timeInterval |
| doubleVec *operador* Double |+, -, *, / |doubleVec |
| *operador* doubleVec doubleVec |+, -, *, / |doubleVec |
| *operador* timeInterval duplo |*, / |timeInterval |
| timeInterval *operador* timeintervalo |+, - |timeInterval |
| carimbo de data/hora do *operador* timeInterval |+ |carimbo de data/hora |
| timeInterval do *operador* timestamp |+ |carimbo de data/hora |
| carimbo de data/hora do *operador* timestamp |- |timeInterval |
| duplo de *operador* |-, ! |double |
| *operator*timeinterval |- |timeInterval |
| duplo de *operador* duplo |<, <=, ==, >=, >, != |double |
| Cadeia de caracteres do *operador* String |<, <=, ==, >=, >, != |double |
| carimbo de data/hora do *operador* timestamp |<, <=, ==, >=, >, != |double |
| timeInterval *operador* timeintervalo |<, <=, ==, >=, >, != |double |
| duplo de *operador* duplo |&&, &#124;&#124; |double |

Ao testar um Double com um operador ternário (`double ? statement1 : statement2`), diferente de zero é **true**e zero é **false**.

## <a name="functions"></a>Funções
Essas **funções** predefinidas estão disponíveis para uso na definição de uma fórmula de dimensionamento automático.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| avg(doubleVecList) |double |Retorna o valor médio de todos os valores no doubleVecList. |
| len(doubleVecList) |double |Retorna o comprimento do vetor que é criado a partir de doubleVecList. |
| LG (duplo) |double |Retorna a base de log 2 do duplo. |
| lg(doubleVecList) |doubleVec |Retorna a base de log do componente 2 do doubleVecList. Um VEC (Double) deve ser passado explicitamente para o parâmetro. Caso contrário, a versão de LG dupla (Double) será assumida. |
| ln (duplo) |double |Retorna o log natural do duplo. |
| ln(doubleVecList) |doubleVec |Retorna o log natural do duplo. |
| log (duplo) |double |Retorna a base de log 10 do duplo. |
| log(doubleVecList) |doubleVec |Retorna a base do log do componente 10 do doubleVecList. Um VEC (Double) deve ser passado explicitamente para o único parâmetro Double. Caso contrário, a versão de log duplo (Double) é assumida. |
| max(doubleVecList) |double |Retorna o valor máximo em doubleVecList. |
| min(doubleVecList) |double |Retorna o valor mínimo no doubleVecList. |
| norm(doubleVecList) |double |Retorna a duas normas do vetor que é criado a partir de doubleVecList. |
| percentil (doubleVec v, duplo p) |double |Retorna o elemento percentil do vetor v. |
| Rand () |double |Retorna um valor aleatório entre 0,0 e 1,0. |
| range(doubleVecList) |double |Retorna a diferença entre os valores mínimo e máximo no doubleVecList. |
| std(doubleVecList) |double |Retorna o desvio padrão de exemplo dos valores no doubleVecList. |
| stop() | |Interrompe a avaliação da expressão de dimensionamento automático. |
| sum(doubleVecList) |double |Retorna a soma de todos os componentes do doubleVecList. |
| time(string dateTime="") |carimbo de data/hora |Retorna o carimbo de data/hora da hora atual se nenhum parâmetro for passado ou o carimbo de data/hora da cadeia de caracteres dateTime se ele for passado. Os formatos de data e hora com suporte são W3C-DTF e RFC 1123. |
| Val (doubleVec v, duplo i) |double |Retorna o valor do elemento que está no local i no vetor v, com um índice inicial de zero. |

Algumas das funções descritas na tabela anterior podem aceitar uma lista como um argumento. A lista separada por vírgulas é qualquer combinação de *Double* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido em um único *doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, chamar `avg(v)` será equivalente a chamar `avg(1,2,3)`. Chamar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo

As fórmulas de dimensionamento automático agem em dados de métricas (exemplos) fornecidos pelo serviço de lote. Uma fórmula aumenta ou reduz o tamanho do pool com base nos valores obtidos do serviço. As variáveis definidas pelo serviço que foram descritas anteriormente são objetos que fornecem vários métodos para acessar dados associados a esse objeto. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() |O método `GetSample()` retorna um vetor de exemplos de dados.<br/><br/>Um exemplo é de 30 segundos de dados de métricas. Em outras palavras, os exemplos são obtidos a cada 30 segundos. Mas, conforme observado abaixo, há um atraso entre o momento em que uma amostra é coletada e quando ela está disponível para uma fórmula. Dessa forma, nem todas as amostras de um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras a serem obtidas dos exemplos mais recentes que foram coletados.<br/><br/>`GetSample(1)` retorna o último exemplo disponível. No entanto, para métricas como `$CPUPercent`, isso não deve ser usado porque é impossível saber *quando* o exemplo foi coletado. Pode ser recente ou, devido a problemas do sistema, pode ser muito mais antiga. Em tais casos, é melhor usar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para a coleta de dados de exemplo. Opcionalmente, ele também especifica a porcentagem de amostras que devem estar disponíveis no período de tempo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` retornará 20 amostras se todas as amostras dos últimos 10 minutos estiverem presentes no histórico de CPUPercent. No entanto, se o último minuto do histórico não estivesse disponível, apenas 18 amostras seriam retornadas. Neste caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` falharia porque apenas 90% dos exemplos estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` teria sucesso.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para coleta de dados, com uma hora de início e uma hora de término.<br/><br/>Conforme mencionado acima, há um atraso entre quando uma amostra é coletada e quando está disponível para uma fórmula. Considere esse atraso ao usar o método `GetSample`. Consulte `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Retorna o período de amostras que foram executadas em um conjunto de dados de exemplo histórico. |
| Contagem () |Retorna o número total de amostras no histórico de métricas. |
| HistoryBeginTime() |Retorna o carimbo de data/hora do exemplo de dados mais antigo disponível para a métrica. |
| GetSamplePercent() |Retorna a porcentagem de amostras disponíveis para um determinado intervalo de tempo. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Como o método `GetSample` falhará se a porcentagem de amostras retornada for menor que a `samplePercent` especificada, você poderá usar o método `GetSamplePercent` para verificar primeiro. Em seguida, você poderá executar uma ação alternativa se houver amostras insuficientes, sem interromper a avaliação automática de dimensionamento. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemplos, porcentagem de exemplo e o método *getsample ()*
A principal operação de uma fórmula de dimensionamento automático é obter dados de métrica de tarefa e recurso e, em seguida, ajustar o tamanho do pool com base nesses dados. Assim, é importante ter uma compreensão clara de como as fórmulas de dimensionamento automático interagem com dados de métricas (exemplos).

**Amostras**

Periodicamente, o serviço de lote faz amostras de métricas de tarefas e recursos e as torna disponíveis para suas fórmulas de dimensionamento automático. Esses exemplos são gravados a cada 30 segundos pelo serviço de lote. No entanto, normalmente há um atraso entre quando essas amostras foram registradas e quando elas são disponibilizadas para (e podem ser lidas por) suas fórmulas de dimensionamento automático. Além disso, devido a vários fatores, como problemas de rede ou outros tipos de infraestrutura, os exemplos não podem ser registrados para um intervalo específico.

**Porcentagem de amostra**

Quando `samplePercent` é passado para o método `GetSample()` ou o método `GetSamplePercent()` é chamado, _percent_ se refere a uma comparação entre o número total possível de amostras que são registradas pelo serviço de lote e o número de amostras disponíveis para sua fórmula de dimensionamento automático.

Vamos examinar um período de 10 minutos como exemplo. Como os exemplos são registrados a cada 30 segundos em um período de 10 minutos, o número total máximo de amostras registradas pelo lote seria de 20 amostras (2 por minuto). No entanto, devido à latência inerente do mecanismo de relatório e outros problemas no Azure, pode haver apenas 15 exemplos disponíveis para sua fórmula de dimensionamento automático para leitura. Portanto, por exemplo, para esse período de 10 minutos, somente 75% do número total de amostras gravadas podem estar disponíveis para sua fórmula.

**Intervalos de amostragem e de getsample ()**

Suas fórmulas de dimensionamento automático vão aumentar e reduzir seus pools &mdash; adicionando nós ou removendo nós. Como os nós custam dinheiro, você deseja garantir que suas fórmulas usem um método inteligente de análise baseado em dados suficientes. Portanto, recomendamos que você use uma análise de tipo de tendência em suas fórmulas. Esse tipo aumenta e reduz seus pools com base em uma variedade de amostras coletadas.

Para fazer isso, use `GetSample(interval look-back start, interval look-back end)` para retornar um vetor de exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada pelo lote, ela retorna um intervalo de amostras como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois de coletar o vetor de exemplos, você pode usar funções como `min()`, `max()`e `avg()` para derivar valores significativos do intervalo coletado.

Para obter mais segurança, você pode forçar uma avaliação de fórmula a falhar se menos de um determinado percentual de amostra estiver disponível por um período de tempo específico. Quando você força uma avaliação de fórmula a falhar, você instrui o lote a cessar a avaliação adicional da fórmula se o percentual especificado de amostras não estiver disponível. Nesse caso, nenhuma alteração é feita no tamanho do pool. Para especificar uma porcentagem necessária de amostras para que a avaliação seja bem sucedida, especifique-a como o terceiro parâmetro a `GetSample()`. Aqui, é especificado um requisito de 75% dos exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Como pode haver um atraso na disponibilidade de exemplo, é importante sempre especificar um intervalo de tempo com uma hora de início de retirada com mais de um minuto. Leva aproximadamente um minuto para que os exemplos se propaguem pelo sistema, portanto, os exemplos no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` podem não estar disponíveis. Novamente, você pode usar o parâmetro percentual de `GetSample()` para forçar um requisito de percentual de exemplo específico.

> [!IMPORTANT]
> É **altamente recomendável** que você **Evite confiar *apenas* em `GetSample(1)` em suas fórmulas de dimensionamento automático**. Isso ocorre porque `GetSample(1)` basicamente diz ao serviço de lote, "Dê-me o último exemplo que você tem, não importa por quanto tempo chegou." Como é apenas um exemplo, e pode ser um exemplo mais antigo, ele pode não ser representativo da visão mais recente do estado de tarefa ou recursos recentes. Se você usar `GetSample(1)`, verifique se ele faz parte de uma declaração maior e não o único ponto de dados do qual sua fórmula depende.
>
>

## <a name="metrics"></a>Métricas

Você pode usar as métricas de tarefa e de recurso quando estiver definindo uma fórmula. Ajuste o número de destino de nós dedicados no pool com base nos dados de métricas obtidos e avaliados. Consulte a seção de [variáveis](#variables) acima para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>As métricas de recurso são baseadas na CPU, na largura de banda, no uso de memória de nós de computação e no número de nós.</p>
        <p> Essas variáveis definidas pelo serviço são úteis para fazer ajustes com base na contagem de nós:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Essas variáveis definidas pelo serviço são úteis para fazer ajustes com base no uso de recursos do nó:</p>
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
    <td><p>As métricas de tarefa baseiam-se no status das tarefas, como ativo, pendente e concluído. As seguintes variáveis definidas pelo serviço são úteis para fazer ajustes de tamanho de pool com base nas métricas de tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escrever uma fórmula de autoescala

Você cria uma fórmula de dimensionamento automático formando instruções que usam os componentes acima e, em seguida, combina essas instruções em uma fórmula completa. Nesta seção, criamos um exemplo de fórmula de autoescala que pode executar algumas decisões de dimensionamento do mundo real.

Primeiro, vamos definir os requisitos para nossa nova fórmula de autoescala. A fórmula deve:

1. Aumente o número alvo de nós de computação dedicados em um pool se o uso da CPU for alto.
1. Diminua o número alvo de nós de computação dedicados em um pool quando o uso da CPU for baixo.
1. Sempre restrinja o número máximo de nós dedicados a 400.
1. Ao reduzir o número de nós, não remova os nós que estão executando tarefas; se necessário, aguarde até que as tarefas tenham sido concluídas para remover os nós.

Para aumentar o número de nós durante o alto uso da CPU, defina a instrução que popula uma variável definida pelo usuário (`$totalDedicatedNodes`) com um valor que seja de 110% do número de destino atual de nós dedicados, mas somente se o uso médio mínimo de CPU durante os últimos 10 minutos tiver sido superior a 70%. Caso contrário, use o valor para o número atual de nós dedicados.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para *diminuir* o número de nós dedicados durante o baixo uso da CPU, a próxima instrução em nossa fórmula define a mesma `$totalDedicatedNodes` variável como 90% do número de nós dedicados de destino atual se o uso médio da CPU nos últimos 60 minutos foi inferior a 20%. Caso contrário, use o valor atual de `$totalDedicatedNodes` que populamos na instrução acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora, limite o número alvo de nós de computação dedicados a um máximo de 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Aqui está a fórmula completa:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Criar um pool habilitado para autoescala com SDKs do lote

O dimensionamento automático do pool pode ser configurado usando qualquer um dos [SDKs](batch-apis-tools.md#azure-accounts-for-batch-development)do lote, os [cmdlets do PowerShell do lote](batch-powershell-cmdlets-get-started.md)da [API REST do lote](https://docs.microsoft.com/rest/api/batchservice/) e a CLI do [lote](batch-cli-get-started.md). Nesta seção, você pode ver exemplos para .NET e Python.

### <a name="net"></a>.NET

Para criar um pool com o dimensionamento automático habilitado no .NET, siga estas etapas:

1. Crie o pool com [BatchClient. PoolOperations. createpool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Defina a propriedade [CloudPool. AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) como `true`.
1. Defina a propriedade [CloudPool. AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) com sua fórmula de autoescala.
1. Adicional Defina a propriedade [CloudPool. AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (o padrão é 15 minutos).
1. Confirme o pool com [CloudPool. Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O trecho de código a seguir cria um pool habilitado para autoescala no .NET. A fórmula de dimensionamento automático do pool define o número de nós dedicados de destino como 5 nas segundas-feiras e 1 em todos os outros dias da semana. O [intervalo de dimensionamento automático](#automatic-scaling-interval) é definido como 30 minutos. Neste e nos outros C# trechos de código neste artigo, `myBatchClient` é uma instância corretamente inicializada da classe [BatchClient][net_batchclient] .

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
> Quando você cria um pool habilitado para autoescala, não especifique o parâmetro _targetDedicatedNodes_ ou o parâmetro _targetLowPriorityNodes_ na chamada para **createpool**. Em vez disso, especifique as propriedades **AutoScaleEnabled** e **AutoScaleFormula** no pool. Os valores para essas propriedades determinam o número de destino de cada tipo de nó. Além disso, para redimensionar manualmente um pool habilitado para autoescala (por exemplo, com [BatchClient. PoolOperations. ResizePoolAsync][net_poolops_resizepoolasync]), primeiro **desabilite** o dimensionamento automático no pool e, em seguida, redimensione-o.
>
>

#### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático

Por padrão, o serviço de lote ajusta o tamanho de um pool de acordo com sua fórmula de dimensionamento automático a cada 15 minutos. Esse intervalo é configurável usando as seguintes propriedades de pool:

* [CloudPool. AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (.net do lote)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API REST)

O intervalo mínimo é de cinco minutos e o máximo é de 168 horas. Se um intervalo fora desse intervalo for especificado, o serviço de lote retornará um erro de solicitação inadequada (400).

> [!NOTE]
> O dimensionamento automático não se destina atualmente a responder a alterações em menos de um minuto, mas sim para ajustar o tamanho do pool gradualmente à medida que você executa uma carga de trabalho.
>
>

### <a name="python"></a>Python

Da mesma forma, você pode criar um pool habilitado para autoescala com o SDK do Python:

1. Crie um pool e especifique sua configuração.
1. Adicione o pool ao cliente de serviço.
1. Habilite o dimensionamento automático no pool com uma fórmula que você escreve.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Mais exemplos de como usar o SDK do Python podem ser encontrados no [repositório do início rápido do Python do lote](https://github.com/Azure-Samples/batch-python-quickstart) no github.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitar o dimensionamento automático em um pool existente

Cada SDK do lote fornece uma maneira de habilitar o dimensionamento automático. Por exemplo:

* [BatchClient. PoolOperations. EnableAutoScaleAsync][net_enableautoscaleasync] (.net do lote)
* [Habilitar o dimensionamento automático em um pool][rest_enableautoscale] (API REST)

Ao habilitar o dimensionamento automático em um pool existente, tenha em mente os seguintes pontos:

* Se o dimensionamento automático estiver desabilitado no pool no momento, quando você emitir a solicitação para habilitar a autoescala, deverá especificar uma fórmula de dimensionamento automático válida ao emitir a solicitação. Opcionalmente, você pode especificar um intervalo de avaliação de dimensionamento automático. Se você não especificar um intervalo, o valor padrão de 15 minutos será usado.
* Se o dimensionamento automático estiver habilitado no pool no momento, você poderá especificar uma fórmula de dimensionamento automático, um intervalo de avaliação ou ambos. Você deve especificar pelo menos uma dessas propriedades.

  * Se você especificar um novo intervalo de avaliação de dimensionamento automático, o agendamento de avaliação existente será interrompido e uma nova agenda será iniciada. A hora de início da nova agenda é a hora em que a solicitação para habilitar o dimensionamento automático foi emitida.
  * Se você omitir a fórmula de dimensionamento automático ou o intervalo de avaliação, o serviço de lote continuará a usar o valor atual dessa configuração.

> [!NOTE]
> Se você especificou valores para os parâmetros *targetDedicatedNodes* ou *targetLowPriorityNodes* do método **createpool** quando criou o pool no .net ou para os parâmetros comparáveis em outra linguagem, esses valores serão ignorados quando a fórmula de dimensionamento automático for avaliada.
>
>

Este C# trecho de código usa a biblioteca [.net do lote][net_api] para habilitar o dimensionamento automático em um pool existente:

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

Para atualizar a fórmula em um pool habilitado para autoescala existente, chame a operação para habilitar o dimensionamento automático novamente com a nova fórmula. Por exemplo, se o dimensionamento automático já estiver habilitado no `myexistingpool` quando o seguinte código .NET for executado, sua fórmula de autoescala será substituída pelo conteúdo de `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de dimensionamento automático

Para atualizar o intervalo de avaliação de autoescala de um pool habilitado para dimensionamento automático existente, chame a operação para habilitar o dimensionamento automático novamente com o novo intervalo. Por exemplo, para definir o intervalo de avaliação de dimensionamento automático para 60 minutos para um pool que já está habilitado para autoescala no .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de dimensionamento automático

Você pode avaliar uma fórmula antes de aplicá-la a um pool. Dessa forma, você pode testar a fórmula para ver como suas instruções são avaliadas antes de colocar a fórmula em produção.

Para avaliar uma fórmula de autoescala, você deve primeiro habilitar o dimensionamento automático no pool com uma fórmula válida. Para testar uma fórmula em um pool que ainda não tem o dimensionamento automático habilitado, use a fórmula de uma linha `$TargetDedicatedNodes = 0` quando você habilitar o dimensionamento automático pela primeira vez. Em seguida, use um dos seguintes para avaliar a fórmula que você deseja testar:

* [BatchClient. PoolOperations. EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Esses métodos .NET do lote exigem a ID de um pool existente e uma cadeia de caracteres que contém a fórmula de dimensionamento automático a ser avaliada.

* [Avaliar uma fórmula de dimensionamento automático](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Nesta solicitação da API REST, especifique a ID do pool no URI e a fórmula de dimensionamento automático no elemento *autoScaleFormula* do corpo da solicitação. A resposta da operação contém qualquer informação de erro que possa estar relacionada à fórmula.

Neste trecho de código [.net do lote][net_api] , avaliamos uma fórmula de dimensionamento automático. Se o pool não tiver o dimensionamento automático habilitado, habilite-o primeiro.

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

A avaliação bem-sucedida da fórmula mostrada neste trecho de código produz resultados semelhantes a:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre as execuções de autoescala

Para garantir que sua fórmula esteja sendo executada conforme o esperado, recomendamos que você verifique periodicamente os resultados das execuções de dimensionamento automático que o lote executa em seu pool. Para fazer isso, obtenha (ou atualize) uma referência ao pool e examine as propriedades de sua última execução de autoescala.

No .NET do lote, a propriedade [CloudPool. AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tem várias propriedades que fornecem informações sobre a execução de dimensionamento automático mais recente executada no pool:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na API REST, a solicitação [obter informações sobre um pool](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) retorna informações sobre o pool, que inclui as informações mais recentes de execução de dimensionamento automático na propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) .

O trecho C# de código a seguir usa a biblioteca .net do lote para imprimir informações sobre a última execução de autoescala no pool _mypool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemplo de saída do trecho anterior:

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

## <a name="example-autoscale-formulas"></a>Exemplo de fórmulas de autoescala

Vamos examinar algumas fórmulas que mostram diferentes maneiras de ajustar a quantidade de recursos de computação em um pool.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: ajuste baseado em tempo

Suponha que você deseja ajustar o tamanho do pool com base no dia da semana e na hora do dia. Este exemplo mostra como aumentar ou diminuir o número de nós no pool de acordo.

A fórmula primeiro obtém a hora atual. Se for um dia da semana (1-5) e no horário de trabalho (8:00 às 18:00), o tamanho do pool de destino será definido como 20 nós. Caso contrário, ele será definido como 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: ajuste baseado em tarefa

Neste exemplo, o tamanho do pool é ajustado com base no número de tarefas na fila. Os comentários e as quebras de linha são aceitáveis nas cadeias de caracteres de fórmula.

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
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: contabilidade para tarefas paralelas

Este exemplo ajusta o tamanho do pool com base no número de tarefas. Essa fórmula também leva em conta o valor de [MaxTasksPerComputeNode][net_maxtasks] que foi definido para o pool. Essa abordagem é útil em situações em que a [execução de tarefa paralela](batch-parallel-node-tasks.md) foi habilitada em seu pool.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: definindo um tamanho de pool inicial

Este exemplo mostra um C# trecho de código com uma fórmula de dimensionamento automático que define o tamanho do pool para um número especificado de nós por um período de tempo inicial. Em seguida, ele ajusta o tamanho do pool com base no número de tarefas em execução e ativas após o período de tempo inicial ter decorrido.

A fórmula no trecho de código a seguir:

* Define o tamanho inicial do pool para quatro nós.
* Não ajusta o tamanho do pool nos primeiros 10 minutos do ciclo de vida do pool.
* Após 10 minutos, obtém o valor máximo do número de tarefas em execução e ativas nos últimos 60 minutos.
  * Se ambos os valores forem 0 (indicando que nenhuma tarefa estava em execução ou ativo nos últimos 60 minutos), o tamanho do pool será definido como 0.
  * Se um valor for maior que zero, nenhuma alteração será feita.

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

## <a name="next-steps"></a>Passos seguintes

* [Maximizar o uso de recursos de computação do lote do Azure com tarefas de nó simultâneas](batch-parallel-node-tasks.md) contém detalhes sobre como você pode executar várias tarefas simultaneamente nos nós de computação em seu pool. Além do dimensionamento automático, esse recurso pode ajudar a reduzir a duração do trabalho para algumas cargas de trabalhos, economizando dinheiro.
* Para outro auxiliar de eficiência, verifique se o aplicativo do lote consulta o serviço de lote da maneira mais ideal. Consulte [consultar o serviço do lote do Azure com eficiência](batch-efficient-list-queries.md) para saber como limitar a quantidade de dados que cruzam a conexão quando você consulta o status de potencialmente milhares de nós ou tarefas de computação.

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
