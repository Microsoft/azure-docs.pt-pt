---
title: Gráfico de criação na automatização do Azure
description: Criação de gráficos permite-lhe criar runbooks de automatização do Azure sem trabalhando com código. Este artigo fornece uma introdução à criação de gráficos e todos os detalhes necessários para começar a criar um runbook gráfico.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d7626706951cc522dce9c6d70251455e64300bc
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476692"
---
# <a name="graphical-authoring-in-azure-automation"></a>Gráfico de criação na automatização do Azure

Criação gráfica permite-lhe criar runbooks de automatização do Azure sem as complexidades do código subjacente do Windows PowerShell ou o fluxo de trabalho do PowerShell. Adicionar atividades à tela de uma biblioteca de cmdlets e runbooks, ligá-las em conjunto e configurar para formar um fluxo de trabalho. Se alguma vez já trabalhou com o System Center Orchestrator ou o Service Management Automation (SMA), em seguida, isso lhe parecerá familiar para

Este artigo fornece uma introdução à criação de gráficos e os conceitos que precisa para começar a utilizar na criação de um runbook gráfico.

## <a name="graphical-runbooks"></a>Runbooks gráficos

Todos os runbooks na automatização do Azure são fluxos de trabalho do Windows PowerShell. Runbooks de gráfico e fluxo de trabalho de PowerShell gráfico gerar o código do PowerShell que é executado por funções de trabalho de automatização, mas não é possível vê-la ou modificar diretamente. Um runbook gráfico pode ser convertido para um runbook de fluxo de trabalho de PowerShell gráfico e vice-versa, mas não pode ser convertidos para um runbook textual. Um runbook textual existente não pode ser importado para o editor gráfico.

## <a name="overview-of-graphical-editor"></a>Descrição geral do editor gráfico

É possível abrir o editor gráfico no portal do Azure ao criar ou editar um runbook gráfico.

