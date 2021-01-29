---
title: Livros gráficos do autor na Azure Automation
description: Este artigo diz como autoria de um livro gráfico sem trabalhar com código.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: ec74ca19978a4164289276d44b34eb14b694687f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051586"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Livros gráficos do autor na Azure Automation

Todos os livros em Azure Automation são fluxos de trabalho Windows PowerShell. Os livros gráficos de runbooks e gráficos powerShell Workflow geram código PowerShell que os trabalhadores da Automação funcionam mas que não é possível ver ou modificar. Pode converter um livro gráfico para um runbook gráfico powerShell Workflow e vice-versa. No entanto, não é possível converter estes livros num livro de texto. Além disso, o editor gráfico da Automation não pode importar um livro de texto.

A autoria gráfica permite-lhe criar livros para a Azure Automation sem as complexidades do código de trabalho do Windows PowerShell ou powerShell. Pode adicionar atividades à tela a partir de uma biblioteca de cmdlets e runbooks, ligá-las e configurá-las para formar um fluxo de trabalho. Se já trabalhou com o System Center Orchestrator ou com a Service Management Automation (SMA), a autoria gráfica deve parecer familiar. Este artigo fornece uma introdução aos conceitos que precisa para começar a criar um runbook gráfico.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Pode abrir o editor gráfico no portal Azure criando ou editando um runbook gráfico.

