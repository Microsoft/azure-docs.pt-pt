---
title: Escala automaticamente os nódosos de computação numa piscina de Lote Azure  Microsoft Docs
description: Ative a escala automática numa piscina de nuvem para ajustar dinamicamente o número de nós de computação na piscina.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 46be210ead3816356b63293b910e1c0e7ffc087b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200100"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Crie uma fórmula automática para a escala de cafés de computação em uma piscina de lote

O Lote Azure pode escalar automaticamente as piscinas com base em parâmetros que define. Com a escala automática, o Lote adiciona os nós a uma piscina à medida que as exigências de tarefa aumentam, e remove os nós de cálculo à medida que diminuem. Pode economizar tempo e dinheiro ajustando automaticamente o número de nós de computação utilizados pela sua aplicação Batch.

Permite a escala automática numa piscina de nódosos computacionais associando-se a ele uma fórmula de *escala automática* que define. O serviço Batch utiliza a fórmula de escala automática para determinar o número de nós de computação que são necessários para executar a sua carga de trabalho. Os nódos os comandados podem ser nódosos dedicados ou [nódosdeos de baixa prioridade.](batch-low-pri-vms.md) O lote responde aos dados das métricas de serviço que são recolhidos periodicamente. Utilizando estes dados de métricas, o Batch ajusta o número de nós de computação na piscina com base na sua fórmula e num intervalo configurável.

Você pode ativar a escala automática quando uma piscina é criada, ou em uma piscina existente. Também pode alterar uma fórmula existente numa piscina que está configurada para autoscalcificar. O lote permite avaliar as suas fórmulas antes de as atribuir às piscinas e monitorizar o estado das operações de escala automática.

Este artigo discute as várias entidades que compõem as suas fórmulas de escala automática, incluindo variáveis, operadores, operações e funções. Discutimos como obter vários recursos computacionais e métricas de tarefa dentro do Lote. Pode utilizar estas métricas para ajustar a contagem de nódoas do seu pool com base no uso do recurso e no estado de tarefa. Em seguida, descrevemos como construir uma fórmula e permitir a escala automática numa piscina utilizando tanto o Lote REST como as APIs .NET. Finalmente, terminamos com algumas fórmulas de exemplo.

