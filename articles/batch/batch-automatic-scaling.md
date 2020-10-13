---
title: Dimensionar automaticamente os nós de computação de um conjunto do Azure Batch
description: Permita que o escalonamento automático numa piscina de nuvens ajuste dinamicamente o número de nós computacional na piscina.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 5774acbfc035ab61267dddb31b01b0e82689f690
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849797"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Crie uma fórmula automática para escalar os nóns computacional numa piscina de Lote

O Azure Batch pode escalar automaticamente os pools com base nos parâmetros que define, poupando-lhe tempo e dinheiro. Com o dimensionamento automático, o Batch adiciona dinamicamente os nós a um pool à medida que as exigências de tarefa aumentam, e remove os nós de computação à medida que as exigências de tarefa diminuem.

Para permitir a escala automática num conjunto de nós computacional, associa a piscina a uma *fórmula de autoescala* que define. O serviço Batch utiliza a fórmula de autoescalação para determinar quantos nós são necessários para executar a sua carga de trabalho. Estes nós podem ser nós dedicados ou [nós de baixa prioridade](batch-low-pri-vms.md). Em seguida, o lote irá analisar periodicamente os dados das métricas do serviço e usá-lo para ajustar o número de nós na piscina com base na sua fórmula e num intervalo que definir.

Pode ativar o dimensionamento automático quando criar uma piscina ou aplicá-la a uma piscina existente. O lote permite-lhe avaliar as suas fórmulas antes de as atribuir a piscinas e monitorizar o estado das corridas de escala automáticas. Uma vez configurar uma piscina com escala automática, pode fazer alterações na fórmula mais tarde.

> [!IMPORTANT]
> Quando criar uma conta Batch, pode especificar o [modo de atribuição](accounts.md)de piscinas, que determina se os pools são atribuídos numa subscrição de serviço Batch (o padrão) ou na subscrição do utilizador. Se criou a sua conta Batch com a configuração de serviço predefinido do Lote, então a sua conta está limitada a um número máximo de núcleos que podem ser utilizados para o processamento. O serviço de lote escala os nós de cálculo apenas até esse limite de núcleo. Por esta razão, o serviço Batch não pode atingir o número-alvo de nós de computação especificados por uma fórmula de autoescala. Consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter informações sobre a visualização e aumento das quotas da sua conta.
>
>Se criou a sua conta com o modo de subscrição do utilizador, então a sua conta partilha na quota principal da subscrição. Para obter mais informações, veja [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Limites das Máquinas Virtuais), em [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Limites, quotas e limitações das subscrições e serviços do Azure).

## <a name="autoscale-formulas"></a>Fórmulas de autoescala

