---
title: Livros gráficos de autor na Automação Azure
description: Este artigo diz como autoria um livro gráfico sem trabalhar com código.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 35d3146c0ca5571e6e16793c97378de1e2db362e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711725"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Livros gráficos de autor na Automação Azure

Todos os livros de execução da Azure Automation são fluxos de trabalho do Windows PowerShell. Os livros gráficos e os livros de execução gráficopower Workflow geram código PowerShell que os trabalhadores da Automação executam, mas que não pode ver ou modificar. Pode converter um livro de execução gráfico num livro de execução gráfico powerShell Workflow e vice-versa. No entanto, não é possível converter estes livros de execução num livro textual. Além disso, o editor gráfico da Automação não pode importar um livro textual.

A autoria gráfica permite-lhe criar livros de execução para a Automação Azure sem as complexidades do código de fluxo de trabalho do Windows PowerShell ou powerShell. Você pode adicionar atividades à tela a partir de uma biblioteca de cmdlets e livros de execução, ligá-los e configurá-los para formar um fluxo de trabalho. Se já trabalhou com o System Center Orchestrator ou com a Automatização de Gestão de Serviços (SMA), a autoria gráfica deve parecer familiar. Este artigo fornece uma introdução aos conceitos que precisa para começar a criar um livro gráfico.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Pode abrir o editor gráfico no portal Azure criando ou editando um livro de execução gráfico.