> [!IMPORTANT]
> Quando criar uma conta 'Lote', pode especificar a configuração da [conta,](batch-api-basics.md#account)que determina se os pools são atribuídos numa subscrição de serviço do Batch (o predefinido) ou na subscrição do utilizador. Se criou a sua conta De Lote com a configuração padrão do Serviço de Lote, então a sua conta está limitada a um número máximo de núcleos que podem ser usados para processamento. O serviço de lote escala sinos de cálculo apenas até esse limite de núcleo. Por esta razão, o serviço Batch não pode atingir o número-alvo de nós de cálculo especificados por uma fórmula de escala automática. Consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter informações sobre visualização e aumento das quotas de conta.
>
>Se criou a sua conta com a configuração de Subscrição do Utilizador, então a sua conta partilha na quota central para a subscrição. Para obter mais informações, veja [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Limites das Máquinas Virtuais), em [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Limites, quotas e limitações das subscrições e serviços do Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Fórmulas de escala automática

Uma fórmula de escala automática é um valor de cadeia que se define que contém uma ou mais declarações. A fórmula de escala automática é atribuída ao elemento [autoScaleFormula][rest_autoscaleformula] (Batch REST) ou [à propriedade CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). O serviço Batch utiliza a sua fórmula para determinar o número de dados de cálculo na piscina para o próximo intervalo de processamento. A cadeia de fórmulas não pode exceder 8 KB, pode incluir até 100 declarações separadas por pontos evímetros, e pode incluir quebras de linha e comentários.

Pode pensar em fórmulas de escala automática como uma "linguagem" de escala automática de Lote. As declarações de fórmula são expressões formadas livremente que podem incluir variáveis definidas pelo serviço (variáveis definidas pelo serviço De lote) e variáveis definidas pelo utilizador (variáveis que define). Podem realizar várias operações sobre estes valores utilizando tipos, operadores e funções incorporados. Por exemplo, uma declaração pode assumir o seguinte formulário:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

As fórmulas geralmente contêm múltiplas declarações que realizam operações sobre valores que são obtidos em declarações anteriores. Por exemplo, primeiro obtemos um valor para `variable1`, depois passamos para uma função de povoar `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua estas declarações na sua fórmula de escala automática para chegar a um número de nós de computação. Nós dedicados e nós de baixa prioridade cada um tem as suas próprias configurações de alvo, para que você possa definir um alvo para cada tipo de nó. Uma fórmula de escala automática pode incluir um valor-alvo para nós dedicados, um valor-alvo para nós de baixa prioridade, ou ambos.

O número alvo de nós pode ser maior, inferior ou o mesmo que o número atual de nós desse tipo na piscina. O lote avalia a fórmula de escala automática de uma piscina num intervalo específico (ver [intervalos automáticos](#automatic-scaling-interval)de escala). O lote ajusta o número-alvo de cada tipo de nó na piscina ao número que a sua fórmula de escala automática especifica no momento da avaliação.

### <a name="sample-autoscale-formulas"></a>Fórmulas de escala automática de amostra

Abaixo estão exemplos de duas fórmulas de escala automática, que podem ser ajustadas para funcionar para a maioria dos cenários. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` nas fórmulas de exemplo podem ser ajustadas às suas necessidades.

#### <a name="pending-tasks"></a>Tarefas pendentes

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Com esta fórmula de escala automática, a piscina é inicialmente criada com um único VM. A métrica `$PendingTasks` define o número de tarefas que estão a decorrer ou em fila. A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define a variável `$TargetDedicatedNodes` em conformidade. A fórmula garante que o número-alvo de nós dedicados nunca excede 25 VMs. À medida que novas tarefas são submetidas, a piscina cresce automaticamente. À medida que as tarefas completam, os VMs tornam-se gratuitos um a um e a fórmula autoscalcificante encolhe a piscina.

Esta fórmula escala nós dedicados, mas pode ser modificado para aplicar à escala de nós de baixa prioridade também.

#### <a name="preempted-nodes"></a>Nódosos pré-empreitados 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Este exemplo cria uma piscina que começa com 25 nós de baixa prioridade. Sempre que um nó de baixa prioridade é preempted, é substituído por um nó dedicado. Tal como no primeiro exemplo, a variável `maxNumberofVMs` impede que a piscina exceda 25 VMs. Este exemplo é útil para tirar partido de VMs de baixa prioridade, garantindo também que apenas um número fixo de preventivos ocorrerá durante toda a vida útil da piscina.

## <a name="variables"></a>Variáveis

Pode utilizar variáveis **definidas** pelo serviço e **definidas** pelo utilizador nas fórmulas de escala automática. As variáveis definidas pelo serviço são incorporadas no serviço Batch. Algumas variáveis definidas pelo serviço são de leitura-escrita, e algumas são apenas de leitura. Variáveis definidas pelo utilizador são variáveis que define. Na fórmula de exemplo mostrada na secção anterior, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço. Variáveis `startingNumberOfVMs` e `maxNumberofVMs` são variáveis definidas pelo utilizador.

> [!NOTE]
> As variáveis definidas pelo serviço são sempre precedidas por um sinal de dólar ($). Para variáveis definidas pelo utilizador, o sinal do dólar é opcional.
>
>

As tabelas seguintes mostram variáveis de leitura e leitura apenas definidas pelo serviço Batch.

Você pode obter e definir os valores destas variáveis definidas pelo serviço para gerir o número de nós de computação em uma piscina:

| Variáveis definidas pelo serviço de leitura | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número alvo de nós de computação dedicados para a piscina. O número de nós dedicados é especificado como um alvo porque uma piscina pode nem sempre atingir o número desejado de nós. Por exemplo, se o número-alvo de nós dedicados for modificado por uma avaliação de escala automática antes de a piscina atingir o alvo inicial, então a piscina pode não atingir o alvo. <br /><br /> Um pool numa conta criada com a configuração do Serviço de Lote pode não atingir o seu alvo se o alvo exceder um nó de conta de Lote ou quota central. Um pool numa conta criada com a configuração de Subscrição do Utilizador pode não atingir o seu objetivo se o alvo exceder a quota de núcleo partilhada para a subscrição.|
| $TargetLowPriorityNodes |O número alvo de nós de computação de baixa prioridade para a piscina. O número de nós de baixa prioridade é especificado como um alvo, uma vez que uma piscina pode nem sempre atingir o número desejado de nós. Por exemplo, se o número-alvo de nós de baixa prioridade for modificado por uma avaliação de escala automática antes de a piscina atingir o objetivo inicial, então a piscina pode não atingir o alvo. Um pool também não pode atingir o seu objetivo se o alvo exceder um nó de conta lote ou quota central. <br /><br /> Para obter mais informações sobre nós de computação de baixa prioridade, consulte [Use VMs de baixa prioridade com Lote](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando os nódosos de computação são removidos de uma piscina. Os valores possíveis são:<ul><li>**refilar**- O valor padrão. Termina imediatamente as tarefas e coloca-as de volta na fila de trabalho para que sejam reagendadas. Esta ação garante que o número de nós alvo é alcançado o mais rapidamente possível, mas pode ser menos eficiente, uma vez que quaisquer tarefas de execução serão interrompidas e terão de ser reiniciadas, desperdiçando todo o trabalho que já tinham feito. <li>**terminar**-- Termina imediatamente as tarefas e retira-as da fila de trabalho.<li>realização de **tarefas**-- Aguarda que as tarefas em execução atualmente terminem e, em seguida, remova o nó da piscina. Utilize esta opção para evitar que as tarefas sejam interrompidas e ressonadas, desperdiçando qualquer trabalho que a tarefa tenha feito. <li>**dados retidos**-- Aguarda que todos os dados locais retidos na tarefa no nó sejam limpos antes de remover o nó da piscina.</ul> |

> [!NOTE]
> A variável `$TargetDedicatedNodes` também pode ser especificada utilizando o pseudónimo `$TargetDedicated`. Da mesma forma, a variável `$TargetLowPriorityNodes` pode ser especificada utilizando o pseudónimo `$TargetLowPriority`. Se tanto a variável totalmente nomeada como o seu pseudónimo forem definidos pela fórmula, o valor atribuído à variável totalmente nomeada terá precedência.
>
>

Você pode obter o valor destas variáveis definidas pelo serviço para fazer ajustes que são baseados em métricas do serviço Batch:

| Variáveis definidas apenas pelo serviço de leitura | Descrição |
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
| $SampleNodeCount |A contagem de nódosos de computação. |
| $ActiveTasks |O número de tarefas que estão prontas a executar, mas que ainda não estão a executar. A contagem de $ActiveTasks inclui todas as tarefas que estão no estado ativo e cujas dependências foram satisfeitas. Quaisquer tarefas que estejam no estado ativo, mas cujas dependências não foram satisfeitas, estão excluídas da contagem $ActiveTasks.|
| $RunningTasks |O número de tarefas em estado de execução. |
| $PendingTasks |A soma dos $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que terminaram com sucesso. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicatedNodes |O número atual de nós de computação dedicados. |
| $CurrentLowPriorityNodes |O número atual de nós de computação de baixa prioridade, incluindo quaisquer nós que tenham sido antecipados. |
| $PreemptedNodeCount | O número de nós na piscina que estão em estado de preoptação. |

> [!TIP]
> As variáveis apenas de leitura, definidas pelo serviço que são mostradas na tabela anterior são *objetos* que fornecem vários métodos de acesso aos dados associados a cada um. Para mais informações, consulte [Obter dados](#getsampledata) da amostra mais tarde neste artigo.
>
>

## <a name="types"></a>Tipos

Estes tipos são suportados numa fórmula:

* double
* doubleVec
* doubleVecList
* string
* timestamp -- timestamp é uma estrutura composta que contém os seguintes membros:

  * ano
  * mês (1-12)
  * dia (1-31)
  * dia da semana (no formato de número; por exemplo, 1 para segunda-feira)
  * hora (em formato de número 24 horas; por exemplo, 13 meios 1 3:00)
  * minuto (00-59)
  * segundo (00-59)
* intervalo de tempo

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

Estas operações são permitidas nos tipos listados na secção anterior.

| Operação | Operadores apoiados | Tipo de resultado |
| --- | --- | --- |
| duplo *operador* duplo |+, -, *, / |double |
| duplo intervalo de tempo *do operador* |* |intervalo de tempo |
| duplo *operador* Vec duplo |+, -, *, / |doubleVec |
| *operador* doubleVec doubleVec |+, -, *, / |doubleVec |
| operador *de* intervalo de tempo duplo |*, / |intervalo de tempo |
| intervalo de tempo *do operador de* intervalo de tempo |+, - |intervalo de tempo |
| carimbo de tempo *intervalado do operador* |+ |carimbo de data/hora |
| intervalo de tempo *do operador da* marca de tempo |+ |carimbo de data/hora |
| carimbo de tempo *do operador* |- |intervalo de tempo |
| *operador*duplo |-, ! |double |
| intervalo de tempo *do operador* |- |intervalo de tempo |
| duplo *operador* duplo |<, <=, ==, >=, >, != |double |
| string *operador* de cordas |<, <=, ==, >=, >, != |double |
| carimbo de tempo *do operador* |<, <=, ==, >=, >, != |double |
| intervalo de tempo *do operador de* intervalo de tempo |<, <=, ==, >=, >, != |double |
| duplo *operador* duplo |&&, &#124;&#124; |double |

Ao testar um duplo com um operador ternário (`double ? statement1 : statement2`), o não zero é **verdadeiro**, e zero é **falso**.

## <a name="functions"></a>Funções
Estas **funções** pré-definidas estão disponíveis para que possa utilizar na definição de uma fórmula de escala automática.

| Função | Tipo de devolução | Descrição |
| --- | --- | --- |
| avg(doubleVecList) |double |Devolve o valor médio de todos os valores do doubleVecList. |
| len(doubleVecList) |double |Devolve o comprimento do vetor que é criado a partir do doubleVecList. |
| lg(duplo) |double |Devolve a base de registo 2 do duplo. |
| lg(doubleVecList) |doubleVec |Devolve a base de registo em termos de componente 2 do doubleVecList. Um vec(duplo) deve ser explicitamente passado para o parâmetro. Caso contrário, assume-se a versão dupla lg(double). |
| Inn(duplo) |double |Devolve o registo natural do duplo. |
| ln(doubleVecList) |doubleVec |Devolve o registo natural do duplo. |
| log (duplo) |double |Devolve a base de registo 10 do duplo. |
| log(doubleVecList) |doubleVec |Devolve a base de registo em termos de componentes 10 do doubleVecList. Um vec(duplo) deve ser explicitamente passado para o parâmetro duplo único. Caso contrário, assume-se a versão de duplo registo (duplo). |
| max(doubleVecList) |double |Devolve o valor máximo no doubleVecList. |
| min(doubleVecList) |double |Devolve o valor mínimo no doubleVecList. |
| norm(doubleVecList) |double |Devolve a norma de duas normas do vetor que é criada a partir do doubleVecList. |
| percentil (doubleVec v, duplo p) |double |Devolve o elemento percentil do vetor v. |
| rand() |double |Devolve um valor aleatório entre 0,0 e 1.0. |
| range(doubleVecList) |double |Devolve a diferença entre o min e os valores máximos no doubleVecList. |
| std(doubleVecList) |double |Devolve o desvio padrão da amostra dos valores no doubleVecList. |
| stop() | |Interrompe a avaliação da expressão autoscalcificante. |
| sum(doubleVecList) |double |Devolve a soma de todos os componentes do doubleVecList. |
| time(string dateTime="") |carimbo de data/hora |Devolve o carimbo de tempo atual se não forem passados parâmetros ou o carimbo de tempo da corda dataTime se for passado. Data suportadaOs formatos de tempo são W3C-DTF e RFC 1123. |
| val (doubleVec v, duplo i) |double |Devolve o valor do elemento que está no local i no vetor v, com um índice inicial de zero. |

Algumas das funções descritas na tabela anterior podem aceitar uma lista como argumento. A lista separada da vírposta é qualquer combinação de *duplo* e *duplo Vec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor *doubleVecList* é convertido para um *único doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, então chamar `avg(v)` equivale a chamar `avg(1,2,3)`. Chamar `avg(v, 7)` equivale a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados da amostra

As fórmulas de escala automática atuam em dados métricos (amostras) fornecidos pelo serviço Batch. Uma fórmula cresce ou diminui o tamanho da piscina com base nos valores que obtém do serviço. As variáveis definidas pelo serviço que foram descritas anteriormente são objetos que fornecem vários métodos de acesso a dados que estão associados a esse objeto. Por exemplo, a seguinte expressão mostra um pedido para obter os últimos cinco minutos de utilização do CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() |O método `GetSample()` devolve um vetor de amostras de dados.<br/><br/>Uma amostra vale 30 segundos de dados métricos. Por outras palavras, as amostras são obtidas a cada 30 segundos. Mas, como se nota abaixo, há um atraso entre quando uma amostra é recolhida e quando está disponível para uma fórmula. Como tal, nem todas as amostras durante um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras a obter das amostras mais recentes que foram recolhidas.<br/><br/>`GetSample(1)` devolve a última amostra disponível. No entanto, para métricas como `$CPUPercent`, esta não deve ser utilizada porque é impossível saber *quando* a amostra foi recolhida. Pode ser recente, ou, devido a problemas de sistema, pode ser muito mais antigo. É melhor, nestes casos, utilizar um intervalo de tempo como mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um prazo para recolher dados da amostra. Opcionalmente, especifica também a percentagem de amostras que devem estar disponíveis no prazo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` devolveria 20 amostras se todas as amostras dos últimos 10 minutos estiverem presentes na história do CPUPercent. No entanto, se o último minuto da história não estivesse disponível, apenas 18 amostras seriam devolvidas. Neste caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` falharia porque apenas 90% das amostras estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` teria sucesso.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um prazo para a recolha de dados, com um tempo de início e um tempo de fim.<br/><br/>Como acima referido, há um atraso entre quando uma amostra é recolhida e quando está disponível para uma fórmula. Considere este atraso quando utilizar o método `GetSample`. Veja `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Devolve o período de amostras colhidas num conjunto histórico de dados de amostras. |
| Contagem() |Devolve o número total de amostras no histórico métrico. |
| HistoryBeginTime() |Devolve o carimbo de tempo da amostra de dados disponíveis mais antiga para a métrica. |
| GetSamplePercent() |Devolve a percentagem de amostras disponíveis para um determinado intervalo de tempo. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Uma vez que o método `GetSample` falha se a percentagem de amostras devolvidas for inferior à `samplePercent` especificada, pode utilizar o método `GetSamplePercent` para verificar primeiro. Em seguida, pode realizar uma ação alternativa se houver amostras insuficientes, sem parar a avaliação automática de escala. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Amostras, percentagem de amostras e método *GetSample()*
O funcionamento principal de uma fórmula de escala automática é obter dados métricos de tarefas e recursos e, em seguida, ajustar o tamanho da piscina com base nesses dados. Como tal, é importante ter uma compreensão clara de como as fórmulas de escala automática interagem com os dados das métricas (amostras).

**Amostras**

O serviço De lote retira periodicamente amostras de métricas de tarefas e recursos e disponibiliza-as para as suas fórmulas de escala automática. Estas amostras são gravadas a cada 30 segundos pelo serviço Batch. No entanto, há tipicamente um atraso entre quando essas amostras foram gravadas e quando são disponibilizadas para (e podem ser lidas) as suas fórmulas de escala automática. Além disso, devido a diversos fatores, tais como problemas de rede ou outras infraestruturas, as amostras não podem ser registadas num determinado intervalo.

**Percentagem de amostra**

Quando `samplePercent` é passada para o método `GetSample()` ou o método `GetSamplePercent()` é chamado, _por cento_ refere-se a uma comparação entre o número total possível de amostras que são registadas pelo serviço De lote e o número de amostras disponíveis para a sua fórmula de escala automática.

Vamos olhar para uma panela de 10 minutos como um exemplo. Uma vez que as amostras são registadas a cada 30 segundos num período de 10 minutos, o número total máximo de amostras que são registadas pelo Batch seria de 20 amostras (2 por minuto). No entanto, devido à latência inerente do mecanismo de reporte e de outras questões dentro do Azure, pode haver apenas 15 amostras disponíveis para a sua fórmula de escala automática para leitura. Assim, por exemplo, durante esse período de 10 minutos, apenas 75% do número total de amostras registadas pode estar disponível para a sua fórmula.

**GetSample() e gamas de amostras**

As suas fórmulas de escala automática vão crescer e encolher as suas piscinas &mdash; adicionar nódosos ou remover os nódosos. Como os nós custam dinheiro, você quer garantir que as suas fórmulas usam um método inteligente de análise que é baseado em dados suficientes. Por isso, recomendamos que utilize uma análise do tipo de tendência nas suas fórmulas. Este tipo cresce e encolhe as suas piscinas com base numa série de amostras recolhidas.

Para tal, use `GetSample(interval look-back start, interval look-back end)` para devolver um vetor de amostras:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada pelo Batch, devolve uma gama de amostras como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois de recolher o vetor de amostras, pode então usar funções como `min()`, `max()`, e `avg()` obter valores significativos da gama recolhida.

Para uma segurança adicional, pode forçar uma avaliação de fórmula a falhar se for inferior a uma determinada percentagem de amostra disponível durante um determinado período de tempo. Quando forçar uma avaliação de fórmula a falhar, instrui o Batch a cessar a avaliação da fórmula se a percentagem especificada de amostras não estiver disponível. Neste caso, não é feita qualquer alteração ao tamanho da piscina. Para especificar uma percentagem necessária de amostras para o sucesso da avaliação, especifique-a como o terceiro parâmetro a `GetSample()`. Aqui, é especificada uma exigência de 75% das amostras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Como pode haver um atraso na disponibilidade da amostra, é importante especificar sempre um intervalo de tempo com um tempo de arranque que seja mais longo do que um minuto. Demora aproximadamente um minuto para as amostras se propagarem através do sistema, para que as amostras da faixa `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` possam não estar disponíveis. Mais uma vez, pode utilizar o parâmetro percentual de `GetSample()` para forçar uma determinada exigência de percentagem de amostra.

> [!IMPORTANT]
> **Recomendamos vivamente** que **evite confiar *apenas* em `GetSample(1)` nas suas fórmulas**de escala automática. Isto porque `GetSample(1)` essencialmente diz ao serviço batch: "Dê-me a última amostra que tiver, não importa há quanto tempo a recuperou." Uma vez que se trata apenas de uma amostra, e pode ser uma amostra mais antiga, pode não ser representativa da imagem maior da tarefa recente ou do estado de recurso. Se utilizar `GetSample(1)`, certifique-se de que faz parte de uma declaração maior e não do único ponto de dados em que a sua fórmula depende.
>
>

## <a name="metrics"></a>Métricas

Pode utilizar métricas de recursos e tarefas quando estiver a definir uma fórmula. Ajusta o número de nós dedicados na piscina com base nos dados métricos que obtém e avalia. Consulte a secção [Variáveis](#variables) acima para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>As métricas de recursos baseiam-se no CPU, na largura de banda, no uso da memória dos nós de computação e no número de nós.</p>
        <p> Estas variáveis definidas pelo serviço são úteis para fazer ajustes com base na contagem de nós:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo serviço são úteis para fazer ajustes baseados na utilização do recurso do nó:</p>
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
    <td><p>As métricas de tarefa baseiam-se no estado das tarefas, tais como Ativo, Pendente e Concluído. As seguintes variáveis definidas pelo serviço são úteis para fazer ajustes no tamanho da piscina com base em métricas de tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escreva uma fórmula de escala automática

Constrói-se uma fórmula de escala automática formando declarações que utilizam os componentes acima referidos e, em seguida, combina-se essas declarações numa fórmula completa. Nesta secção, criamos uma fórmula de escala automática que pode executar algumas decisões de escala no mundo real.

Primeiro, vamos definir os requisitos para a nossa nova fórmula de escala automática. A fórmula deve:

1. Aumente o número de nós de computação dedicados numa piscina se o uso de CPU for elevado.
1. Diminua o número de nós de computação dedicados numa piscina quando o uso do CPU é baixo.
1. Restrinja sempre o número máximo de nós dedicados a 400.
1. Ao reduzir o número de nós, não remova os nós que estão a executar tarefas; se necessário, aguarde até que as tarefas tenham terminado para remover os nós.

Para aumentar o número de nós durante o elevado uso do CPU, defina a declaração que povoa uma variável definida pelo utilizador (`$totalDedicatedNodes`) com um valor que é de 110 por cento do número de nós dedicados, mas apenas se a média mínima de utilização do CPU durante os últimos 10 minutos for superior a 70 por cento. Caso contrário, utilize o valor para o número atual de nós dedicados.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para *diminuir* o número de nós dedicados durante a baixa utilização do CPU, a próxima declaração na nossa fórmula define a mesma variável `$totalDedicatedNodes` para 90% do número atual de nós dedicados se o uso médio de CPU nos últimos 60 minutos fosse inferior a 20 por cento. Caso contrário, utilize o valor atual de `$totalDedicatedNodes` que povoámos na declaração acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora limite o número de nomes de cálculo dedicados a um máximo de 400:

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

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Criar uma piscina ativada por escala automática com SDKs de Lote

A autodimensionamento da piscina pode ser configurada utilizando qualquer um dos [SDKs](batch-apis-tools.md#azure-accounts-for-batch-development)de Lote, os [cmdlets](batch-powershell-cmdlets-get-started.md)de PowerShell do [lote REST API](https://docs.microsoft.com/rest/api/batchservice/) e o [Lote CLI](batch-cli-get-started.md). Nesta secção, pode ver exemplos tanto para .NET como Python.

### <a name="net"></a>.NET

Para criar uma piscina com autoscalcificação ativada em .NET, siga estes passos:

1. Crie a piscina com [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Defina a propriedade [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) para `true`.
1. Defina a propriedade [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) com a sua fórmula de escala automática.
1. (Opcional) Defina a propriedade [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (predefinição é de 15 minutos).
1. Comprometa a piscina com [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O seguinte código snippet cria uma piscina ativada por escala automática em .NET. A fórmula de escala automática da piscina define o número de nós dedicados a 5 às segundas-feiras, e 1 em todos os dias da semana. O intervalo de [escala automática](#automatic-scaling-interval) está definido para 30 minutos. Neste e nos C# outros cortes neste artigo, `myBatchClient` é uma instância devidamente inicializada da classe [BatchClient.][net_batchclient]

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
> Quando criar uma piscina ativada por escala automática, não especifique o parâmetro _targetDedicatedNodes_ ou o parâmetro _targetLowPriorityNodes_ na chamada para **CreatePool**. Em vez disso, especifique as propriedades **AutoScaleEnabled** e **AutoScaleFormula** na piscina. Os valores destas propriedades determinam o número-alvo de cada tipo de nó. Além disso, para redimensionar manualmente uma piscina ativada por escala automática (por exemplo, com [BatchClient.PoolOperations.ResizePoolAsync),][net_poolops_resizepoolasync]primeiro **desative** a escala automática na piscina e, em seguida, redimensione-a.
>
>

#### <a name="automatic-scaling-interval"></a>Intervalo automático de escalação

Por padrão, o serviço Batch ajusta o tamanho de uma piscina de acordo com a sua fórmula de escala automática a cada 15 minutos. Este intervalo é configurável utilizando as seguintes propriedades da piscina:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Lote .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

O intervalo mínimo é de cinco minutos, e o máximo é de 168 horas. Se for especificado um intervalo for for além desta gama, o serviço 'Batch' devolve um erro de Pedido Mau (400).

> [!NOTE]
> A autoscalcificação não se destina a responder a alterações em menos de um minuto, mas destina-se a ajustar gradualmente o tamanho da sua piscina à medida que executa uma carga de trabalho.
>
>

### <a name="python"></a>Python

Da mesma forma, você pode fazer uma piscina ativada por escala automática com o Python SDK por:

1. Crie uma piscina e especifique a sua configuração.
1. Adicione a piscina ao cliente de serviço.
1. Ative a escala automática na piscina com uma fórmula que escreve.

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
> Mais exemplos de utilização do SDK Python podem ser encontrados no [repositório Batch Python Quickstart](https://github.com/Azure-Samples/batch-python-quickstart) no GitHub.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Ativar a autodimensionamento numa piscina existente

Cada Lote SDK fornece uma forma de permitir a autodimensionamento. Por exemplo:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Lote .NET)
* [Ativar a escala automática numa piscina][rest_enableautoscale] (REST API)

Quando ativa a autodimensionamento numa piscina existente, tenha em mente os seguintes pontos:

* Se o escalonamento automático estiver atualmente desativado na piscina quando emitir o pedido para ativar a autoescalação, deve especificar uma fórmula de escala automática válida quando emitir o pedido. Pode especificar opcionalmente um intervalo de avaliação à escala automática. Se não especificar um intervalo, o valor padrão de 15 minutos é utilizado.
* Se a escala automática estiver ativada na piscina, pode especificar uma fórmula de escala automática, um intervalo de avaliação ou ambos. Deve especificar pelo menos uma destas propriedades.

  * Se especificar um novo intervalo de avaliação à escala automática, então o calendário de avaliação existente é interrompido e um novo horário é iniciado. A hora de início do novo horário é o momento em que foi emitido o pedido para permitir a autoscalcificação.
  * Se omitir a fórmula de escala automática ou o intervalo de avaliação, o serviço Batch continua a utilizar o valor atual dessa definição.

> [!NOTE]
> Se especificou valores para os *parâmetros targetDedicatedNodes* ou *targetLowPriorityNodes* do método **CreatePool** quando criou o pool em .NET, ou para os parâmetros comparáveis noutra língua, então esses valores são ignorados quando a fórmula de escala automática é avaliada.
>
>

Este C# fragmento de código utiliza a biblioteca [Batch .NET][net_api] para permitir a autodimensionamento numa piscina existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de escala automática

Para atualizar a fórmula numa piscina ativada por escala automática existente, ligue para a operação para permitir a autodimensionamento novamente com a nova fórmula. Por exemplo, se a autodimensionamento já estiver ativada em `myexistingpool` quando for executado o seguinte código .NET, a sua fórmula de escala automática é substituída pelo conteúdo de `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de escala automática

Para atualizar o intervalo de avaliação de escala automática de uma piscina ativada por escala automática existente, ligue para a operação para permitir a autodimensionamento novamente com o novo intervalo. Por exemplo, para definir o intervalo de avaliação de escala automática para 60 minutos para uma piscina que já está ativada automaticamente em .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de escala automática

Você pode avaliar uma fórmula antes de aplicá-la a uma piscina. Desta forma, pode testar a fórmula para ver como as suas declarações avaliam antes de colocar a fórmula em produção.

Para avaliar uma fórmula de escala automática, primeiro deve ativar a autoscalcificação na piscina com uma fórmula válida. Para testar uma fórmula numa piscina que ainda não tenha autoscalcificação ativada, utilize a fórmula de uma linha `$TargetDedicatedNodes = 0` quando ativar automaticamente. Em seguida, use um dos seguintes para avaliar a fórmula que pretende testar:

* [BatchClient.PoolOperations.AssessAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Estes métodos de lote .NET requerem a identificação de uma piscina existente e uma cadeia contendo a fórmula de escala automática para avaliar.

* [Avaliar uma fórmula de escala automática](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Neste pedido REST API, especifique o ID do pool no URI e a fórmula de escala automática no elemento *autoScaleFormula* do organismo de pedido. A resposta da operação contém qualquer informação de erro que possa estar relacionada com a fórmula.

Neste [código Batch .NET,][net_api] avaliamos uma fórmula de escala automática. Se a piscina não tiver autoscalcificação ativada, nós a ativamos primeiro.

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

A avaliação bem sucedida da fórmula mostrada neste código produz resultados semelhantes a:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtenha informações sobre corridas de escala automática

Para garantir que a sua fórmula está a funcionar como esperado, recomendamos que verifique periodicamente os resultados das correções de autoscalcificação que o Batch executa na sua piscina. Para tal, obtenha (ou refresque) uma referência à piscina, e examine as propriedades da sua última corrida à escala automática.

Em Batch .NET, a propriedade [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tem várias propriedades que fornecem informações sobre a mais recente corrida automática de escala realizada na piscina:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoscaleRun.Resultados](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoscaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na Rest API, o [Obter informações sobre um](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) pedido de piscina devolve informações sobre a piscina, que inclui as mais recentes informações de execução automática de escala na propriedade [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool)

O C# seguinte código de snippet utiliza a biblioteca Batch .NET para imprimir informações sobre a última execução autoscalcificada na piscina _myPool:_

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Saída da amostra do corte anterior:

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

## <a name="example-autoscale-formulas"></a>Fórmulas de escala automática exemplo

Vejamos algumas fórmulas que mostram diferentes formas de ajustar a quantidade de recursos computacionais numa piscina.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: Ajuste baseado no tempo

Suponha que queira ajustar o tamanho da piscina com base no dia da semana e hora do dia. Este exemplo mostra como aumentar ou diminuir o número de nós na piscina em conformidade.

A fórmula obtém primeiro o tempo atual. Se for um dia de semana (1-5) e dentro do horário de trabalho (8:00 às 18:00), o tamanho da piscina alvo está definido para 20 nós. Caso contrário, está marcado para 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: Ajustamento baseado em tarefas

Neste exemplo, o tamanho da piscina é ajustado com base no número de tarefas na fila. Tanto os comentários como as quebras de linha são aceitáveis nas cordas de fórmula.

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

Este exemplo ajusta o tamanho da piscina com base no número de tarefas. Esta fórmula também tem em conta o valor [MaxTasksPerComputeNode][net_maxtasks] que foi definido para a piscina. Esta abordagem é útil em situações em que a execução paralela de [tarefas](batch-parallel-node-tasks.md) foi ativada na sua piscina.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: Definir um tamanho inicial da piscina

Este exemplo C# mostra um corte de código com uma fórmula de escala automática que define o tamanho da piscina para um número especificado de nós durante um período de tempo inicial. Em seguida, ajusta o tamanho da piscina com base no número de tarefas de execução e ativas após o período de tempo inicial ter decorrido.

A fórmula no seguinte código:

* Define o tamanho inicial da piscina para quatro nódosos.
* Não ajusta o tamanho da piscina nos primeiros 10 minutos do ciclo de vida da piscina.
* Após 10 minutos, obtém o valor máximo do número de tarefas de execução e ativas nos últimos 60 minutos.
  * Se ambos os valores forem 0 (indicando que nenhuma tarefa estava a funcionar ou ativa nos últimos 60 minutos), o tamanho da piscina está definido para 0.
  * Se qualquer um dos valores for superior a zero, não se faz qualquer alteração.

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

* O uso de [recursos computacionais do Maximize Azure Batch com tarefas de nó simultânea](batch-parallel-node-tasks.md) contém detalhes sobre como pode executar várias tarefas simultaneamente nos nós de computação na sua piscina. Além da autoscalcificação, esta funcionalidade pode ajudar a reduzir a duração do trabalho para algumas cargas de trabalho, poupando-lhe dinheiro.
* Para outro reforço de eficiência, certifique-se de que a aplicação Batch consulta o serviço Batch da forma mais ideal. Consulte [o serviço Azure Batch de forma eficiente](batch-efficient-list-queries.md) para aprender a limitar a quantidade de dados que cruzam o fio quando se consulta o estado de milhares de potenciais nódos os nossos comíveis ou tarefas.

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