Uma fórmula de escala automática é um valor de cadeia que se define que contém uma ou mais declarações. A fórmula de autoescalação é atribuída ao elemento [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (Batch REST) ou [cloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Lote.NET). O serviço Batch utiliza a sua fórmula para determinar o número alvo de nós de computação na piscina para o próximo intervalo de processamento. A cadeia de fórmula não pode exceder 8 KB, pode incluir até 100 declarações que são separadas por pontos e-tímricos, e pode incluir quebras de linha e comentários.

Pode pensar em fórmulas de escala automática como uma "linguagem" de autoescala do Batch. As declarações de fórmula são expressões de forma livre que podem incluir variáveis definidas pelo serviço (definidas pelo serviço Batch) e variáveis definidas pelo utilizador. As fórmulas podem realizar várias operações nestes valores utilizando tipos, operadores e funções incorporados. Por exemplo, uma declaração pode assumir o seguinte formulário:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

As fórmulas geralmente contêm múltiplas declarações que realizam operações sobre valores que são obtidos em declarações anteriores. Por exemplo, primeiro obtemos um valor `variable1` para, em seguida, passá-lo para uma função para `variable2` povoar:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua estas declarações na sua fórmula de autoescalação para chegar a um número de nós de computação. Nós dedicados e nós de baixa prioridade têm cada um as suas próprias definições de alvo. Uma fórmula de autoescala pode incluir um valor-alvo para nós dedicados, um valor-alvo para nós de baixa prioridade, ou ambos.

O número de nós-alvo pode ser maior, mais baixo ou o mesmo que o número atual de nós desse tipo na piscina. O lote avalia a fórmula de autoescala de uma piscina em [intervalos de escala automática específicos](#automatic-scaling-interval). O lote ajusta o número de alvo de cada tipo de nó na piscina ao número que a sua fórmula de autoescalação especifica no momento da avaliação.

### <a name="sample-autoscale-formulas"></a>Fórmulas de autoescala de amostra

Abaixo estão exemplos de duas fórmulas de autoescala, que podem ser ajustadas para trabalhar para a maioria dos cenários. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` as fórmulas de exemplo podem ser ajustadas às suas necessidades.

#### <a name="pending-tasks"></a>Tarefas pendentes

Com esta fórmula de autoescala, a piscina é inicialmente criada com um único VM. A `$PendingTasks` métrica define o número de tarefas que estão em execução ou em fila. A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define a `$TargetDedicatedNodes` variável em conformidade. A fórmula garante que o número-alvo de nós dedicados nunca exceda 25 VMs. À medida que novas tarefas são submetidas, o pool cresce automaticamente. À medida que as tarefas terminam, os VMs tornam-se livres e a fórmula de autoscalagem diminui a piscina.

Esta fórmula escala os nós dedicados, mas pode ser modificada para aplicar também aos nós de baixa prioridade.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Nódoas presas

Este exemplo cria uma piscina que começa com 25 nós de baixa prioridade. Sempre que um nó de baixa prioridade é antecipado, é substituído por um nó dedicado. Tal como no primeiro exemplo, a `maxNumberofVMs` variável impede que a piscina exceda 25 VMs. Este exemplo é útil para tirar partido de VMs de baixa prioridade, garantindo também que apenas um número fixo de preempções ocorrerá durante a vida útil da piscina.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Você vai aprender mais sobre [como criar fórmulas de autoescala](#write-an-autoscale-formula) e ver [fórmulas de autoescala](#example-autoscale-formulas) adicionais mais tarde neste tópico.

## <a name="variables"></a>Variáveis

Pode utilizar variáveis **definidas pelo serviço** e **definidas pelo utilizador** nas suas fórmulas de autoescala.

As variáveis definidas pelo serviço são incorporadas no serviço Batch. Algumas variáveis definidas pelo serviço são de leitura-escrita, e algumas são apenas de leitura.

As variáveis definidas pelo utilizador são variáveis que define. Na fórmula de exemplo acima mostrada, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço, enquanto `startingNumberOfVMs` são `maxNumberofVMs` variáveis definidas pelo utilizador.

> [!NOTE]
> As variáveis definidas pelo serviço são sempre precedidas por um sinal de dólar ($). Para variáveis definidas pelo utilizador, o sinal de dólar é opcional.

As tabelas que se seguem mostram as variáveis de leitura e leitura que são definidas pelo serviço Batch.

### <a name="read-write-service-defined-variables"></a>Variáveis definidas por serviço de leitura

Você pode obter e definir os valores destas variáveis definidas pelo serviço para gerir o número de nós computacional em uma piscina.

| Variável | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número-alvo de nós computacional dedicados para a piscina. Isto é especificado como um alvo porque uma piscina pode nem sempre alcançar o número desejado de nós. Por exemplo, se o número de nós específicos for modificado por uma avaliação de autoescala antes de o pool atingir o objetivo inicial, o pool pode não atingir o alvo. <br /><br /> Um pool numa conta criada no modo de serviço Batch pode não atingir o seu objetivo se o objetivo exceder um nó de conta batch ou uma quota principal. Um pool numa conta criada no modo de subscrição do utilizador pode não atingir o seu objetivo se o objetivo exceder a quota-base partilhada para a subscrição.|
| $TargetLowPriorityNodes |O número-alvo de nós de computação de baixa prioridade para a piscina. Isto especificado como um alvo porque uma piscina pode nem sempre alcançar o número desejado de nós. Por exemplo, se o número-alvo de nós de baixa prioridade for modificado por uma avaliação de autoescala antes de o pool atingir o objetivo inicial, o pool pode não atingir o alvo. Um pool também não pode atingir o seu objetivo se o alvo exceder um nó de conta batch ou uma quota principal. <br /><br /> Para obter mais informações sobre nós de computação de baixa prioridade, consulte [utilizar VMs de baixa prioridade com Lote](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando os nós computadores são removidos de uma piscina. Os valores possíveis são:<ul><li>**requeue**: O valor predefinido. Termina as tarefas imediatamente e coloca-as de volta na fila de trabalho para que sejam reagendadas. Esta ação garante que o número de nós-alvo seja atingido o mais rapidamente possível. No entanto, pode ser menos eficiente, uma vez que quaisquer tarefas de execução serão interrompidas e terão de ser completamente reiniciadas. <li>**terminar:** Acaba imediatamente com as tarefas e retira-as da fila de trabalho.<li>**taskcompleletion**: Espera que as tarefas atualmente em execução terminem e, em seguida, remova o nó da piscina. Utilize esta opção para evitar que as tarefas sejam interrompidas e requeadas, desperdiçando qualquer trabalho que a tarefa tenha feito.<li>**retenção de dados**: Aguarda que todos os dados locais retidos no nó sejam limpos antes de retirar o nó do pool.</ul> |

> [!NOTE]
> A `$TargetDedicatedNodes` variável também pode ser especificada usando o pseudónimo `$TargetDedicated` . Da mesma forma, a `$TargetLowPriorityNodes` variável pode ser especificada usando o pseudónimo `$TargetLowPriority` . Se tanto a variável totalmente nomeada como o seu pseudónimo forem definidos pela fórmula, o valor atribuído à variável totalmente nomeada terá precedência.

### <a name="read-only-service-defined-variables"></a>Variáveis definidas apenas por serviços

Pode obter o valor destas variáveis definidas pelo serviço para fazer ajustes que são baseados em métricas do serviço Batch.

> [!IMPORTANT]
> As tarefas de libertação de emprego não estão atualmente incluídas em variáveis que fornecem contagens de tarefas, tais como $ActiveTasks e $PendingTasks. Dependendo da sua fórmula de autoescala, isto pode resultar na remoção de nós sem nós disponíveis para executar tarefas de libertação de emprego.

| Variável | Descrição |
| --- | --- |
| $CPUPercent |A percentagem média de utilização do CPU. |
| $WallClockSeconds |O número de segundos consumidos. |
| $MemoryBytes |O número médio de megabytes usados. |
| $DiskBytes |O número médio de gigabytes usados nos discos locais. |
| $DiskReadBytes |O número de bytes lidos. |
| $DiskWriteBytes |O número de bytes escritos. |
| $DiskReadOps |A contagem de operações de disco de leitura realizadas. |
| $DiskWriteOps |A contagem de operações de disco de escrita realizadas. |
| $NetworkInBytes |O número de bytes de entrada. |
| $NetworkOutBytes |O número de bytes de saída. |
| $SampleNodeCount |A contagem de nós computacional. |
| $ActiveTasks |O número de tarefas que estão prontas a executar mas que ainda não estão a ser executadas. Isto inclui todas as tarefas que se encontram no estado ativo e cujas dependências foram satisfeitas. Quaisquer tarefas que estejam no estado ativo, mas cujas dependências não foram satisfeitas, estão excluídas da contagem $ActiveTasks. Para uma tarefa multi-instância, $ActiveTasks incluirá o número de casos definidos na tarefa.|
| $RunningTasks |O número de tarefas num estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que terminaram com sucesso. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicatedNodes |O número atual de nós computacional dedicados. |
| $CurrentLowPriorityNodes |O número atual de nós computacional de baixa prioridade, incluindo quaisquer nós que tenham sido antecipados. |
| $PreemptedNodeCount | O número de nós na piscina que estão em estado preemptido. |

> [!TIP]
> Estas variáveis definidas apenas por serviço são *objetos* que fornecem vários métodos de acesso aos dados associados a cada um. Para mais informações, consulte [Obter dados da amostra](#obtain-sample-data) mais tarde neste artigo.

## <a name="types"></a>Tipos

As fórmulas de autoescala suportam os seguintes tipos:

- double
- doubleVec
- doubleVecList
- string
- timetamp -- uma estrutura composta que contém os seguintes membros:
  - ano
  - mês (1-12)
  - dia (1-31)
  - dia da semana (no formato de número; por exemplo, 1 para segunda-feira)
  - hora (em formato número 24 horas; por exemplo, 13 significa 1 PM)
  - minuto (00-59)
  - segundo (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Operações

Estas operações são permitidas nos tipos listados na secção anterior.

| Operação | Operadores apoiados | Tipo de resultado |
| --- | --- | --- |
| *duplo operador* duplo |+, -, *, / |double |
| *tempointerval do operador* duplo |* |timeinterval |
| duplo *operadorvec* duplo |+, -, *, / |doubleVec |
| doubleVec *operador* doubleVec |+, -, *, / |doubleVec |
| *timeinterval operador* duplo |*, / |timeinterval |
| timeinterval *operador* timeinterval |+, - |timeinterval |
| timeinterval *operador* relógios |+ |carimbo de data/hora |
| timetamp *operador* timeinterval |+ |carimbo de data/hora |
| timetamp *operador* |- |timeinterval |
| *operador* duplo |-, ! |double |
| *timeinterval operador* |- |timeinterval |
| *duplo operador* duplo |<, <== =, >=, >, != |double |
| cadeia *de operador de* cordas |<, <== =, >=, >, != |double |
| timetamp *operador* |<, <== =, >=, >, != |double |
| timeinterval *operador* timeinterval |<, <== =, >=, >, != |double |
| *duplo operador* duplo |&&, &#124;&#124; |double |

Ao testar um duplo com um operador ternário `double ? statement1 : statement2` (), não fazer nada é **verdade,** e zero é **falso**.

## <a name="functions"></a>Funções

Pode utilizar estas **funções** predefinidas ao definir uma fórmula de autoescala.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| avg (doubleVecList) |double |Devolve o valor médio para todos os valores na DoubleVecList. |
| len (doubleVecList) |double |Devolve o comprimento do vetor que é criado a partir do doubleVecList. |
| lg(duplo) |double |Devolve a base de registo 2 do duplo. |
| lg (doubleVecList) |doubleVec |Devolve a base de registo em termos de componente 2 da DoubleVecList. Um vec(duplo) deve ser explicitamente passado para o parâmetro. Caso contrário, assume-se a versão dupla lg(duplo). |
| In(duplo) |double |Devolve o registo natural do duplo. |
| In (doubleVecList) |doubleVec |Devolve o registo natural do duplo. |
| log (duplo) |double |Devolve a base de registo 10 do duplo. |
| log (doubleVecList) |doubleVec |Devolve a base de registo em termos de componente 10 da DoubleVecList. Um vec(duplo) deve ser explicitamente passado para o único parâmetro duplo. Caso contrário, assume-se a versão de registo duplo(duplo). |
| max (doubleVecList) |double |Devolve o valor máximo na DoubleVecList. |
| min(doubleVecList) |double |Devolve o valor mínimo na DoubleVecList. |
| norma (doubleVecList) |double |Devolve a duas normas do vetor que é criado a partir do doubleVecList. |
| percentil (doubleVec v, duplo p) |double |Devolve o elemento percentil do vetor v. |
| rand() |double |Devolve um valor aleatório entre 0.0 e 1.0. |
| gama (doubleVecList) |double |Devolve a diferença entre os valores min e max no DoubleVecList. |
| std (doubleVecList) |double |Devolve o desvio padrão da amostra dos valores na DoubleVecList. |
| paragem() | |Para a avaliação da expressão auto-caling. |
| soma (doubleVecList) |double |Devolve a soma de todos os componentes da DoubleVecList. |
| tempo (data de cadeiaTime=") |carimbo de data/hora |Devolve o carimbo de tempo da hora atual se não forem passados parâmetros ou o carimbo de hora da cadeia Time se tal for passado. Data suportada Os formatos tempos são W3C-DTF e RFC 1123. |
| val (doubleVec v, double i) |double |Devolve o valor do elemento que está na localização i no vetor v, com um índice inicial de zero. |

Algumas das funções descritas na tabela anterior podem aceitar uma lista como argumento. A lista separada por vírgula é qualquer combinação de *Duplo* e *DuploVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor *duploVecList* é convertido para um único *DuploVec* antes da avaliação. Por exemplo, `v = [1,2,3]` se, então, ligar `avg(v)` é equivalente a chamar `avg(1,2,3)` . Ligar `avg(v, 7)` é equivalente a `avg(1,2,3,7)` ligar.

## <a name="metrics"></a>Métricas

Pode utilizar as métricas de recursos e tarefas quando estiver a definir uma fórmula. Ajusta o número-alvo de nós dedicados na piscina com base nos dados métricos que obtém e avalia. Para obter mais informações sobre cada métrica, consulte a secção [Variáveis](#variables) acima.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>As métricas de recursos baseiam-se no CPU, na largura de banda, no uso da memória dos nós computacional e no número de nós.</p>
        <p> Estas variáveis definidas pelo serviço são úteis para fazer ajustes com base na contagem de nó:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo serviço são úteis para fazer ajustes com base na utilização do recurso nó:</p>
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
    <td><p>As métricas de tarefa baseiam-se no estado das tarefas, tais como Ative, Pendente e Concluído. As seguintes variáveis definidas pelo serviço são úteis para fazer ajustes de tamanho de piscina com base em métricas de tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Obter dados de amostra

O funcionamento principal de uma fórmula de autoescala é obter dados de tarefa e métrica de recursos (amostras) e, em seguida, ajustar o tamanho da piscina com base nesses dados. Como tal, é importante ter uma compreensão clara de como as fórmulas de autoescala interagem com amostras.

### <a name="methods"></a>Métodos

As fórmulas de autoescala atuam em amostras de dados métricos fornecidos pelo serviço Batch. Uma fórmula irá crescer ou diminuir o tamanho da piscina com base nos valores que obtém. As variáveis definidas pelo serviço são objetos que fornecem métodos de acesso a dados que estão associados a esse objeto. Por exemplo, a seguinte expressão mostra um pedido para obter os últimos cinco minutos de utilização do CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Podem ser utilizados os seguintes métodos para obter dados de amostra sobre variáveis definidas pelo serviço.

| Método | Descrição |
| --- | --- |
| GetSample() |O `GetSample()` método devolve um vetor de amostras de dados.<br/><br/>Uma amostra tem 30 segundos de dados métricos. Por outras palavras, as amostras são obtidas a cada 30 segundos. Mas, como se nota abaixo, há um atraso entre quando uma amostra é recolhida e quando está disponível para uma fórmula. Como tal, nem todas as amostras durante um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`: Especifica o número de amostras a obter das amostras mais recentes recolhidas. `GetSample(1)` devolve a última amostra disponível. Para métricas como `$CPUPercent` , no entanto, `GetSample(1)` não deve ser usado, porque é impossível saber *quando* a amostra foi recolhida. Pode ser recente, ou, devido a problemas do sistema, pode ser muito mais antigo. Nesses casos, é melhor usar um intervalo de tempo como mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Especifica um prazo para a recolha de dados da amostra. Opcionalmente, especifica também a percentagem de amostras que devem estar disponíveis no prazo solicitado. Por exemplo, `$CPUPercent.GetSample(TimeInterval_Minute * 10)` devolveria 20 amostras se todas as amostras dos últimos 10 minutos estiverem presentes na `CPUPercent` história. Se o último minuto da história não estivesse disponível, apenas 18 amostras seriam devolvidas. Neste caso `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` falharia porque apenas 90% das amostras estão disponíveis, mas `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` seriam bem sucedidas.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Especifica um prazo para a recolha de dados, com uma hora de início e um tempo de fim. Como mencionado acima, há um atraso entre quando uma amostra é recolhida e quando fica disponível para uma fórmula. Considere este atraso quando utilizar o `GetSample` método. Veja `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Devolve o período de amostras que foram colhidas num conjunto histórico de dados de amostras. |
| Contagem() |Devolve o número total de amostras na história métrica. |
| HistoryBeginTime() |Devolve o carimbo de tempo da amostra de dados mais antiga disponível para a métrica. |
| GetSamplePercent() |Devolve a percentagem de amostras disponíveis para um determinado intervalo de tempo. Por exemplo, `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Como o `GetSample` método falha se a percentagem de amostras devolvidas for inferior à `samplePercent` especificada, pode utilizar o `GetSamplePercent` método para verificar primeiro. Em seguida, pode efetuar uma ação alternativa se estiverem presentes amostras insuficientes, sem travar a avaliação automática de escalonamento. |

### <a name="samples"></a>Amostras

O serviço Batch retira periodicamente amostras de métricas de tarefa e recursos e coloca-as à disposição das suas fórmulas de autoescala. Estas amostras são registadas a cada 30 segundos pelo serviço Batch. No entanto, existe tipicamente um atraso entre quando essas amostras foram gravadas e quando são disponibilizadas para (e podem ser lidas) as suas fórmulas de autoescala. Além disso, as amostras não podem ser registadas num intervalo específico devido a fatores como a rede ou outras questões de infraestruturas.

### <a name="sample-percentage"></a>Percentagem de amostra

Quando `samplePercent` é passado para o método ou o método é `GetSample()` `GetSamplePercent()` chamado, por _cento_ refere-se a uma comparação entre o número total possível de amostras que são registadas pelo serviço Batch e o número de amostras que estão disponíveis para a sua fórmula de autoescala.

Vejamos um intervalo de 10 minutos como um exemplo. Como as amostras são registadas a cada 30 segundos dentro desse intervalo de 10 minutos, o número máximo total de amostras registadas pelo Batch seria de 20 amostras (2 por minuto). No entanto, devido à latência inerente do mecanismo de reporte e outras questões dentro do Azure, pode haver apenas 15 amostras que estão disponíveis na sua fórmula de autoescala para leitura. Assim, por exemplo, para esse período de 10 minutos, apenas 75% do número total de amostras registadas podem estar disponíveis na sua fórmula.

### <a name="getsample-and-sample-ranges"></a>Gamas getSample e amostras

As suas fórmulas de autoescala irão crescer e diminuir as suas piscinas adicionando ou removendo nós. Como os nós lhe custam dinheiro, certifique-se de que as suas fórmulas usam um método inteligente de análise que se baseia em dados suficientes. Recomendamos que utilize uma análise de tendência nas suas fórmulas. Este tipo cresce e encolhe as suas piscinas com base numa gama de amostras recolhidas.

Para tal, utilize `GetSample(interval look-back start, interval look-back end)` para devolver um vetor de amostras:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada por Batch, retorna uma gama de amostras como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Uma vez recolhido o vetor de amostras, pode então usar funções como `min()` `max()` , e obter `avg()` valores significativos da gama recolhida.

Para uma segurança adicional, pode forçar uma avaliação de fórmula a falhar se houver menos de uma determinada percentagem de amostra disponível por um determinado período de tempo. Quando forçar uma avaliação de fórmula a falhar, instrui o Batch a cessar a avaliação da fórmula se a percentagem especificada de amostras não estiver disponível. Neste caso, não é feita nenhuma alteração ao tamanho da piscina. Para especificar uma percentagem necessária de amostras para que a avaliação tenha êxito, especifique-a como o terceiro parâmetro para `GetSample()` . Aqui, é especificado um requisito de 75 por cento das amostras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Como pode haver um atraso na disponibilidade da amostra, deve sempre especificar um intervalo de tempo com um tempo de retrocesso que seja superior a um minuto. Leva aproximadamente um minuto para que as amostras se propaguem através do sistema, pelo que as amostras na gama `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` podem não estar disponíveis. Mais uma vez, pode utilizar o parâmetro percentual de forçar um determinado requisito de percentagem da `GetSample()` amostra.

> [!IMPORTANT]
> Recomendamos vivamente que **evite confiar *apenas* `GetSample(1)` nas suas fórmulas de autoescala**. Isto `GetSample(1)` porque, essencialmente, diz ao serviço Batch: "Dê-me a última amostra que tiver, não importa há quanto tempo a recuperou." Uma vez que se trata apenas de uma única amostra, e pode ser uma amostra mais antiga, pode não ser representativa da imagem maior do estado de tarefa ou recursos recentes. Se `GetSample(1)` utilizar, certifique-se de que faz parte de uma declaração maior e não o único ponto de dados em que a sua fórmula depende.

## <a name="write-an-autoscale-formula"></a>Escreva uma fórmula de autoescala

Constrói uma fórmula de autoescala formando declarações que utilizam os componentes acima e, em seguida, combina essas declarações numa fórmula completa. Nesta secção, criamos uma fórmula de autoescala exemplo que pode executar decisões de escala no mundo real e fazer ajustes.

Primeiro, vamos definir os requisitos para a nossa nova fórmula de autoescala. A fórmula deve:

- Aumente o número-alvo de nós computacional dedicados numa piscina se o uso do CPU for elevado.
- Diminua o número-alvo de nós computacional dedicados numa piscina quando o uso do CPU é baixo.
- Restrinja sempre o número máximo de nós dedicados a 400.
- Ao reduzir o número de nós, não remova os nós que estão a executar tarefas; se necessário, aguarde até que as tarefas tenham terminado antes de remover os nós.

A primeira declaração na nossa fórmula aumentará o número de nós durante o uso elevado do CPU. Vamos definir uma afirmação que povoa uma variável definida pelo utilizador `$totalDedicatedNodes` com um valor que é 110 por cento do número atual de nós dedicados, mas apenas se o uso médio mínimo de CPU durante os últimos 10 minutos fosse superior a 70 por cento. Caso contrário, utiliza o valor para o número atual de nós dedicados.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para diminuir o número de nós dedicados durante o baixo uso do CPU, a próxima declaração na nossa fórmula define a mesma `$totalDedicatedNodes` variável para 90 por cento do número atual de nós dedicados, se o uso médio do CPU nos últimos 60 minutos foi inferior a 20 por cento. Caso contrário, utiliza o valor atual do `$totalDedicatedNodes` que povoamos na declaração acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora, vamos limitar o número alvo de nós computacional dedicados a um máximo de 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Finalmente, vamos garantir que os nós não sejam removidos até que as suas tarefas estejam terminadas.

```
$NodeDeallocationOption = taskcompletion;
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
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Se optar, pode incluir comentários e quebras de linha em cadeias de fórmula.

## <a name="automatic-scaling-interval"></a>Intervalo de escala automático

Por predefinição, o serviço Batch ajusta o tamanho da piscina de acordo com a sua fórmula de autoescala a cada 15 minutos. Este intervalo é configurável utilizando as seguintes propriedades da piscina:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Lote.NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

O intervalo mínimo é de cinco minutos, e o máximo é de 168 horas. Se for especificado um intervalo fora deste intervalo, o serviço Batch retorna um erro de Mau Pedido (400).

> [!NOTE]
> A autoscalagem não se destina atualmente a responder a alterações em menos de um minuto, mas destina-se a ajustar gradualmente o tamanho da sua piscina à medida que funciona uma carga de trabalho.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Criar uma piscina com autoescala com SDKs de lote

A autoscalagem da piscina pode ser configurada utilizando qualquer um dos [SDKs de lote,](batch-apis-tools.md#azure-accounts-for-batch-development)os [cmdlets powershell do lote](batch-powershell-cmdlets-get-started.md) [REST,](/rest/api/batchservice/) e o [Lote CLI](batch-cli-get-started.md). Nesta secção, pode ver exemplos tanto para .NET como para Python.

### <a name="net"></a>.NET

Para criar uma piscina com autoscalagem ativada em .NET, siga estes passos:

1. Crie a piscina com [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Desafie a propriedade [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) para `true` .
1. Desave a propriedade [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) com a sua fórmula de autoescala.
1. (Opcional) Desafie a propriedade [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (o padrão é de 15 minutos).
1. Comprometa a piscina com [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O exemplo a seguir cria uma piscina ativada por escala automática em .NET. A fórmula de autoescala da piscina define o número de nós dedicados para 5 às segundas-feiras, e para 1 em cada dois dias da semana. O [intervalo de escala automático](#automatic-scaling-interval) está definido para 30 minutos. Neste e nos outros snippets C# neste artigo, `myBatchClient` é uma instância devidamente inicializada da classe [BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

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
> Quando criar uma piscina ativada por escala automática, não especifique o parâmetro _TargetDedicatedNodes_ ou o parâmetro _targetLowPriorityNodes_ na chamada para **CreatePool**. Em vez disso, especifique as propriedades **AutoScaleEnabled** e **AutoScaleFormula** na piscina. Os valores destas propriedades determinam o número alvo de cada tipo de nó.
>
> Para redimensionar manualmente uma piscina com uma escala automática (por exemplo, com [BatchClient.PoolOperations.ResizePoolAsync),](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)primeiro deve desativar a escala automática na piscina e depois redimensioná-la.

### <a name="python"></a>Python

Para criar piscina com uma escala automática com o Python SDK:

1. Crie uma piscina e especifique a sua configuração.
1. Adicione a piscina ao cliente de serviço.
1. Ative a autoescala na piscina com uma fórmula que escreve.

O exemplo que se segue ilustra estes passos.

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
> Mais exemplos de utilização do Python SDK podem ser encontrados no [repositório DeStart Quickstart Batch Python](https://github.com/Azure-Samples/batch-python-quickstart) no GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Ativar a autoscalagem numa piscina existente

Cada Batch SDK fornece uma forma de permitir a escala automática. Por exemplo:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Lote.NET)
- [Permitir a escala automática numa piscina](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Quando ativar a autoscalagem numa piscina existente, lembre-se:

- Se a autoscalagem estiver atualmente desativada na piscina, tem de especificar uma fórmula de autoescala válida quando emitir o pedido. Pode especificar opcionalmente um intervalo de escala automático. Se não especificar um intervalo, utiliza-se o valor predefinido de 15 minutos.
- Se a autoscalagem estiver ativada na piscina, pode especificar uma nova fórmula, um novo intervalo ou ambos. Deve especificar pelo menos uma destas propriedades.
  - Se especificar um novo intervalo de escala automático, o horário existente é interrompido e um novo horário é iniciado. A hora de início do novo horário é o momento em que foi emitido o pedido de autoscalagem.
  - Se omitir a fórmula de autoescala ou o intervalo, o serviço Batch continuará a utilizar o valor atual dessa definição.

> [!NOTE]
> Se especificou valores para os *parâmetros targetDedicatedNodes* ou *targetLowPriorityNodes* do método **CreatePool** quando criou o pool em .NET, ou para os parâmetros comparáveis noutra língua, então esses valores são ignorados quando a fórmula automática é avaliada.

Este exemplo C# utiliza a biblioteca [Batch .NET](/dotnet/api/microsoft.azure.batch) para permitir a autoscalagem numa piscina existente.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de autoescala

Para atualizar a fórmula num pool autoescalado existente, ligue para a operação para permitir a autoscalagem novamente com a nova fórmula. Por exemplo, se a autoscalagem já estiver ativada `myexistingpool` quando o seguinte código .NET for executado, a sua fórmula de autoescala é substituída pelo conteúdo de `myNewFormula` .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de autoescala

Para atualizar o intervalo de avaliação de autoescala de um pool autoescalado existente, ligue para a operação para permitir a autoscalagem novamente com o novo intervalo. Por exemplo, para definir o intervalo de avaliação de autoescala para 60 minutos para uma piscina que já está ativada automaticamente em .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de autoescala

Você pode avaliar uma fórmula antes de aplicá-la em uma piscina. Isto permite-lhe testar os resultados da fórmula antes de a colocar em produção.

Antes de poder avaliar uma fórmula de autoescala, tem primeiro de ativar a autoscalagem na piscina com uma fórmula válida, como a fórmula de uma linha `$TargetDedicatedNodes = 0` . Em seguida, utilize uma das seguintes para avaliar a fórmula que pretende testar:

- [BatchClient.PoolOperations.AssessAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [AssessAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Estes métodos Batch .NET requerem a identificação de uma piscina existente e uma corda que contenha a fórmula de autoescala para avaliar.

- [Avaliar uma fórmula de escala automática](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Neste pedido de API REST, especifique o ID do pool no URI e a fórmula de autoescalação no elemento *autoScaleFormula* do corpo de pedido. A resposta da operação contém qualquer informação de erro que possa estar relacionada com a fórmula.

Este exemplo [batch .NET](/dotnet/api/microsoft.azure.batch) avalia uma fórmula de autoescala. Se a piscina já não utilizar autoscaling, ativamos primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
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

Uma avaliação bem sucedida da fórmula apresentada neste código produz resultados semelhantes:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtenha informações sobre corridas de autoescala

Para garantir que a sua fórmula está a funcionar como esperado, recomendamos que verifique periodicamente os resultados das correções de autoscalagem que o Batch executa na sua piscina. Para tal, obtenha (ou refresque) uma referência à piscina e, em seguida, examine as propriedades da sua última corrida de autoescala.

Em Batch .NET, a propriedade [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tem várias propriedades que fornecem informações sobre a mais recente corrida de escala automática realizada na piscina:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [Resultados autoScaleRun.](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [Erro autoScaleRun.Erro](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na API REST, o [Get information about a pool](/rest/api/batchservice/get-information-about-a-pool) request devolve informações sobre o pool, que inclui as mais recentes informações automáticas de escala na propriedade [autoScaleRun.](/rest/api/batchservice/get-information-about-a-pool)

O exemplo C# a seguir utiliza a biblioteca Batch .NET para imprimir informações sobre a última autoscalagem executada na _piscina myPool_.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Saída da amostra do exemplo anterior:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Obtenha o histórico de corridas de autoescala usando eventos de autoescala de piscina
Também pode verificar o histórico de escalas automáticas consultando [PoolAutoScaleEvent](batch-pool-autoscale-event.md). Este evento é emitido pelo Batch Service para registar cada ocorrência de avaliação e execução de fórmulas de autoescalação, o que pode ser útil para resolver potenciais problemas.

Evento de amostra para PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Fórmulas de autoescala exemplo

Vejamos algumas fórmulas que mostram diferentes formas de ajustar a quantidade de recursos computativos numa piscina.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: Regulação por tempo

Suponha que queira ajustar o tamanho da piscina com base no dia da semana e na hora do dia. Este exemplo mostra como aumentar ou diminuir o número de nós na piscina em conformidade.

A fórmula obtém primeiro o tempo atual. Se for um dia de semana (1-5) e dentro do horário de trabalho (8:00 às 18:00), o tamanho da piscina-alvo está definido para 20 nós. Caso contrário, está definido para 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` pode ser ajustado para refletir o seu fuso horário local adicionando `time()` ao produto e ao seu offset `TimeZoneInterval_Hour` UTC. Por exemplo, utilize `$curTime = time() + (-6 * TimeInterval_Hour);` para o horário de verão da montanha (MDT). Tenha em mente que a compensação teria de ser ajustada no início e no fim do horário de verão (se aplicável).

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: Ajustamento baseado em tarefas

Neste exemplo C#, o tamanho da piscina é ajustado com base no número de tarefas na fila. Incluímos comentários e quebras de linha nas cordas de fórmula.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: Contabilização de tarefas paralelas

Este exemplo C# ajusta o tamanho da piscina com base no número de tarefas. Esta fórmula também tem em conta o valor [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) que foi definido para o pool. Esta abordagem é útil em situações em que a [execução paralela](batch-parallel-node-tasks.md) de tarefas foi ativada na sua piscina.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: Definir um tamanho inicial da piscina

Este exemplo mostra um exemplo C# com uma fórmula de autoescala que define o tamanho da piscina num determinado número de nós durante um período de tempo inicial. Depois disso, ajusta o tamanho da piscina com base no número de tarefas em execução e ativas.

Especificamente, esta fórmula faz o seguinte:

- Define o tamanho inicial da piscina em quatro nós.
- Não ajuste o tamanho da piscina nos primeiros 10 minutos do ciclo de vida da piscina.
- Após 10 minutos, obtém o valor máximo do número de tarefas de execução e ativas nos últimos 60 minutos.
  - Se ambos os valores forem 0 (indicando que nenhuma tarefa estava em execução ou ativa nos últimos 60 minutos), o tamanho da piscina está definido para 0.
  - Se um dos valores for maior do que zero, não é feita nenhuma alteração.

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

- Saiba como [executar várias tarefas simultaneamente nos nós de computação na sua piscina](batch-parallel-node-tasks.md). Juntamente com o autoscaling, isto pode ajudar a reduzir a duração do trabalho para algumas cargas de trabalho, poupando-lhe dinheiro.
- Saiba como [consultar o serviço Azure Batch de forma eficiente](batch-efficient-list-queries.md) para obter uma maior eficiência.