![Espaço de trabalho gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As seguintes secções descrevem os controlos no editor gráfico.

### <a name="canvas-control"></a>Controlo de lona

O controlo de Lona permite-lhe desenhar o seu livro de corridas. Pode adicionar atividades desde os nódosos do controlo da Biblioteca ao livro de execução e conectá-las com links para definir a lógica do livro de corridas. Na parte inferior da tela, existem controlos que lhe permitem fazer zoom para dentro e para fora.

### <a name="library-control"></a>Controlo da biblioteca

O controlo da Biblioteca permite-lhe selecionar [atividades](#use-activities) para adicionar ao seu livro de execução. Adicione-os à tela, onde pode ligá-los a outras atividades. O controlo da Biblioteca inclui as secções definidas na tabela seguinte.

| Section | Descrição |
|:--- |:--- |
| Cmdlets |Todos os cmdlets que podem ser usados no seu livro de execução. Os cmdlets são organizados por módulo. Todos os módulos instalados na sua conta Automation estão disponíveis. |
| Runbooks |Os livros na sua conta de Automação. Pode adicionar estes livros de execução à tela para serem usados como livros infantis. Apenas são mostrados livros do mesmo tipo de núcleo que o livro de execução que está a ser editado. Para livros gráficos, apenas são mostrados livros de execução baseados na PowerShell. Para os livros de execução de fluxo de trabalho de PowerShell, apenas são apresentados livros de execução baseados em PowerShell Workflow. |
| Elementos |Os ativos de [automação](/previous-versions/azure/dn939988(v=azure.100)) na sua conta Automation que pode utilizar no seu livro de execução. Adicionar um ativo a um livro de execução adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos variáveis, pode selecionar se deve adicionar uma atividade para obter a variável ou definir a variável. |
| Controlo do Runbook |Controle as atividades que podem ser usadas no seu atual livro de execução. Uma atividade de Junção requer várias inputs e espera até que todos tenham terminado antes de continuar o fluxo de trabalho. Uma atividade de Código executa uma ou mais linhas de PowerShell ou PowerShell Workflow code, dependendo do tipo de livro gráfico. Pode utilizar esta atividade para código personalizado ou para funcionalidades difíceis de alcançar com outras atividades. |

### <a name="configuration-control"></a>Controlo de configuração

O controlo de configuração permite-lhe fornecer detalhes para um objeto que é selecionado na tela. As propriedades disponíveis neste controlo dependem do tipo de objeto selecionado. Quando escolhe uma opção no controlo de Configuração, abre lâminas adicionais para fornecer mais informações.

### <a name="test-control"></a>Controlo de ensaios

O controlo de Teste não é apresentado quando o editor gráfico é iniciado pela primeira vez. Abre-se quando se testa interativamente um livro de execução gráfico.

## <a name="use-activities"></a>Utilizar atividades

As atividades são os blocos de construção de um livro de corridas. Uma atividade pode ser um cmdlet PowerShell, um livro de crianças ou um fluxo de trabalho. Pode adicionar uma atividade ao livro de execução clicando-o à direita no controlo da Biblioteca e selecionando **Adicionar à tela**. Em seguida, pode clicar e arrastar a atividade para colocá-la em qualquer lugar da tela que você gosta. A localização da atividade na tela não afeta o funcionamento do livro de execução. Pode definir o seu livro de corridas da forma que achar mais adequado para visualizar o seu funcionamento.

![Adicione à tela](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecione uma atividade na tela para configurar as suas propriedades e parâmetros na lâmina de configuração. Pode alterar o rótulo da atividade para um nome que ache descritivo. O livro de corridas ainda executa o cmdlet original. Está simplesmente a mudar o nome de exibição que o editor gráfico usa. Note que a etiqueta deve ser única dentro do livro de execução.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para um determinado cmdlet. Todos os cmdlets têm pelo menos um conjunto de parâmetros, e alguns têm vários conjuntos. Se um cmdlet tiver vários conjuntos de parâmetros, deve selecionar o que utilizar antes de configurar os parâmetros. Pode alterar o conjunto de parâmetros utilizado por uma atividade selecionando o Conjunto de **Parâmetros** e escolhendo outro conjunto. Neste caso, perdem-se quaisquer valores de parâmetros que já configuraste.

No exemplo seguinte, o cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) tem três conjuntos de parâmetros. O exemplo utiliza um conjunto chamado **ListVirtualMachineInResourceGroupParamSet,** com um único parâmetro opcional, para devolver todas as máquinas virtuais num grupo de recursos. O exemplo também utiliza o parâmetro de parâmetro **GetVirtualMachineInResourceGroupParamSet** para especificar a máquina virtual para devolver. Este conjunto tem dois parâmetros obrigatórios e um parâmetro opcional.

![Conjunto de parâmetros](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetros

Quando especifica um valor para um parâmetro, seleciona uma fonte de dados para determinar como o valor é especificado. As fontes de dados disponíveis para um parâmetro específico dependem dos valores válidos para esse parâmetro. Por exemplo, a Null não é uma opção disponível para um parâmetro que não permite valores nulos.

| Origem de Dados | Descrição |
|:--- |:--- |
| Valor Constante |Digite um valor para o parâmetro. Esta fonte de dados só está disponível para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime, Switch. |
| Saída de Atividade |Utilize a saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas estão listadas. Para o valor do parâmetro, utilize apenas a atividade que produz a saída. Se a atividade for saída de um objeto com múltiplas propriedades, pode escrever em nome de uma propriedade específica depois de selecionar a atividade. |
| Entrada do livro de execução |Selecione uma entrada de livro de execução como entrada para o parâmetro de atividade. |
| Ativo Variável |Selecione uma variável de Automação como entrada. |
| Ativo credencial |Selecione uma credencial de Automação como entrada. |
| Ativo de Certificado |Selecione um certificado de Automação como entrada. |
| Ativo de Ligação |Selecione uma ligação Automação como entrada. |
| Expressão do PowerShell |Especifique uma expressão simples [powerShell](#work-with-powershell-expressions). A expressão é avaliada antes da atividade e o resultado é usado para o valor do parâmetro. Pode utilizar variáveis para se referir à saída de uma atividade ou a um parâmetro de entrada de caderneta. |
| Não Configurado |Limpe qualquer valor que tenha sido previamente configurado. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Estes são parâmetros comuns powerShell ou outros parâmetros personalizados. O editor gráfico apresenta uma caixa de texto onde pode fornecer parâmetros usando a sintaxe PowerShell. Por exemplo, para utilizar o `Verbose` parâmetro comum, deve especificar `-Verbose:$True` .

### <a name="retry-activity"></a>Atividade de retry

A funcionalidade de retry para uma atividade permite que seja executada várias vezes até que uma determinada condição seja satisfeita, tal como um loop. Pode utilizar esta funcionalidade para atividades que devem ser executadas várias vezes, são propensas a erros, podem necessitar de mais do que uma tentativa de sucesso, ou testar a informação de saída da atividade para dados válidos.

Quando ativa ristente para uma atividade, pode definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o livro de corridas espera antes de voltar a executar a atividade. Se não especificar um atraso, a atividade volta a ser imediatamente terminada.

![Atraso de retry da atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de retry é uma expressão PowerShell que é avaliada após cada vez que a atividade funciona. Se a expressão resolver com true, a atividade corre novamente. Se a expressão se resolver com Falso, a atividade não volta a funcionar e o livro de reparação passa para a próxima atividade.

![Atraso de retry da atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de retry pode usar uma variável chamada `RetryData` que fornece acesso à informação sobre as repetições da atividade. Esta variável tem as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| `NumberOfAttempts` |Número de vezes que a atividade foi executada. |
| `Output` |Saída da última execução da atividade. |
| `TotalDuration` |O tempo decorrido desde que a atividade começou da primeira vez. |
| `StartedAt` |Tempo (em formato UTC) quando a atividade foi iniciada pela primeira vez. |

Seguem-se exemplos de condições de retry de atividade.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Depois de configurar uma condição de retry para uma atividade, a atividade inclui duas pistas visuais para lembrá-lo. Um é apresentado na atividade e o outro é mostrado quando você revê a configuração da atividade.

![Retry Indicadores Visuais](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controlo do script de fluxo de trabalho

Um controlo de script de fluxo de trabalho é uma atividade especial que aceita o powerShell ou o script powerShell Workflow, dependendo do tipo de livro gráfico que está sendo da autoria. Este controlo fornece uma funcionalidade que pode não estar disponível por outros meios. Não pode aceitar parâmetros, mas pode usar variáveis para a saída de atividade e parâmetros de entrada de caderneta. Qualquer saída da atividade é adicionada ao databus. Uma exceção é a saída sem ligação de saída, caso em que a saída é adicionada à saída do livro de execução.

Por exemplo, o código seguinte executa cálculos de data utilizando uma variável de entrada de livro de execução denominada `NumberOfDays` . Em seguida, envia um valor datatime calculado como saída a ser utilizado por atividades subsequentes no livro de execução.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Utilizar links para fluxo de trabalho

Um link num livro gráfico liga duas atividades. É exibido na tela como uma seta que aponta da atividade de origem para a atividade de destino. As atividades decorrem na direção da seta com a atividade de destino a partir do final da atividade de origem.

### <a name="create-a-link"></a>Criar um link

Pode criar uma ligação entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e liberte.'

![Criar um link](media/automation-graphical-authoring-intro/create-link-options.png)

Selecione o link para configurar as suas propriedades na lâmina de configuração. As propriedades incluem o tipo de ligação, que é descrito na tabela seguinte.

| Tipo de ligação | Descrição |
|:--- |:--- |
| Pipeline |A atividade de destino funciona uma vez para cada saída de objeto da atividade de origem. A atividade de destino não funciona se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como objeto. |
| Sequence |A atividade de destino funciona apenas uma vez quando recebe a produção da atividade de origem. A saída da atividade de origem está disponível como uma variedade de objetos. |

### <a name="start-runbook-activity"></a>Iniciar atividade de livro de corridas

Um livro de execução gráfico começa com quaisquer atividades que não tenham uma ligação de entrada. Muitas vezes só existe uma atividade que funciona como a atividade inicial para o livro de corridas. Se várias atividades não tiverem uma ligação de entrada, o livro de corridas começa por executá-las em paralelo. Segue os links para executar outras atividades à medida que cada uma completa.

### <a name="specify-link-conditions"></a>Especificar as condições de ligação

Quando especifica uma condição num link, a atividade de destino só funciona se a condição resolver com True. Normalmente, utiliza-se uma `ActivityOutput` variável em condições para recuperar a saída da atividade de origem.

Para uma ligação de gasoduto, deve especificar uma condição para um único objeto. O livro de execução avalia a condição para cada saída de objeto pela atividade de origem. Em seguida, executa a atividade de destino para cada objeto que satisfaz a condição. Por exemplo, com uma atividade de origem `Get-AzVM` de, pode utilizar a seguinte sintaxe para uma ligação de gasoduto condicional para recuperar apenas máquinas virtuais no grupo de recursos chamado Grupo 1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para uma ligação de sequência, o livro de execução apenas avalia a condição uma vez, uma vez que uma única matriz contendo todos os objetos da atividade de origem é devolvida. Por causa disso, o livro de execução não pode usar uma ligação de sequência para filtrar, como pode com uma ligação de pipeline. A ligação de sequência pode simplesmente determinar se a próxima atividade é executada ou não.

Por exemplo, tome o seguinte conjunto de atividades no nosso livro de **execução Start VM:**

![Ligação Condicional com Sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

O livro de execução utiliza três ligações de sequência diferentes que verificam os valores dos parâmetros de entrada `VMName` e para determinar as `ResourceGroupName` medidas adequadas a tomar. As possíveis ações são iniciar um único VM, iniciar todos os VMs no grupo de recursos, ou iniciar todos os VMs numa subscrição. Para a ligação de sequência entre `Connect to Azure` `Get single VM` e, aqui está a lógica da condição:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Quando utiliza um link condicional, os dados disponíveis da atividade de origem para outras atividades nesse ramo são filtrados pela circunstância. Se uma atividade for a fonte de múltiplas ligações, os dados disponíveis para atividades em cada ramo dependem da condição na ligação que liga a esse ramo.

Por exemplo, a `Start-AzVM` atividade no livro de corridas abaixo inicia todas as máquinas virtuais. Tem dois links condicional. A primeira ligação condicional utiliza a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` para filtrar se a `Start-AzVM` atividade terminar com sucesso. A segunda ligação condicional utiliza a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` para filtrar se a `Start-AzVm` atividade não conseguir ligar a máquina virtual.

![Exemplo de ligação condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que siga o primeiro link e utilize a saída de atividade `Get-AzureVM` apenas recupera as máquinas virtuais que foram iniciadas no momento em que `Get-AzureVM` foi executada. Qualquer atividade que siga o segundo link só recebe as máquinas virtuais que foram paradas no momento em que `Get-AzureVM` foi executada. Qualquer atividade que seguem o terceiro link obtém todas as máquinas virtuais, independentemente do seu estado de funcionamento.

### <a name="use-junctions"></a>Use junções

Uma junção é uma atividade especial que espera até que todos os ramos que chegam tenham terminado. Isto permite que o livro de corridas execute várias atividades paralelamente e certifique-se de que todas tenham terminado antes de seguir em frente.

Enquanto uma junção pode ter um número ilimitado de ligações de entrada, apenas uma dessas ligações pode ser um oleoduto. O número de ligações de sequência de entrada não está limitado. Pode criar a junção com várias ligações de gasoduto de entrada e guardar o livro de corridas, mas falhará quando estiver em funcionação.

O exemplo abaixo faz parte de um livro de execução que inicia um conjunto de máquinas virtuais, ao mesmo tempo que descarrega patches para serem aplicados nessas máquinas. Utiliza uma junção para garantir que ambos os processos sejam concluídos antes que o livro de execução continue.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Trabalhar com ciclos

Forma-se um ciclo quando uma atividade de destino se liga à sua atividade de origem ou a outra atividade que eventualmente liga à sua origem. A autoria gráfica não suporta atualmente ciclos. Se o seu livro de execução tiver um ciclo, economiza corretamente, mas recebe um erro quando funciona.

![Cíclico](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Partilhar dados entre atividades

Quaisquer dados que uma atividade produza com um link de saída são escritos para o databus para o livro de execução. Qualquer atividade no livro de execução pode usar dados no databus para preencher valores de parâmetros ou incluir no código script. Uma atividade pode aceder à saída de qualquer atividade anterior no fluxo de trabalho.

A forma como os dados são escritos ao databus depende do tipo de ligação à atividade. Para uma ligação de pipeline, os dados são de saída como múltiplos objetos. Para uma ligação de sequência, os dados são de saída como uma matriz. Se houver apenas um valor, é a saída como uma matriz de um elemento único.

O seu livro tem duas formas de aceder aos dados no databus: 
* Utilize uma fonte de dados de saída de atividade.
* Utilize uma fonte de dados de expressão PowerShell.

O primeiro mecanismo utiliza uma fonte de dados de saída de atividade para povoar um parâmetro de outra atividade. Se a saída for um objeto, o livro de execução pode especificar uma única propriedade.

![produção de atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

O segundo mecanismo de acesso a dados recupera a saída de uma atividade numa fonte de dados de expressão PowerShell ou numa atividade de script de fluxo de trabalho com uma `ActivityOutput` variável, utilizando a sintaxe mostrada abaixo. Se a saída for um objeto, o seu livro de execução pode especificar uma única propriedade.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Utilizar postos de controlo

Pode definir [pontos](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) de verificação num livro de execução de fluxo de trabalho de PowerShell gráfico, selecionando o livro de **execução do Checkpoint** em qualquer atividade. Isto faz com que um posto de controlo seja definido após o funcionar da atividade.

![Check Point](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os postos de controlo só estão ativados em livros de execução gráficos powerShell Workflow, e não estão disponíveis em livros gráficos. Se o livro de execução utilizar cmdlets Azure, deve seguir qualquer atividade checkpointada com uma `Connect-AzAccount` atividade. A operação de ligação é utilizada no caso de o livro de execução ser suspenso e deve reiniciar a partir deste posto de controlo num trabalhador diferente.

## <a name="handle-runbook-input"></a>Entrada do livro de correção do cabo

Um livro de execução requer a entrada de um utilizador que inicia o livro através do portal Azure ou de outro livro de execução, se o atual for utilizado em criança. Por exemplo, para um livro de execução que cria uma máquina virtual, o utilizador pode precisar de fornecer informações como o nome da máquina virtual e outras propriedades cada vez que o livro de execução começa.

O livro de execução aceita a entrada definindo um ou mais parâmetros de entrada. O utilizador fornece valores para estes parâmetros sempre que o livro de execução começa. Quando o utilizador inicia o livro de execução utilizando o portal Azure, o utilizador é solicitado a fornecer valores para cada parâmetro de entrada suportado pelo livro de execução.

Ao autorizar o seu livro de execução, pode aceder aos seus parâmetros de entrada clicando em **Entrada e saída** na barra de ferramentas do livro de execução. Isto abre o controlo de entrada e saída onde pode editar um parâmetro de entrada existente ou criar um novo clicando **em Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades do quadro seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Name | Necessário. O nome do parâmetro. O nome deve ser único dentro do livro de execução. Deve começar com uma letra e pode conter apenas letras, números e sublinhados. O nome não pode conter um espaço. |
| Descrição |Opcional. Descrição do propósito para o parâmetro de entrada. |
| Tipo | Opcional. Tipo de dados esperado para o valor do parâmetro. O portal Azure fornece um controlo adequado para o tipo de dados para cada parâmetro quando solicita a entrada. Os tipos de parâmetros suportados são String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se um tipo de dados não for selecionado, não se leciona a String.|
| Obrigatório | Opcional. A definição especifica se deve ser previsto um valor para o parâmetro. Se `yes` escolher, deve ser fornecido um valor quando o livro de execução for iniciado. Se escolher , não é necessário um valor quando o livro de `no` execução é iniciado, e um valor predefinido pode ser usado. O livro de execução não pode arrancar se não fornecer um valor para cada parâmetro obrigatório que não tenha um valor predefinido. |
| Valor Predefinido | Opcional. O valor utilizado para um parâmetro se não for passado quando o livro de execução é iniciado. Para definir um valor predefinido, escolha `Custom` . Selecione `None` se não quiser fornecer qualquer valor predefinido. |

## <a name="handle-runbook-output"></a>Saída do livro de dismas

A autoria gráfica guarda dados criados por qualquer atividade que não tenha uma ligação de saída com a [saída do livro de execução](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A saída é guardada com o trabalho do livro de corridas e está disponível para um livro de pais quando o livro de execução é usado em criança.

## <a name="work-with-powershell-expressions"></a>Trabalhar com expressões PowerShell

Uma das vantagens da autoria gráfica é que lhe permite construir um livro de corridas com o mínimo conhecimento da PowerShell. Atualmente, porém, você precisa saber um pouco de PowerShell para a população de certos valores de [parâmetros](#use-activities) e para definir condições de [ligação](#use-links-for-workflow). Esta secção fornece uma introdução rápida às expressões PowerShell. Todos os detalhes do PowerShell estão disponíveis na [Scripting com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Use uma expressão PowerShell como fonte de dados

Pode utilizar uma expressão PowerShell como fonte de dados para povoar o valor de um parâmetro de [atividade](#use-activities) com os resultados do código PowerShell. A expressão pode ser uma única linha de código que executa uma função simples ou múltiplas linhas que executam alguma lógica complexa. Qualquer saída de um comando que não seja atribuído a uma variável é a saída ao valor do parâmetro.

Por exemplo, as seguintes saídas de comando na data atual.

```powershell-interactive
Get-Date
```

O próximo código snippet constrói uma corda a partir da data atual e atribui-a a uma variável. O código envia o conteúdo da variável para a saída.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os seguintes comandos avaliam a data atual e devolvem uma corda indicando se o dia atual é um fim de semana ou um dia de semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Utilizar a saída de atividade

Para utilizar a saída de uma atividade anterior no seu livro de execução, utilize a `ActivityOutput` variável com a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, pode ter uma atividade com uma propriedade que requer o nome de uma máquina virtual. Neste caso, o seu livro de execução pode usar a seguinte expressão.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se a propriedade necessitar do objeto de máquina virtual em vez de apenas um nome, o livro de execução devolve todo o objeto usando a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

O livro de execução pode utilizar a saída de uma atividade numa expressão mais complexa, como o seguinte. Esta expressão concatena texto para o nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Comparar valores

Utilize [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação devolve um valor de verdade ou falso.

Por exemplo, a seguinte condição determina se a máquina virtual de uma atividade nomeada `Get-AzureVM` está atualmente parada.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A seguinte condição determina se a mesma máquina virtual está em qualquer estado que não seja parado.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Pode juntar várias condições no seu livro de execução utilizando um [operador lógico](https://technet.microsoft.com/library/hh847789.aspx), como `-and` ou `-or` . Por exemplo, a condição seguinte verifica para ver se a máquina virtual no exemplo anterior está em estado de paragem ou paragem.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Use hashtables

[Hashtables](https://technet.microsoft.com/library/hh847780.aspx) são pares de valor de nome que são úteis para devolver um conjunto de valores. Também pode ver um hashtable referido como um dicionário. Propriedades para certas atividades esperam um hashtable em vez de um valor simples.

Crie um hashtable utilizando a seguinte sintaxe. Pode conter qualquer número de entradas, mas cada uma é definida por um nome e valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a seguinte expressão cria um hashtable para ser usado como fonte de dados para um parâmetro de atividade que espera um hashtable de valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo seguinte utiliza a saída de uma atividade chamada `Get Twitter Connection` para povoar um hashtable.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Autenticar os recursos do Azure

Os livros de execução da Azure Automation que gerem os recursos do Azure exigem a autenticação ao Azure. A [conta Run As](automation-create-runas-account.md), também referida como um principal de serviço, é o mecanismo padrão que um livro de execução da Automação utiliza para aceder aos recursos do Gestor de Recursos Azure na sua subscrição. Pode adicionar esta funcionalidade a um livro de execução gráfico adicionando o ativo de `AzureRunAsConnection` ligação, que utiliza o cmdlet PowerShell [Get-AutomationConnection,](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) à tela. Também pode adicionar o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Este cenário é ilustrado no seguinte exemplo.

![Executar como atividades de autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A `Get Run As Connection` atividade, `Get-AutomationConnection` ou, é configurada com uma fonte de dados de valor constante chamada `AzureRunAsConnection` .

![Executar como configuração de ligação](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima atividade, `Connect-AzAccount` adiciona o Run autenticado Como conta para utilização no livro de execução.

![Conjunto de parâmetros Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Para os livros de execução da PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Note que estes pseudónimos não estão disponíveis para os seus livros gráficos. Um livro gráfico só pode `Connect-AzAccount` usar-se sozinho.

Para os campos de **parâmetros APPLICATIONID,** **CERTIFICATETHUMBPRINT,** e **TENANTID,** especifique o nome da propriedade para o caminho de campo, uma vez que a atividade produz um objeto com múltiplas propriedades. Caso contrário, quando o livro de execução executa, falha ao tentar autenticar. Isto é o que você precisa no mínimo para autenticar o seu livro de execução com a conta Run As.

Alguns subscritores criam uma conta De automação utilizando uma conta de utilizador da [Azure AD](automation-create-aduser-account.md) para gerir a implantação clássica do Azure ou para os recursos do Gestor de Recursos Azure. Para manter a retrocompatibilidade para estes assinantes, o mecanismo de autenticação a utilizar no seu livro de execução é o `Add-AzureAccount` cmdlet com um [ativo credencial](automation-credentials.md). O ativo representa um utilizador do Ative Directy com acesso à conta Azure.

Pode ativar esta funcionalidade para o seu rsido gráfico adicionando um ativo credencial à tela, seguido de uma atividade que utiliza o ativo credencial para a `Add-AzureAccount` sua entrada. Veja o seguinte exemplo.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

O livro de execução deve autenticar no seu início e após cada ponto de verificação. Assim, deve utilizar uma `Add-AzureAccount` atividade após qualquer `Checkpoint-Workflow` atividade. Não precisa de usar uma atividade de credencial adicional.

![Produção de atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Exportar um livro gráfico

Só pode exportar a versão publicada de um livro de execução gráfico. Se o livro de execução ainda não tiver sido publicado, o botão **Export** está desativado. Quando clica no botão **Export,** o livro de execução descarrega para o seu computador local. O nome do ficheiro corresponde ao nome do livro de execução com uma extensão **.graphrunbook.**

## <a name="import-a-graphical-runbook"></a>Importar um livro de execução gráfico

Pode importar um ficheiro de fluxo de fluxo de fluxo de fluxo de fluxo de powershell gráfico ou gráfico selecionando a opção **Import** ao adicionar um livro de execução. Quando selecionar o ficheiro para importar, pode manter o mesmo nome ou fornecer um novo. O campo **'Tipo de Livro de Execução'** apresenta o tipo de livro de execução depois de avaliar o ficheiro selecionado. Se tentar selecionar um tipo diferente que não esteja correto, o editor gráfico apresenta uma mensagem notando que existem potenciais conflitos e que pode haver erros de sintaxe durante a conversão.

![Livro de importação](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Teste um livro gráfico

Cada livro gráfico da Azure Automation tem uma versão Draft e uma versão Publicada. Só pode executar a versão Publicada, enquanto só pode editar a versão Draft. A versão Publicada não é afetada por quaisquer alterações feitas à versão de Rascunho. Quando a versão Do Projeto estiver pronta a ser utilizada, publica-a, que substitui a versão atual publicada com a versão Draft.

Pode testar a versão Draft de um livro de execução no portal Azure, deixando a versão Publicada inalterada. Em alternativa, pode testar um novo livro de executões antes de ser publicado para que possa verificar se o livro funciona corretamente antes de qualquer substituição da versão. O teste de um livro executa a versão Draft e garante que quaisquer ações que executa estejam concluídas. Não é criado histórico de trabalho, mas o painel de saída de teste mostra a saída.

Abra o controlo de teste para o seu livro de execução gráfico abrindo o livro de execução para edição e, em seguida, clicando na **vidraça de teste**. O controlo de teste solicita os parâmetros de entrada e pode iniciar o livro de corridas clicando em **Iniciar**.

## <a name="publish-a-graphical-runbook"></a>Publicar um livro gráfico

Publique um livro de execução gráfico abrindo o livro de execução para edição e, em seguida, clicando **em Publicar**. Os possíveis estatutos para o livro de execução são:

* Novo- o livro de corridas ainda não foi publicado. 
* Publicado, o livro de corridas foi publicado.
* Na edição -- o livro de execução foi editado após a sua publicação, e as versões Draft e Published são diferentes.

![Estatutos do livro de corridas](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Tem a opção de reverter para a versão publicada de um livro de execução. Esta operação deita fora quaisquer alterações feitas desde que o livro de execução foi publicado pela última vez. Substitui a versão Draft do livro de execução pela versão Publicada.

## <a name="next-steps"></a>Próximos passos

* Para começar com os livros de execução powerShell Workflow, consulte o meu primeiro livro de [execução powerShell Workflow](automation-first-runbook-textual.md).
* Para começar com livros gráficos, veja [o meu primeiro livro de corridas gráficos.](automation-first-runbook-graphical.md)
* Para saber mais sobre os tipos de livro de corridas e as suas vantagens e limitações, consulte os tipos de livro de [execução da Automação Azure](automation-runbook-types.md).
* Para entender como autenticar usando a automatização Executar Como conta, consulte [Configure Azure Run As Account](automation-sec-configure-azure-runas-account.md).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