![Espaço de trabalho gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As secções seguintes descrevem os controlos no editor gráfico.

### <a name="canvas-control"></a>Controlo de lona

O controlo de tela permite-lhe conceber o seu runbook. Pode adicionar atividades dos nós no controlo da Biblioteca ao livro de recortes e conectá-las com links para definir a lógica do livro de bordo. Na parte inferior da tela, existem controlos que permitem fazer zoom para dentro e para fora.

### <a name="library-control"></a>Controlo da biblioteca

O controlo da Biblioteca permite-lhe selecionar [atividades](#use-activities) para adicionar ao seu runbook. Adicione-os à tela, onde pode ligá-los a outras atividades. O controlo da Biblioteca inclui as secções definidas na tabela seguinte.

| Section | Description |
|:--- |:--- |
| Cmdlets |Todos os cmdlets que podem ser usados no seu runbook. Os cmdlets são organizados por módulo. Todos os módulos que instalou na sua conta Demôm automação estão disponíveis. |
| Runbooks |Os livros na sua conta de Automação. Pode adicionar estes livros à tela para serem usados como livros infantis. Apenas são apresentados livros de bordo do mesmo tipo de núcleo que o livro de bordo que está a ser editado. Para livros gráficos, apenas são apresentados livros de execução baseados em PowerShell. Para os livros gráficos de fluxo de trabalho powerShell, apenas são apresentados livros de execução baseados em fluxos de trabalho powerShell. |
| Elementos |Os [ativos de automatização](/previous-versions/azure/dn939988(v=azure.100)) na sua conta Automation que pode utilizar no seu runbook. Adicionar um ativo a um livro de execução adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos variáveis, pode selecionar se deve adicionar uma atividade para obter a variável ou definir a variável. |
| Controlo do Runbook |Controle as atividades que podem ser usadas no seu livro de bordo atual. Uma atividade de Junção requer múltiplas entradas e espera até que todas tenham terminado antes de continuar o fluxo de trabalho. Uma atividade de código executa uma ou mais linhas de PowerShell ou código de fluxo de trabalho PowerShell, dependendo do tipo de runbook gráfico. Pode utilizar esta atividade para código personalizado ou para funcionalidades difíceis de realizar com outras atividades. |

### <a name="configuration-control"></a>Controlo de configuração

O controlo de configuração permite-lhe fornecer detalhes para um objeto selecionado na tela. As propriedades disponíveis neste controlo dependem do tipo de objeto selecionado. Quando escolhe uma opção no comando de Configuração, abre lâminas adicionais para fornecer mais informações.

### <a name="test-control"></a>Controlo de testes

O controlo de Teste não é apresentado quando o editor gráfico é iniciado pela primeira vez. É aberto quando se testa interativamente um livro de bordo gráfico.

## <a name="use-activities"></a>Utilizar atividades

As atividades são os blocos de construção de um livro de bordo. Uma atividade pode ser um cmdlet PowerShell, um livro infantil ou um fluxo de trabalho. Pode adicionar uma atividade ao runbook clicando-a com o controlo da Biblioteca e selecionando **Adicionar à tela**. Em seguida, pode clicar e arrastar a atividade para colocá-la em qualquer lugar na tela que você gosta. A localização da atividade na tela não afeta o funcionamento do livro de recortes. Pode apresentar o seu livro de corridas da forma que achar mais adequado para visualizar o seu funcionamento.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecione uma atividade na tela para configurar as suas propriedades e parâmetros na lâmina de configuração. Pode alterar o rótulo da atividade para um nome que encontre descritivo. O runbook ainda executa o cmdlet original. Está simplesmente a alterar o nome de exibição que o editor gráfico utiliza. Note que a etiqueta deve ser única dentro do livro de bordo.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para um cmdlet particular. Todos os cmdlets têm pelo menos um parâmetro definido, e alguns têm vários conjuntos. Se um cmdlet tiver vários parâmetros, deve selecionar o que usar antes de configurar parâmetros. Pode alterar o parâmetro definido por uma atividade selecionando Conjunto de **Parâmetros** e escolhendo outro conjunto. Neste caso, perdem-se os valores de parâmetro que já configuraram.

No exemplo seguinte, o [cmdlet Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0&preserve-view=true) tem três parâmetros. O exemplo utiliza um conjunto chamado **ListVirtualMachineInResourceGroupGroup,** com um único parâmetro opcional, para devolver todas as máquinas virtuais num grupo de recursos. O exemplo também utiliza o parâmetro **GetVirtualMachineInResourceGroupGroup** para especificar a máquina virtual para devolver. Este conjunto tem dois parâmetros obrigatórios e um parâmetro opcional.

![Conjunto de parâmetros](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetros

Quando especificar um valor para um parâmetro, selecione uma fonte de dados para determinar como o valor é especificado. As fontes de dados disponíveis para um determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, Null não é uma opção disponível para um parâmetro que não permite valores nulos.

| Origem de dados | Description |
|:--- |:--- |
| Valor Constante |Digite um valor para o parâmetro. Esta fonte de dados só está disponível para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime, Switch. |
| Saída de Atividade |Utilize a produção de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas estão listadas. Para o valor do parâmetro, utilize apenas a atividade que produz a saída. Se a atividade der um objeto com múltiplas propriedades, pode digitar o nome de uma propriedade específica após a seleção da atividade. |
| Entrada de runbook |Selecione uma entrada de runbook como entrada para o parâmetro de atividade. |
| Ativo Variável |Selecione uma variável de automação como entrada. |
| Ativo credencial |Selecione uma credencial de automação como entrada. |
| Ativo certificado |Selecione um certificado de automação como entrada. |
| Ativo de conexão |Selecione uma ligação de automatização como entrada. |
| Expressão do PowerShell |Especifique uma [simples expressão PowerShell](#work-with-powershell-expressions). A expressão é avaliada antes da atividade e o resultado é usado para o valor do parâmetro. Pode utilizar variáveis para se referir à saída de uma atividade ou a um parâmetro de entrada de um livro de execução. |
| Não Configurado |Limpar qualquer valor que tenha sido previamente configurado. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Estes são parâmetros powerShell-common ou outros parâmetros personalizados. O editor gráfico apresenta uma caixa de texto onde pode fornecer parâmetros usando a sintaxe PowerShell. Por exemplo, para utilizar o `Verbose` parâmetro comum, deve especificar `-Verbose:$True` .

### <a name="retry-activity"></a>Atividade de retíria

A funcionalidade de retenção para uma atividade permite que seja executada várias vezes até que uma determinada condição seja satisfeita, tal como um loop. Pode utilizar esta funcionalidade para atividades que deverão ser executadas várias vezes, que sejam propensas a erros, que possam necessitar de mais do que uma tentativa de sucesso, ou testar as informações de saída da atividade para dados válidos.

Quando ativar a repetição de uma atividade, pode definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o livro de bordo espera antes de voltar a executar a atividade. Se não especificar um atraso, a atividade volta a funcionar imediatamente após a sua conclusão.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Screenshot das definições de funcionalidade de relíndi ativação.":::

A condição de relemissão é uma expressão PowerShell que é avaliada após cada vez que a atividade é executado. Se a expressão se resolver para True, a atividade volta a funcionar. Se a expressão se resolver para Falso, a atividade não volta a funcionar e o livro passa para a atividade seguinte.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Screenshot mostrando a Retry até que esta condição seja um campo verdadeiro e exemplos de expressões PowerShell que podem ser usadas na condição de relemissão.":::

A condição de retagem pode usar uma variável nomeada `RetryData` que dá acesso à informação sobre as retrações da atividade. Esta variável tem as propriedades no quadro seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| `NumberOfAttempts` |Número de vezes que a atividade foi executada. |
| `Output` |Saída da última parte da atividade. |
| `TotalDuration` |O tempo decorrido desde que a atividade começou pela primeira vez. |
| `StartedAt` |Tempo (em formato UTC) quando a atividade foi iniciada pela primeira vez. |

Seguem-se exemplos de condições de relemis de atividade.

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

Depois de configurar uma condição de retíria para uma atividade, a atividade inclui duas pistas visuais para lembrá-lo. Um é apresentado na atividade e o outro é mostrado quando revê a configuração da atividade.

![Indicadores visuais de relívisão de atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de script de fluxo de trabalho

Um controle de script de fluxo de trabalho é uma atividade especial que aceita o script PowerShell ou PowerShell Workflow, dependendo do tipo de livro gráfico que está sendo da autoria. Este controlo fornece funcionalidades que podem não estar disponíveis por outros meios. Não pode aceitar parâmetros, mas pode usar variáveis para a saída da atividade e parâmetros de entrada do runbook. Qualquer saída da atividade é adicionada ao databus. Uma exceção é a saída sem ligação de saída, caso em que a saída é adicionada à saída do runbook.

Por exemplo, o seguinte código executa cálculos de data usando uma variável de entrada de runbook chamada `NumberOfDays` . Em seguida, envia um valor de DataTime calculado como saída a utilizar por atividades subsequentes no livro de execução.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Utilizar links para fluxo de trabalho

Um link num runbook gráfico liga duas atividades. É exibido na tela como uma seta que aponta da atividade da fonte para a atividade de destino. As atividades decorrem na direção da seta com a atividade de destino a começar após a conclusão da atividade da fonte.

### <a name="create-a-link"></a>Criar uma ligação

Pode criar uma ligação entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade do destino e liberte-a.

![Criar uma ligação](media/automation-graphical-authoring-intro/create-link-options.png)

Selecione o link para configurar as suas propriedades na lâmina de configuração. As propriedades incluem o tipo de ligação, que é descrito na tabela seguinte.

| Tipo de ligação | Description |
|:--- |:--- |
| Pipeline |A atividade de destino é executado uma vez para cada saída de objeto da atividade de origem. A atividade de destino não funciona se a atividade de origem não resultar em saída. A saída da atividade de origem está disponível como um objeto. |
| Sequence |A atividade de destino funciona apenas uma vez quando recebe a produção da atividade de origem. A saída da atividade da fonte está disponível como uma variedade de objetos. |

### <a name="start-runbook-activity"></a>Inicie a atividade do runbook

Um runbook gráfico começa com quaisquer atividades que não tenham um link de entrada. Muitas vezes há apenas uma atividade que funciona como a atividade inicial para o runbook. Se várias atividades não tiverem uma ligação de entrada, o livro de recortes começa por executá-los em paralelo. Segue os links para executar outras atividades à medida que cada um completa.

### <a name="specify-link-conditions"></a>Especifique as condições de ligação

Quando especifica uma condição num link, a atividade de destino só funciona se a condição se resolver para True. Normalmente, usa-se uma `ActivityOutput` variável numa condição para recuperar a saída da atividade da fonte.

Para uma ligação de gasoduto, deve especificar uma condição para um único objeto. O livro avalia a condição de cada objeto de saída pela atividade da fonte. Em seguida, executa a atividade de destino para cada objeto que satisfaz a condição. Por exemplo, com uma atividade de origem de `Get-AzVM` , pode utilizar a seguinte sintaxe para uma ligação de gasoduto condicional para recuperar apenas máquinas virtuais no grupo de recursos denominado Grupo1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para uma ligação de sequência, o livro de recortes só avalia a condição uma vez, uma vez que uma única matriz contendo todos os objetos da atividade da fonte é devolvida. Por causa disso, o livro não pode usar uma ligação de sequência para filtragem, como pode com uma ligação de pipeline. A ligação de sequência pode simplesmente determinar se a próxima atividade é ou não executada.

Por exemplo, tome o seguinte conjunto de atividades no nosso livro de vídeo **start:**

![Ligação condicional com sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

O livro de bordo utiliza três ligações de sequência diferentes que verificam os valores dos parâmetros de entrada `VMName` e `ResourceGroupName` determinam as medidas adequadas a tomar. Possíveis ações são iniciar um único VM, iniciar todos os VMs no grupo de recursos, ou iniciar todos os VMs em uma subscrição. Para a ligação de sequência entre `Connect to Azure` `Get single VM` e, aqui está a lógica da condição:

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

Quando utiliza um link condicional, os dados disponíveis da atividade de origem para outras atividades nesse ramo são filtrados pela condição. Se uma atividade é a fonte de múltiplos links, os dados disponíveis para atividades em cada ramo dependem da condição na ligação que liga a esse ramo.

Por exemplo, a `Start-AzVM` atividade no runbook abaixo começa todas as máquinas virtuais. Tem duas ligações condicionais. O primeiro elo condicional utiliza a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` para filtrar se a `Start-AzVM` atividade terminar com sucesso. A segunda ligação condicional utiliza a expressão `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` para filtrar se a `Start-AzVm` atividade não iniciar o arranque da máquina virtual.

![Exemplo de ligação condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que siga o primeiro link e utilize a saída de atividade `Get-AzureVM` apenas recupera as máquinas virtuais que foram iniciadas no momento em que `Get-AzureVM` foi executada. Qualquer atividade que siga o segundo link só recebe as máquinas virtuais que foram paradas no momento em que `Get-AzureVM` foi executada. Qualquer atividade após o terceiro link obtém todas as máquinas virtuais, independentemente do seu estado de funcionamento.

### <a name="use-junctions"></a>Use junções

Uma junção é uma atividade especial que aguarda até que todos os ramos de entrada tenham terminado. Isto permite que o runbook execute várias atividades em paralelo e certifique-se de que todos tenham terminado antes de seguir em frente.

Enquanto uma junção pode ter um número ilimitado de ligações recebidas, apenas uma dessas ligações pode ser um oleoduto. O número de ligações de sequência de entrada não está limitado. Pode criar a junção com várias ligações de gasoduto de entrada e guardar o livro de execução, mas falhará quando for executado.

O exemplo abaixo é parte de um runbook que inicia um conjunto de máquinas virtuais enquanto simultaneamente descarrega patches para serem aplicados a essas máquinas. Utiliza uma junção para garantir que ambos os processos sejam concluídos antes de o livro continuar.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Trabalhar com ciclos

Forma-se um ciclo quando uma atividade de destino se liga à sua atividade de origem ou a outra atividade que eventualmente se liga à sua origem. A autoria gráfica não suporta atualmente ciclos. Se o seu livro de execução tiver um ciclo, guarda corretamente, mas recebe um erro quando funciona.

![Cíclico](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Partilhar dados entre atividades

Todos os dados que uma atividade produz com uma ligação de saída são escritos ao databus para o livro de corridas. Qualquer atividade no livro pode usar dados no databus para preencher valores de parâmetros ou incluir no código do script. Uma atividade pode aceder à produção de qualquer atividade anterior no fluxo de trabalho.

A forma como os dados são escritos ao databus depende do tipo de ligação na atividade. Para uma ligação de gasoduto, os dados são de saída como múltiplos objetos. Para uma ligação de sequência, os dados são de saída como uma matriz. Se houver apenas um valor, é a saída como um conjunto de elementos únicos.

O seu livro de corridas tem duas formas de aceder aos dados do databus: 
* Use uma fonte de dados de saída de atividade.
* Utilize uma fonte de dados de expressão PowerShell.

O primeiro mecanismo utiliza uma fonte de dados de saída de atividade para povoar um parâmetro de outra atividade. Se a saída for um objeto, o livro pode especificar uma única propriedade.

![produção de atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

O segundo mecanismo de acesso a dados recupera a saída de uma atividade numa fonte de dados de expressão PowerShell ou numa atividade de script de fluxo de trabalho com uma `ActivityOutput` variável, utilizando a sintaxe mostrada abaixo. Se a saída for um objeto, o seu livro de execução pode especificar uma única propriedade.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Utilize postos de controlo

Pode definir [pontos de verificação](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) num livro de trabalho gráfico powerShell workflow selecionando **o livro de verificação** em qualquer atividade. Isto faz com que seja definido um ponto de verificação após o funcionação da atividade.

![Check Point](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os pontos de verificação só são ativados em livros gráficos de fluxo de trabalho powerShell e não estão disponíveis em livros gráficos. Se o livro utilizar cmdlets Azure, deve seguir qualquer atividade de verificação com uma `Connect-AzAccount` atividade. A operação de ligação é utilizada no caso de o livro de execuções ser suspenso e deve reiniciar a partir deste ponto de verificação num trabalhador diferente.

## <a name="handle-runbook-input"></a>Inserir runbook de pega

Um livro de execução requer entrada de um utilizador que inicia o livro de aplicação através do portal Azure ou de outro livro de aplicação, se o atual for utilizado em criança. Por exemplo, para um livro de execução que cria uma máquina virtual, o utilizador poderá precisar de fornecer informações como o nome da máquina virtual e outras propriedades cada vez que o livro de execução começa.

O livro aceita a entrada definindo um ou mais parâmetros de entrada. O utilizador fornece valores para estes parâmetros cada vez que o livro de recortes começa. Quando o utilizador inicia o livro de aplicação utilizando o portal Azure, o utilizador é solicitado a fornecer valores para cada parâmetro de entrada suportado pelo livro de aplicação.

Ao autorizar o seu runbook, pode aceder aos seus parâmetros de entrada clicando em **Entrada e saída** na barra de ferramentas do runbook. Isto abre o controlo de entrada e saída onde pode editar um parâmetro de entrada existente ou criar um novo clicando na **entrada Adicionar**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades no quadro seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Nome | Obrigatório. O nome do parâmetro. O nome deve ser único dentro do livro de bordo. Deve começar com uma letra e conter apenas letras, números e sublinhados. O nome não pode conter um espaço. |
| Description |Opcional. Descrição da finalidade para o parâmetro de entrada. |
| Tipo | Opcional. Tipo de dados esperado para o valor do parâmetro. O portal Azure fornece um controlo adequado para o tipo de dados para cada parâmetro quando solicita a entrada. Os parâmetros suportados são String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se um tipo de dado não for selecionado, predefinitivo ao String.|
| Obrigatório | Opcional. Definição que especifica se deve ser fornecido um valor para o parâmetro. Se `yes` escolher, deve ser fornecido um valor quando o livro de recortes for iniciado. Se `no` escolher, não é necessário um valor quando o livro de execução é iniciado e pode ser utilizado um valor predefinido. O livro de execuções não pode arrancar se não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Valor Predefinido | Opcional. O valor utilizado para um parâmetro se não for passado quando o livro de bordo for iniciado. Para definir um valor predefinido, escolha `Custom` . Selecione `None` se não quiser fornecer qualquer valor predefinido. |

## <a name="handle-runbook-output"></a>Executar a saída do livro de execução

A autoria gráfica guarda dados criados por qualquer atividade que não tenha uma ligação de saída com a [saída do livro de bordo.](./automation-runbook-output-and-messages.md) A saída é guardada com o trabalho de runbook e está disponível para um livro de bordo dos pais quando o livro de recortes é usado em criança.

## <a name="work-with-powershell-expressions"></a>Trabalhar com expressões PowerShell

Uma das vantagens da autoria gráfica é que permite construir um runbook com o mínimo conhecimento da PowerShell. Atualmente, porém, precisa de conhecer um pouco de PowerShell para povoar certos valores de [parâmetros](#use-activities) e para definir [condições de ligação](#use-links-for-workflow). Esta secção proporciona uma introdução rápida às expressões PowerShell. Todos os detalhes do PowerShell estão disponíveis no [Scripting com o Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Use uma expressão PowerShell como fonte de dados

Pode utilizar uma expressão PowerShell como fonte de dados para preencher o valor de um parâmetro de [atividade](#use-activities) com os resultados do código PowerShell. A expressão pode ser uma única linha de código que executa uma função simples ou múltiplas linhas que executam alguma lógica complexa. Qualquer saída de um comando que não seja atribuída a uma variável é a saída para o valor do parâmetro.

Por exemplo, as seguintes saídas de comando na data atual.

```powershell-interactive
Get-Date
```

O próximo corte de código constrói uma cadeia a partir da data atual e atribui-a a uma variável. O código envia o conteúdo da variável para a saída.

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

### <a name="use-activity-output"></a>Utilizar a saída da atividade

Para utilizar a saída de uma atividade anterior no seu runbook, utilize a `ActivityOutput` variável com a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, você pode ter uma atividade com uma propriedade que requer o nome de uma máquina virtual. Neste caso, o seu livro de bordo pode utilizar a seguinte expressão.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se a propriedade necessitar do objeto da máquina virtual em vez de apenas um nome, o livro de recortes devolve todo o objeto usando a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

O runbook pode utilizar a saída de uma atividade numa expressão mais complexa, como a seguinte. Esta expressão concatena texto para o nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Comparar valores

Utilize [operadores de comparação](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação devolve um valor de Verdadeiro ou Falso.

Por exemplo, a seguinte condição determina se a máquina virtual de uma atividade nomeada `Get-AzureVM` está atualmente parada.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A seguinte condição determina se a mesma máquina virtual está em qualquer estado que não seja parado.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Pode juntar várias condições no seu livro de bordo utilizando um [operador lógico,](/powershell/module/microsoft.powershell.core/about/about_logical_operators)como `-and` ou `-or` . Por exemplo, a seguinte condição verifica se a máquina virtual no exemplo anterior está num estado de paragem ou paragem.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Use haxixe

[Hashtables](/powershell/module/microsoft.powershell.core/about/about_hash_tables) são pares de valor-nome que são úteis para devolver um conjunto de valores. Você também pode ver um haxixe referido como um dicionário. Propriedades para certas atividades esperam um haxixe em vez de um valor simples.

Crie um haxixe utilizando a seguinte sintaxe. Pode conter qualquer número de entradas, mas cada uma é definida por um nome e valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria um haxixe para ser usado como fonte de dados para um parâmetro de atividade que espera um haxixe de valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo a seguir utiliza a saída de uma atividade chamada `Get Twitter Connection` para povoar um haxixe.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Autenticar recursos da Azure

Os runbooks na Azure Automation que gerem os recursos da Azure requerem autenticação ao Azure. A [conta Run As](./automation-security-overview.md), também referida como principal de serviço, é o mecanismo padrão que um runbook Automation usa para aceder aos recursos do Azure Resource Manager na sua subscrição. Pode adicionar esta funcionalidade a um runbook gráfico adicionando o `AzureRunAsConnection` ativo de ligação, que utiliza o cmdlet PowerShell [Get-AutomationConnection,](/system-center/sma/manage-global-assets) à tela. Também pode adicionar o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Este cenário é ilustrado no exemplo seguinte.

![Executar como atividades de autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A `Get Run As Connection` atividade, `Get-AutomationConnection` ou, é configurada com uma fonte de dados de valor constante chamada `AzureRunAsConnection` .

![Executar como configuração de ligação](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima atividade, `Connect-AzAccount` adiciona a execução autenticada Como conta para utilização no livro de recortes.

![Conjunto de parâmetros de Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Para os livros powerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Note que estes pseudónimos não estão disponíveis para os seus livros gráficos. Um livro gráfico só pode `Connect-AzAccount` usar-se sozinho.

Para os campos de **parâmetros APPLICATIONID,** **CERTIFICATETHUMBPRINT,** e **TENANTID,** especifiquem o nome do imóvel para o percurso de campo, uma vez que a atividade produz um objeto com múltiplas propriedades. Caso contrário, quando o livro de recortes executa, falha enquanto tenta autenticar. Isto é o que precisa no mínimo para autenticar o seu runbook com a conta Run As.

Alguns subscritores criam uma conta Automation utilizando uma [conta de utilizador Azure AD](./shared-resources/credentials.md) para gerir a implementação clássica do Azure ou para os recursos do Azure Resource Manager. Para manter a retrocompatibilidade para estes assinantes, o mecanismo de autenticação a utilizar no seu runbook é o `Add-AzureAccount` cmdlet com um [ativo credencial](./shared-resources/credentials.md). O ativo representa um utilizador do Ative Directory com acesso à conta Azure.

Pode ativar esta funcionalidade para o seu runbook gráfico adicionando um ativo credencial à tela, seguido de uma `Add-AzureAccount` atividade que utiliza o ativo credencial para a sua entrada. Veja o seguinte exemplo.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

O livro deve autenticar no seu início e após cada posto de controlo. Assim, deve utilizar uma `Add-AzureAccount` atividade após qualquer `Checkpoint-Workflow` atividade. Não precisa de utilizar uma atividade de credencial adicional.

![Saída de atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Exportar um runbook gráfico

Só é possível exportar a versão publicada de um runbook gráfico. Se o livro de execução ainda não tiver sido publicado, o botão **Exportação** está desativado. Ao clicar no botão **Exportar,** o livro de recortes descarrega para o computador local. O nome do ficheiro corresponde ao nome do livro de bordo com uma extensão **.graphrunbook.**

## <a name="import-a-graphical-runbook"></a>Importar um runbook gráfico

Pode importar um ficheiro de fluxo de trabalho gráfico ou gráfico PowerShell selecionando a opção **Import** ao adicionar um livro de execução. Quando selecionar o ficheiro para importar, pode ficar com o mesmo nome ou fornecer um novo. O **campo Runbook Type** exibe o tipo de livro de execução depois de avaliar o ficheiro selecionado. Se tentar selecionar um tipo diferente que não esteja correto, o editor gráfico apresenta uma mensagem observando que existem potenciais conflitos e pode haver erros de sintaxe durante a conversão.

![Livro de importação](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Teste um livro gráfico

Cada livro gráfico da Azure Automation tem uma versão Draft e uma versão publicada. Pode executar apenas a versão Publicada, enquanto só pode editar a versão Draft. A versão Publicada não é afetada por quaisquer alterações feitas à versão de Rascunho. Quando a versão Draft estiver pronta a ser utilizada, publica-a, o que substitui a versão atual publicada com a sua versão Draft.

Pode testar a versão Draft de um runbook no portal Azure, deixando a versão publicada inalterada. Em alternativa, pode testar um novo livro de recortes antes de ser publicado para que possa verificar se o livro funciona corretamente antes de qualquer substituição da versão. O teste de um livro de execuções executa a versão Draft e garante que quaisquer ações que executa estão concluídas. Não é criado nenhum histórico de emprego, mas o painel de saída de teste mostra a saída.

Abra o controlo de Teste para o seu runbook gráfico abrindo o manual de execução para edição e, em seguida, clicando no **painel de teste**. O controlo do teste indica para os parâmetros de entrada e pode iniciar o livro de execução clicando **em Iniciar**.

## <a name="publish-a-graphical-runbook"></a>Publique um runbook gráfico

Publique um runbook gráfico abrindo o runbook para edição e, em seguida, clicando em **Publicar**. Os possíveis estatutos para o livro são:

* Novo- o livro ainda não foi publicado. 
* Publicado, o livro foi publicado.
* Em edição -- o livro foi editado depois de ter sido publicado, e as versões Draft e Published são diferentes.

![Estatutos de runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Tem a opção de reverter para a versão publicada de um livro de execução. Esta operação deita fora quaisquer alterações feitas desde que o livro foi publicado pela última vez. Substitui a versão Draft do livro de bordo pela versão publicada.

## <a name="next-steps"></a>Passos seguintes

* Para começar com os runbooks gráficos, consulte [Tutorial: Crie um runbook gráfico](learn/automation-tutorial-runbook-graphical.md).
* Para saber mais sobre os tipos de runbook e as suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).
* Para compreender como autenticar utilizando a conta Automation Run As, consulte [a conta Executar Como.](automation-security-overview.md#run-as-account)
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation).