![Área de trabalho gráfica](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As secções seguintes descrevem os controles no editor gráfico.

### <a name="canvas"></a>Canvas

A tela é onde criar o runbook. Adicionar atividades a partir de nós no controlo da biblioteca para o runbook e ligá-los com ligações para definir a lógica do runbook.

Pode usar os controles na parte inferior da tela para ampliar e reduzir.

### <a name="library-control"></a>Controlo da biblioteca

Controlo da biblioteca é onde seleciona [atividades](#activities) para adicionar ao runbook. Adicioná-los para a tela, onde pode ligá-los para outras atividades. Ele inclui quatro secções descritas na tabela a seguir:

| Section | Description |
|:--- |:--- |
| Cmdlets |Inclui todos os cmdlets que pode ser utilizados no runbook. Cmdlets são organizados por módulo. Todos os módulos que instalou na sua conta de automatização estão disponíveis. |
| Runbooks |Inclui os runbooks na conta de automatização. Estes runbooks podem ser adicionados à tela para ser usado como runbooks subordinados. São apresentados apenas os runbooks do mesmo tipo de principal como o runbook que está a ser editado; para o gráfico são apresentados runbooks apenas baseada no PowerShell de runbooks, enquanto para runbooks de fluxo de trabalho de PowerShell gráfico são mostrados apenas PowerShell fluxo de trabalho baseada em runbooks. |
| Elementos |Inclui a [recursos de automatização](/previous-versions/azure/dn939988(v=azure.100)) na sua conta de automatização que pode ser utilizada no runbook. Quando adiciona um recurso a um runbook, ele adiciona uma atividade de fluxo de trabalho que obtém o recurso selecionado. No caso de recursos de variável, pode selecionar se pretende adicionar uma atividade para obter a variável ou definir a variável. |
| Controlo do Runbook |Inclui atividades de controlo do runbook que podem ser utilizadas no runbook atual. R *junção* usa várias entradas e aguarda até que concluíram antes de continuar o fluxo de trabalho. R *código* uma ou mais linhas de código do PowerShell ou o fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico de execuções de atividades. Pode utilizar esta atividade de código personalizado ou para a funcionalidade que é difícil de atingir com outras atividades. |

### <a name="configuration-control"></a>Controlo de configuração

O controle de configuração é onde fornecer detalhes para um objeto selecionado na tela. As propriedades disponíveis neste controlo depende do tipo de objeto selecionado. Quando seleciona uma opção no controlo da configuração, abre painéis adicionais para fornecer informações adicionais.

### <a name="test-control"></a>Controlo de teste

O controle de teste não é apresentado quando o editor gráfico é iniciado pela primeira vez. É aberto quando interativamente [testar um runbook gráfico](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Procedimentos de runbook gráfico

### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportar e importar um runbook gráfico

Só pode exportar a versão publicada de um runbook gráfico. Se ainda não tenha sido publicado do runbook, o **exportar** botão está desativado. Quando clica no **exportar** botão, o runbook é transferido para o computador local. O nome do ficheiro corresponde ao nome do runbook com um *graphrunbook* extensão.

Pode importar um ficheiro de runbook gráfico ou fluxo de trabalho de PowerShell gráfico selecionando o **importar** opção ao adicionar um runbook. Quando seleciona o ficheiro a importar, pode manter o mesmo **nome** ou fornecer um novo. O campo de tipo de Runbook irá apresentar o tipo de runbook depois de ele avalia o ficheiro selecionado e se tentar selecionar um tipo diferente que não está correto, uma mensagem será apresentada observar, há possíveis conflitos e durante a conversão, poderia haver sintaxe erros.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testar um runbook gráfico

Pode testar a versão de rascunho de um runbook no portal do Azure enquanto deixando a versão publicada do runbook inalterado, ou pode testar um runbook novo antes de ele foi publicado. Isto permite-lhe verificar se o runbook está a funcionar corretamente antes de substituir a versão publicada. Quando testa um runbook, o runbook de rascunho é executado e quaisquer ações que executar são concluídas. Nenhum histórico da tarefa é criado, mas o resultado é apresentado no painel de resultados do teste.

Abra o controle de teste para um runbook, abrindo o runbook para edição e, em seguida, clique nas **painel de teste** botão.

O controle de teste pede-lhe para quaisquer parâmetros de entrada, e pode iniciar o runbook ao clicar no **iniciar** botão.

### <a name="publishing-a-graphical-runbook"></a>Publicar um runbook gráfico

Cada runbook na automatização do Azure tem um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações feitas à versão de rascunho. Quando a versão de rascunho estiver pronta para ser disponíveis, em seguida, publicar, que substitui a versão publicada com a versão de rascunho.

Pode publicar um runbook gráfico abrindo o runbook para edição e, em seguida, clicar no **publicar** botão.

Quando um runbook ainda não foi publicado, ele tem o estado **New**. Quando é publicado, ele tem o estado **publicado**. Se editar o runbook depois foi publicado e as versões de rascunho e a publicada são diferentes, o runbook tem o estado **na edição**.

![Estados de Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Tem também a opção de reverter para a versão publicada do runbook. Isso gerará distância todas as alterações efetuadas uma vez que o runbook foi publicado pela última vez e substitui a versão de rascunho do runbook com a versão publicada.

## <a name="activities"></a>Actividades

Atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet do PowerShell, um runbook subordinado ou uma atividade de fluxo de trabalho. Adicionar uma atividade ao runbook ao clicar com botão direito-lo no controlo da biblioteca e selecionar **adicionar à tela**. Pode, em seguida, clique e arraste a atividade para colocá-lo em qualquer local na tela que desejar. A localização da atividade na tela não afetam o funcionamento do runbook de qualquer forma. Pode dispor o runbook no entanto achar mais adequado visualizar a sua operação.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selecione a atividade na tela para configurar suas propriedades e parâmetros no painel de configuração. Pode alterar o **etiqueta** da atividade para algo que é descritiva para. O cmdlet original ainda está a ser executado, está alterando simplesmente seu nome de exibição, que é utilizado no editor gráfico. A etiqueta tem de ser exclusiva dentro do runbook.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam os valores para um cmdlet específico. Todos os cmdlets ter, pelo menos, um parâmetro definido, e algumas possuem vários. Se um cmdlet tem vários conjuntos de parâmetros, em seguida, tem de selecionar qual delas utilizar antes de poder configurar parâmetros. Os parâmetros que pode configurar depende do conjunto de parâmetros que escolher. Pode alterar o conjunto de parâmetros usado por uma atividade selecionando **conjunto de parâmetros** e selecionar outro conjunto. Neste caso, os valores de parâmetro que configurou serão perdidos.

No exemplo a seguir, o cmdlet Get-AzureRmVM tem três conjuntos de parâmetros. Não é possível configurar os valores de parâmetro até que selecione um dos conjuntos de parâmetros. O conjunto de parâmetros ListVirtualMachineInResourceGroupParamSet é para retornar todas as máquinas virtuais num grupo de recursos e possui um único parâmetro opcional. O **GetVirtualMachineInResourceGroupParamSet** é para especificar a máquina virtual que desejo retornar e tem dois obrigatória e um parâmetro opcional.

![Conjunto de parâmetros](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetros

Quando especificar um valor para um parâmetro, selecione uma origem de dados para determinar como o valor for especificado. As origens de dados que estão disponíveis para um determinado parâmetro depende dos valores válidos para esse parâmetro. Por exemplo, Null não é uma opção disponível para um parâmetro que não permite valores nulos.

| Origem de Dados | Description |
|:--- |:--- |
| Valor constante |Escreva um valor para o parâmetro. Isso só está disponível para os seguintes tipos de dados: Int32, Int64, String,Boolean,DateTime,Switch. |
| Saída da atividade |Saída de uma atividade que precede a atividade atual no fluxo de trabalho. São listadas todas as atividades válidas. Selecione apenas a atividade para utilizar o respetivo resultado para o valor do parâmetro. Se a atividade produz um objeto com várias propriedades, em seguida, pode digitar o nome da propriedade depois de selecionar a atividade. |
| Entrada do Runbook |Selecione um parâmetro de entrada do runbook como entrada para o parâmetro de atividade. |
| Recurso de variável |Selecione uma variável de automatização como entrada. |
| Recurso de credencial |Selecione uma credencial da automatização como entrada. |
| Recurso de certificado |Selecione um certificado da automatização como entrada. |
| Recurso de ligação |Selecione uma ligação da automatização como entrada. |
| Expressão do PowerShell |Especificar simples [expressão do PowerShell](#powershell-expressions). A expressão é avaliada antes da atividade e o resultado utilizado para o valor do parâmetro. Pode utilizar variáveis para fazer referência a saída de uma atividade ou um parâmetro de entrada do runbook. |
| Não configurado |Limpa qualquer valor que foi configurada anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção para fornecer parâmetros adicionais. Estes são os parâmetros comuns do PowerShell ou outros parâmetros personalizados. É apresentada uma caixa de texto em que pode fornecer parâmetros usando a sintaxe do PowerShell. Por exemplo, para utilizar o **verboso** parâmetro comum, tem de especificar **"-Verbose: $True"** .

### <a name="retry-activity"></a>Repita a atividade

**Comportamento de tentativas** permite que uma atividade a ser executado várias vezes até que uma determinada condição seja cumprida, bem como um loop. Pode utilizar esta funcionalidade para as atividades que deve ser executado várias vezes, são suscetíveis a erros e pode precisar de mais do que um tentar para o sucesso ou testar as informações de saída da atividade para dados válidos.

Ao ativar a repetição de uma atividade, pode definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o runbook tem de aguardar antes da execução da atividade novamente. Se não for especificado nenhum atraso, em seguida, a atividade será executado novamente imediatamente depois de terminar.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de repetição é uma expressão do PowerShell que é avaliada após cada vez que a atividade é executada. Se a expressão for resolvida como verdadeira, em seguida, a atividade é executada novamente. Se a expressão for resolvida como False, em seguida, a atividade não é executado novamente e o runbook passa para a atividade seguinte.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de repetição pode utilizar uma variável chamada $RetryData que fornece acesso a informações sobre as repetições de atividade. Esta variável tem as propriedades na tabela a seguir:

| Propriedade | Description |
|:--- |:--- |
| NumberOfAttempts |Número de vezes que a atividade tiver sido executada. |
| Output |Resultado da última execução da atividade. |
| TotalDuration |Excedeu o tempo limite decorridos desde que a atividade foi iniciada pela primeira vez. |
| StartedAt |Hora em formato UTC, que a atividade foi iniciada pela primeira vez. |

Seguem-se exemplos de atividade Repita condições.

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

Depois de configurar uma condição de repetição para uma atividade, a atividade inclui dois indicações visuais lembrá-lo. Uma é apresentada na atividade e o outro é quando analisar a configuração da atividade.

![Indicadores visuais de repetição de atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de Script de fluxo de trabalho

Um controle de código é uma atividade especial que aceita o script do PowerShell ou o fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico a ser criado para fornecer funcionalidade que poderão caso contrário, não estar disponível. Ele não pode aceitar parâmetros, mas ele pode usar variáveis atividade saída e o runbook para parâmetros de entrada. Qualquer saída da atividade é adicionada no barramento de dados, exceto não se tiver nenhuma ligação de saída caso em que é adicionada à saída do runbook.

Por exemplo, o código a seguir executa os cálculos de data com uma variável de entrada do runbook chamada $NumberOfDays. Em seguida, envia uma hora de data calculada como saída a ser usado por atividades subsequentes no runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Ligações e fluxo de trabalho

R **link** num runbook gráfico liga-se duas atividades. É apresentado na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta para a atividade de destino iniciar depois de concluir a atividade de origem.

### <a name="create-a-link"></a>Criar uma ligação

Crie uma ligação entre duas atividades, selecionando a atividade de origem e clicar no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e a versão.

![Criar uma ligação](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selecione a ligação para configurar as respetivas propriedades no painel de configuração. Isto inclui o tipo de ligação, o que é descrito na tabela a seguir:

| Tipo de ligação | Description |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada objeto de saída da atividade de origem. A atividade de destino não é executado se a atividade de origem resulta em nenhuma saída. Saída da atividade de origem está disponível como um objeto. |
| Sequência |A atividade de destino é executada apenas uma vez. Ele recebe uma matriz de objetos da atividade de origem. Saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="starting-activity"></a>Atividade de partida

Inicia um runbook gráfico com quaisquer atividades que não têm uma ligação de entrada. Geralmente essa é apenas uma atividade, o que seria atuar como a atividade de partida para o runbook. Se várias atividades não tiver uma ligação de entrada, em seguida, o runbook é iniciado, executá-los em paralelo. Ele segue as ligações para executar outras atividades que cada um é concluída.

### <a name="conditions"></a>Condições

Quando especificar uma condição de uma ligação, a atividade de destino apenas é executada se a condição for resolvida como verdadeira. Normalmente usar uma variável de $ActivityOutput numa condição para recuperar a saída da atividade de origem

Para uma ligação de pipeline, especifique uma condição para um único objeto, e a condição é avaliada para cada objeto de saída, a atividade de origem. A atividade de destino, em seguida, é executada para cada objeto que satisfaça a condição. Por exemplo, com uma atividade de origem de Get-AzureRmVm, a seguinte sintaxe poderia ser usada para uma ligação de pipeline condicional para obter apenas as máquinas virtuais no grupo de recursos com o nome *Group1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para obter uma ligação de sequência, a condição é avaliada apenas uma vez, uma vez que uma única matriz é retornada que contém toda a saída objetos da atividade de origem. Por este motivo, um link de sequência não pode ser utilizado para filtrar, como uma ligação de pipeline mas simplesmente irá determinar se é ou não a próxima atividade é executada. Considere o seguinte conjunto de atividades, por exemplo no nosso runbook iniciar VM.

![Ligação condicional com seqüências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Existem três ligações de sequência de diferentes que estão a procurar valores foram fornecidos a dois parâmetros de entrada do runbook que representa o nome da VM e o nome do grupo de recursos para determinar o que é a ação apropriada para tirar - iniciar uma única VM, iniciar todas as VMs no recurso grupo ou todas as VMs numa subscrição. Para a ligação de sequência entre ligar ao Azure e Get única VM, esta é a lógica de condição:

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

Quando utiliza uma ligação condicional, os dados disponíveis da atividade de origem para outras atividades nessa sucursal são filtrados pela condição. Se uma atividade é a origem para várias ligações, os dados disponíveis para atividades em cada ramo dependem a condição na ligação de conexão com essa ramificação.

Por exemplo, o **Start-AzureRmVm** atividade no runbook abaixo inicia todas as máquinas virtuais. Ele tem duas ligações condicionais. Na primeira ligação condicional usa a expressão *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* para filtrar se a atividade de Start-AzureRmVm foi concluída com êxito. A segunda usa a expressão *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* para filtrar se a atividade de Start-AzureRmVm Falha ao iniciar a máquina virtual.

![Exemplo de ligação condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que segue a primeira ligação e utiliza a saída da atividade de Get-AzureVM obterá apenas as máquinas virtuais que foram iniciadas no momento em que Get-AzureVM foi executado. Qualquer atividade que segue o segundo link obtém apenas as máquinas virtuais que foram interrompidas no momento em que Get-AzureVM foi executado. Qualquer atividade que o terceiro link a seguir obtém todas as máquinas virtuais, independentemente do seu estado em execução.

### <a name="junctions"></a>Junções

Uma junção é uma atividade especial que aguarda até que todos os ramos de entrada foram concluídas. Isto permite-lhe executar várias atividades em paralelo e certifique-se de que tudo tenha concluído antes de continuar.

Embora uma junção possa ter um número ilimitado de ligações de entrada, não mais do que um dessas ligações pode ser um pipeline. O número de ligações de seqüência de entrada não é restrito. Pode criar a junção com várias ligações entrada do pipeline e guarde o runbook, mas falhar quando for executada.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais durante a transferência em simultâneo patches a aplicar a essas máquinas. Uma junção é utilizada para se certificar de que ambos os processos sejam concluídos antes do runbook continua.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciclos

Um ciclo é quando um links de atividade de destino para sua atividade de origem ou de outra atividade que, eventualmente, vincula de volta para a respetiva origem. Ciclos não são permitidos atualmente na criação de gráficos. Se o runbook tem um ciclo, ele salva corretamente, mas recebe um erro quando é executada.

![Ciclo de](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Partilhar dados entre atividades

Todos os dados que é a saída por uma atividade com uma ligação de saída são escritos para o *barramento de dados* para o runbook. Qualquer atividade no runbook pode utilizar dados sobre o barramento de dados para preencher os valores de parâmetros ou inclua no código de script. Uma atividade pode aceder à saída de qualquer atividade anterior no fluxo de trabalho.

Como os dados são escritos no barramento de dados dependem do tipo de ligação na atividade. Para uma **pipeline**, os dados são o resultado como objetos de múltiplos. Para uma **sequência** é de ligação, os dados de saída como uma matriz. Se houver apenas um valor, é o resultado como uma matriz de elemento único.

Pode aceder a dados no barramento de dados através de um dos dois métodos. Em primeiro lugar está a utilizar um **saída da atividade** origem de dados para preencher um parâmetro de outra atividade. Se o resultado é um objeto, pode especificar uma única propriedade.

![Saída da atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Também pode obter o resultado de uma atividade num **expressão do PowerShell** origem de dados ou a partir de um **Script de fluxo de trabalho** atividade com uma variável de ActivityOutput. Se o resultado é um objeto, pode especificar uma única propriedade. Variáveis de ActivityOutput utilizam a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Pontos de verificação

Pode definir [pontos de verificação](automation-powershell-workflow.md#checkpoints) de um runbook de fluxo de trabalho de PowerShell gráfico selecionando *runbook do ponto de verificação* qualquer atividade de início. Isso faz com que um ponto de verificação ser definida depois da atividade é executada.

![Check Point](media/automation-graphical-authoring-intro/set-checkpoint.png)

Pontos de verificação só estão ativados em runbooks do fluxo de trabalho de PowerShell gráfico, não está disponível em runbooks gráficos. Se o runbook utiliza cmdlets do Azure, deve seguir qualquer atividade foi efetuada a verificação com um Connect-AzureRmAccount no caso do runbook está suspenso e reinicia partir deste ponto de verificação numa função de trabalho diferente.

## <a name="authenticating-to-azure-resources"></a>Autenticação aos recursos do Azure

Os Runbooks na automatização do Azure que gerir recursos do Azure exigir a autenticação para o Azure. O [conta Run As](automation-create-runas-account.md) (também referido como um principal de serviço) é o método predefinido para aceder aos recursos do Azure Resource Manager na sua subscrição com runbooks de automatização. Pode adicionar esta funcionalidade para um runbook gráfico, adicionando a **AzureRunAsConnection** recurso de ligação, que está a utilizar o PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet, e [ Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet para a tela. Isso é ilustrado no exemplo a seguir:

![Executar como atividades de autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A atividade obter ligação Run As (ou seja, Get-AutomationConnection), está configurado com uma origem de dados de valor constante denominada AzureRunAsConnection.

![Executar como configuração da ligação](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A atividade seguinte, Connect-AzureRmAccount, adiciona a conta autenticada Run As para utilização no runbook.

![Conjunto de parâmetros do Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Quando pesquisar a sua biblioteca de itens, se não vir **Connect-AzureRMAccount**, pode utilizar **Add-AzureRmAccount**, ou pode atualizar os módulos na conta de automatização.

Para os parâmetros **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, e **TENANTID** tem de especificar o nome da propriedade para o caminho do campo porque a atividade produz um objeto com várias propriedades. Caso contrário, quando executar o runbook, falhar tentar autenticar. Este é o que precisa no mínimo, para autenticar o runbook com a conta Run As.

Para manter versões anteriores a compatibilidade para os assinantes que tiverem criado uma conta de automatização com um [conta de utilizador do Azure AD](automation-create-aduser-account.md) para gerir a implementação clássica do Azure ou para os recursos do Azure Resource Manager, é o método para autenticar o Adicionar-AzureAccount cmdlet com um [recurso de credencial](automation-credentials.md) que representa um utilizador do Active Directory com acesso à conta do Azure.

Pode adicionar esta funcionalidade para um runbook gráfico, adicionando um recurso de credencial para a tela seguida de uma atividade de Add-AzureAccount. Adicionar-AzureAccount utiliza a atividade de credencial para a respetiva entrada. Isso é ilustrado no exemplo a seguir:

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

Tem de autenticar no início do runbook e depois de cada ponto de verificação. Isso significa que a adição de uma atividade de adição Add-AzureAccount após qualquer atividade Checkpoint-Workflow. Não é necessário uma atividade de credencial de adição, uma vez que pode utilizar o mesmo

![Saída da atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook de entrada e saída

### <a name="runbook-input"></a>Entrada do Runbook

Um runbook pode exigir a entrada a partir de um utilizador durante o arranque do runbook através do portal do Azure ou de outro runbook se a atual é utilizada como um filho.
Por exemplo, se tiver um runbook que cria uma máquina virtual, terá de fornecer informações tais como o nome de máquina virtual e outras propriedades sempre que iniciar o runbook.

Aceitar entrada de um runbook com a definição de um ou mais parâmetros de entrada. Fornecer valores para estes parâmetros sempre que o runbook é iniciado. Quando inicia um runbook com o portal do Azure, pede-lhe para fornecer valores para cada um dos parâmetros de entrada do runbook.

Pode acessar parâmetros de entrada para um runbook clicando a **de entrada e saída** botão na barra de ferramentas do runbook.

Esta ação abre o **de entrada e saída** controle onde pode editar um parâmetro de entrada existente ou crie um novo ao clicar em **Adicionar entrada de**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir:

| Propriedade | Description |
|:--- |:--- |
| Name |O nome exclusivo do parâmetro. Isto só pode conter carateres alfanuméricos e não pode conter um espaço. |
| Description |Uma descrição opcional para o parâmetro de entrada. |
| Type |Tipo de dados esperado para o valor do parâmetro. O portal do Azure fornece um controlo apropriado para o tipo de dados para cada parâmetro ao pedir entrada. |
| Mandatory |Especifica se deve ser fornecido um valor para o parâmetro. Não é possível iniciar o runbook se não fornecer um valor para cada parâmetro obrigatório que não tem um valor predefinido especificado. |
| Default Value |Especifica qual valor é utilizado para o parâmetro se não for fornecido. Isso pode ser Null ou um valor específico. |

### <a name="runbook-output"></a>Resultado do runbook

Criado por qualquer atividade que não tem uma ligação de saída de dados são guardados para o [saída do runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A saída é guardada com a tarefa de runbook e está disponível para um runbook principal quando o runbook é utilizado como um filho.

## <a name="powershell-expressions"></a>Expressões de PowerShell

Uma das vantagens da criação de gráficos é fornecer-lhe a capacidade de criar um runbook com o mínimo de conhecimento do PowerShell. Atualmente, precisa saber um pouco do PowerShell, embora para preencher determinados [valores de parâmetros](#activities) e para definição [ligação condições](#links-and-workflow). Esta seção fornece uma introdução rápida para expressões de PowerShell para os utilizadores que talvez não esteja familiarizados com ele. Todos os detalhes do PowerShell estão disponíveis em [Scripting com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Origem de dados de expressão do PowerShell
Pode utilizar uma expressão do PowerShell como uma origem de dados para preencher o valor de uma [parâmetro de atividade](#activities) com os resultados de algum código do PowerShell. Isto pode ser uma única linha de código que execute alguma função simple ou várias linhas que realizar alguma lógica complexa. Qualquer saída de um comando que não está atribuído a uma variável é a saída para o valor do parâmetro.

Por exemplo, o seguinte comando faria de saída a data atual.

Por exemplo, o seguinte comando faria de saída a data atual.

```powershell-interactive
Get-Date
```

Os seguintes comandos cria uma seqüência de caracteres a contar da data atual e atribua-a uma variável. O conteúdo da variável, em seguida, é enviado para a saída

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os seguintes comandos avalie a data atual e retornam uma cadeia de caracteres que indica se o dia atual é um fim de semana ou dia da semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Saída da atividade

Para utilizar a saída de uma atividade anterior no runbook, utilize a variável $ActivityOutput com a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, pode ter uma atividade com uma propriedade que requer o nome de uma máquina virtual caso em que poderia usar a seguinte expressão:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Se a propriedade que exigiam a máquina virtual de objeto, em vez de apenas uma propriedade, retornará a todo o objeto com a seguinte sintaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Também pode utilizar a saída de uma atividade numa expressão mais complexa, como o seguinte que concatena o texto para o nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Condições

Uso [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação retorna um valor de $true ou $false.

Por exemplo, a seguinte condição determina se a máquina virtual de uma atividade com o nome *Get-AzureVM* está atualmente *parado*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

As seguintes verificações de condição, se a mesma máquina virtual está em qualquer Estado diferente de *parado*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Pode participar em várias condições com um [operador lógico](https://technet.microsoft.com/library/hh847789.aspx) como **- e** ou **- ou**. Por exemplo, a seguinte condição verifica se a mesma máquina virtual no exemplo anterior está num Estado de *parado* ou *parar*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelas hash

[Tabelas hash](https://technet.microsoft.com/library/hh847780.aspx) são pares de nome/valor que são úteis para retornar um conjunto de valores. Propriedades para determinadas atividades podem esperar que uma tabela de hash, em vez de um valor simples. Também poderá ver como a hashtable referido como um dicionário.

Criar uma tabela de hash com a seguinte sintaxe. Uma tabela de hash pode conter qualquer número de entradas, mas cada um é definido por um nome e valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria uma tabela de hash a ser utilizado na origem de dados para um parâmetro de atividade que poderá demorar uma tabela de hash com valores para uma pesquisa na internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo seguinte utiliza o resultado de uma atividade chamado *obter ligação do Twitter* para popular uma tabela de hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Próximos Passos

* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para compreender como autenticar com a conta Run As de automatização, veja [configurar conta Run as Azure](automation-sec-configure-azure-runas-account.md)
