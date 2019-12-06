---
title: Criação gráfica na automação do Azure
description: A criação gráfica permite que você crie runbooks para a automação do Azure sem trabalhar com código. Este artigo fornece uma introdução à criação gráfica e todos os detalhes necessários para começar a criar um runbook gráfico.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82a06510bd9d1e0de2b38260773cb4848156bf12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850300"
---
# <a name="graphical-authoring-in-azure-automation"></a>Criação gráfica na automação do Azure

A criação gráfica permite que você crie runbooks para a automação do Azure sem as complexidades do código subjacente de fluxo de trabalho do Windows PowerShell ou do PowerShell. Você adiciona atividades à tela por meio de uma biblioteca de cmdlets e runbooks, vincula-as em conjunto e configura para formar um fluxo de trabalho. Se você já trabalhou com o System Center Orchestrator ou o Service Management Automation (SMA), isso deve parecer familiar para você

Este artigo fornece uma introdução à criação gráfica e os conceitos que você precisa para começar a criar um runbook gráfico.

## <a name="graphical-runbooks"></a>Runbooks gráficos

Todos os runbooks na automação do Azure são fluxos de trabalho do Windows PowerShell. Runbooks de fluxo de trabalho gráfico e gráfico do PowerShell geram código do PowerShell que é executado pelos trabalhos de automação, mas você não pode exibi-lo ou modificá-lo diretamente. Um runbook gráfico pode ser convertido em um runbook de fluxo de trabalho do PowerShell gráfico e vice-versa, mas não podem ser convertidos em um runbook textual. Um runbook textual existente não pode ser importado para o editor gráfico.

## <a name="overview-of-graphical-editor"></a>Visão geral do editor gráfico

Você pode abrir o editor gráfico no portal do Azure criando ou editando um runbook gráfico.

![Espaço de trabalho gráfico](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As seções a seguir descrevem os controles no editor gráfico.

### <a name="canvas"></a>Tela

A tela é onde você cria o runbook. Você adiciona atividades dos nós no controle de biblioteca ao runbook e as conecta com links para definir a lógica do runbook.

Você pode usar os controles na parte inferior da tela para ampliar e reduzir.

### <a name="library-control"></a>Controle de biblioteca

O controle biblioteca é onde você seleciona [as atividades](#activities) a serem adicionadas ao seu runbook. Você os adiciona à tela na qual você os conecta a outras atividades. Ele inclui quatro seções descritas na tabela a seguir:

| Section | Descrição |
|:--- |:--- |
| Cmdlets |Inclui todos os cmdlets que podem ser usados em seu runbook. Os cmdlets são organizados por módulo. Todos os módulos que você instalou em sua conta de automação estão disponíveis. |
| Runbooks |Inclui os runbooks em sua conta de automação. Esses runbooks podem ser adicionados à tela para serem usados como runbooks filho. São mostrados apenas runbooks do mesmo tipo de núcleo do runbook que está sendo editado; para runbooks gráficos somente runbooks baseados no PowerShell são mostrados, enquanto para runbooks de fluxo de trabalho gráfico do PowerShell somente runbooks baseados no fluxo de trabalho do PowerShell são mostrados. |
| Elementos |Inclui os [ativos de automação](/previous-versions/azure/dn939988(v=azure.100)) em sua conta de automação que podem ser usados em seu runbook. Quando você adiciona um ativo a um runbook, ele adiciona uma atividade de fluxo de trabalho que obtém o ativo selecionado. No caso de ativos variáveis, você pode selecionar se deseja adicionar uma atividade para obter a variável ou definir a variável. |
| Controle de runbook |Inclui atividades de controle de runbook que podem ser usadas em seu runbook atual. Uma *junção* usa várias entradas e aguarda até que todas tenham sido concluídas antes de continuar o fluxo de trabalho. Uma atividade de *código* executa uma ou mais linhas de código do PowerShell ou do fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico. Você pode usar essa atividade para código personalizado ou para a funcionalidade que é difícil de alcançar com outras atividades. |

### <a name="configuration-control"></a>Controle de configuração

O controle de configuração é onde você fornece detalhes para um objeto selecionado na tela. As propriedades disponíveis neste controle dependem do tipo de objeto selecionado. Quando você seleciona uma opção no controle de configuração, ela abre lâminas adicionais para fornecer informações adicionais.

### <a name="test-control"></a>Controle de teste

O controle de teste não é exibido quando o editor gráfico é iniciado pela primeira vez. Ele é aberto quando você testa interativamente [um runbook gráfico](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Procedimentos gráficos de runbook

### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportando e importando um runbook gráfico

Você só pode exportar a versão publicada de um runbook gráfico. Se o runbook ainda não tiver sido publicado, o botão **Exportar** será desabilitado. Quando você clica no botão **Exportar** , o runbook é baixado em seu computador local. O nome do arquivo corresponde ao nome do runbook com uma extensão *graphrunbook* .

Você pode importar um arquivo de runbook de fluxo de trabalho gráfico ou gráfico do PowerShell selecionando a opção **importar** ao adicionar um runbook. Ao selecionar o arquivo a ser importado, você pode manter o mesmo **nome** ou fornecer um novo. O campo tipo de runbook exibirá o tipo de runbook depois de avaliar o arquivo selecionado e se você tentar selecionar um tipo diferente que não está correto, uma mensagem será apresentada observando que há conflitos potenciais e, durante a conversão, pode haver sintaxe los.

![Importar runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testando um runbook gráfico

Você pode testar a versão de rascunho de um runbook no portal do Azure enquanto deixa a versão publicada do runbook inalterada ou pode testar um novo runbook antes que ele seja publicado. Isto permite-lhe verificar se o runbook está a funcionar corretamente antes de substituir a versão publicada. Quando testa um runbook, o runbook de rascunho é executado e quaisquer ações que executar são concluídas. Nenhum histórico de trabalho é criado, mas a saída é exibida no painel saída de teste.

Abra o controle de teste para um runbook abrindo o runbook para editar e, em seguida, clique no botão do **painel de teste** .

O controle de teste solicita quaisquer parâmetros de entrada e você pode iniciar o runbook clicando no botão **Iniciar** .

### <a name="publishing-a-graphical-runbook"></a>Publicando um runbook gráfico

Cada runbook na automação do Azure tem um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações feitas à versão de rascunho. Quando a versão de rascunho estiver pronta para ser disponibilizada, você a publicará, o que substitui a versão publicada pela versão de rascunho.

Você pode publicar um runbook gráfico abrindo o runbook para edição e clicando no botão **publicar** .

Quando um runbook ainda não foi publicado, ele tem um status **novo**. Quando é publicado, ele tem um status de **publicado**. Se você editar o runbook depois que ele tiver sido publicado e o rascunho e as versões publicadas forem diferentes, o runbook terá o status **em Editar**.

![Status do runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Você também tem a opção de reverter para a versão publicada de um runbook. Isso gera as alterações feitas desde que o runbook foi publicado pela última vez e substitui a versão de rascunho do runbook pela versão publicada.

## <a name="activities"></a>Atividades

As atividades são os blocos de construção de um runbook. Uma atividade pode ser um cmdlet do PowerShell, um runbook filho ou uma atividade de fluxo de trabalho. Você adiciona uma atividade ao runbook clicando com o botão direito do mouse no controle de biblioteca e selecionando **Adicionar à tela**. Em seguida, você pode clicar e arrastar a atividade para colocá-la em qualquer lugar na tela que desejar. O local da atividade na tela não afeta a operação do runbook de forma alguma. Você pode definir o layout de seu runbook no entanto, você o acha mais adequado para visualizar sua operação.

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selecione a atividade na tela para configurar suas propriedades e parâmetros na folha configuração. Você pode alterar o **rótulo** da atividade para algo que seja descritivo para você. O cmdlet original ainda está sendo executado. você está simplesmente alterando seu nome de exibição que é usado no editor gráfico. O rótulo deve ser exclusivo dentro do runbook.

### <a name="parameter-sets"></a>Conjuntos de parâmetros

Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que aceitam valores para um cmdlet específico. Todos os cmdlets têm pelo menos um conjunto de parâmetros e alguns têm vários. Se um cmdlet tiver vários conjuntos de parâmetros, você deverá selecionar qual deles usar para poder configurar parâmetros. Os parâmetros que você pode configurar dependem do conjunto de parâmetros que você escolher. Você pode alterar o conjunto de parâmetros usado por uma atividade selecionando **conjunto de parâmetros** e selecionando outro conjunto. Nesse caso, todos os valores de parâmetro que você configurou serão perdidos.

No exemplo a seguir, o cmdlet Get-AzureRmVM tem três conjuntos de parâmetros. Você não pode configurar valores de parâmetro até selecionar um dos conjuntos de parâmetros. O conjunto de parâmetros ListVirtualMachineInResourceGroupParamSet é para retornar todas as máquinas virtuais em um grupo de recursos e tem um único parâmetro opcional. O **GetVirtualMachineInResourceGroupParamSet** é para especificar a máquina virtual que você deseja retornar e tem dois parâmetros obrigatórios e um opcional.

![Conjunto de parâmetros](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetros

Ao especificar um valor para um parâmetro, você seleciona uma fonte de dados para determinar como o valor é especificado. As fontes de dados que estão disponíveis para um determinado parâmetro dependem dos valores válidos para esse parâmetro. Por exemplo, NULL não é uma opção disponível para um parâmetro que não permite valores nulos.

| Origem de Dados | Descrição |
|:--- |:--- |
| Valor constante |Digite um valor para o parâmetro. Isso só está disponível para os seguintes tipos de dados: Int32, Int64, String, Boolean, DateTime, switch. |
| Saída da atividade |Saída de uma atividade que precede a atividade atual no fluxo de trabalho. Todas as atividades válidas são listadas. Selecione apenas a atividade para usar sua saída para o valor do parâmetro. Se a atividade produzir um objeto com várias propriedades, você poderá digitar o nome da propriedade depois de selecionar a atividade. |
| Entrada de runbook |Selecione um parâmetro de entrada de runbook como entrada para o parâmetro de atividade. |
| Ativo variável |Selecione uma variável de automação como entrada. |
| Ativo de credencial |Selecione uma credencial de automação como entrada. |
| Ativo de certificado |Selecione um certificado de automação como entrada. |
| Ativo de conexão |Selecione uma conexão de automação como entrada. |
| Expressão do PowerShell |Especifique uma [expressão](#powershell-expressions)simples do PowerShell. A expressão é avaliada antes da atividade e do resultado usado para o valor do parâmetro. Você pode usar variáveis para se referir à saída de uma atividade ou a um parâmetro de entrada de runbook. |
| Não configurado |Limpa qualquer valor configurado anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais

Todos os cmdlets têm a opção de fornecer parâmetros adicionais. Esses são parâmetros comuns do PowerShell ou outros parâmetros personalizados. Você verá uma caixa de texto na qual é possível fornecer parâmetros usando a sintaxe do PowerShell. Por exemplo, para usar o parâmetro comum **Verbose** , você deve especificar **"-verbose: $true"** .

### <a name="retry-activity"></a>Atividade de repetição

O **comportamento de repetição** permite que uma atividade seja executada várias vezes até que uma determinada condição seja atendida, assim como um loop. Você pode usar esse recurso para atividades que devem ser executadas várias vezes, são propensos a erros e podem precisar de mais de uma tentativa de sucesso ou testar as informações de saída da atividade para obter dados válidos.

Ao habilitar a repetição de uma atividade, você pode definir um atraso e uma condição. O atraso é o tempo (medido em segundos ou minutos) que o runbook aguarda antes de executar a atividade novamente. Se nenhum atraso for especificado, a atividade será executada imediatamente após a conclusão.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de repetição é uma expressão do PowerShell que é avaliada após cada vez que a atividade é executada. Se a expressão for resolvida como true, a atividade será executada novamente. Se a expressão for resolvida como false, a atividade não será executada novamente e o runbook passará para a próxima atividade.

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de repetição pode usar uma variável chamada $RetryData que fornece acesso a informações sobre as tentativas de atividade. Essa variável tem as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| NumberOfAttempts |Número de vezes que a atividade foi executada. |
| Saída |Saída da última execução da atividade. |
| TotalDuration |Tempo decorrido desde que a atividade foi iniciada pela primeira vez. |
| StartedAt |Hora no formato UTC em que a atividade foi iniciada pela primeira vez. |

Veja a seguir exemplos de condições de repetição de atividade.

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

Depois de configurar uma condição de repetição para uma atividade, a atividade inclui duas indicações visuais para lembrá-lo. Uma é apresentada na atividade e a outra é quando você revisa a configuração da atividade.

![Indicadores visuais de repetição de atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controle de script de fluxo de trabalho

Um controle de código é uma atividade especial que aceita o script do PowerShell ou do fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico que está sendo criado para fornecer funcionalidade que, de outra forma, pode não estar disponível. Ele não pode aceitar parâmetros, mas pode usar variáveis para saída de atividade e parâmetros de entrada de runbook. Qualquer saída da atividade é adicionada às DataBus, a menos que não tenha nenhum link de saída nesse caso, ela é adicionada à saída do runbook.

Por exemplo, o código a seguir executa cálculos de data usando uma variável de entrada de runbook chamada $NumberOfDays. Em seguida, ele envia uma data e hora calculada como saída a ser usada pelas atividades subsequentes no runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Links e fluxo de trabalho

Um **link** em um runbook gráfico conecta duas atividades. Ele é exibido na tela como uma seta apontando da atividade de origem para a atividade de destino. As atividades são executadas na direção da seta com a atividade de destino iniciando após a conclusão da atividade de origem.

### <a name="create-a-link"></a>Criar um link

Crie um link entre duas atividades selecionando a atividade de origem e clicando no círculo na parte inferior da forma. Arraste a seta para a atividade de destino e a versão.

![Criar um link](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selecione o link para configurar suas propriedades na folha configuração. Isso inclui o tipo de link, que é descrito na tabela a seguir:

| Tipo de link | Descrição |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada saída de objeto da atividade de origem. A atividade de destino não será executada se a atividade de origem não resultar em nenhuma saída. A saída da atividade de origem está disponível como um objeto. |
| Sequence |A atividade de destino é executada apenas uma vez. Ele recebe uma matriz de objetos da atividade de origem. A saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="starting-activity"></a>Iniciando atividade

Um runbook gráfico começa com qualquer atividade que não tenha um link de entrada. Isso geralmente é apenas uma atividade, que atuaria como a atividade inicial para o runbook. Se várias atividades não tiverem um link de entrada, o runbook começará executando-as em paralelo. Ele segue os links para executar outras atividades à medida que cada uma é concluída.

### <a name="conditions"></a>Condições

Quando você especifica uma condição em um link, a atividade de destino só é executada se a condição for resolvida como true. Normalmente, você usa uma variável $ActivityOutput em uma condição para recuperar a saída da atividade de origem

Para um link de pipeline, você especifica uma condição para um único objeto e a condição é avaliada para cada saída de objeto pela atividade de origem. Em seguida, a atividade de destino é executada para cada objeto que satisfaz a condição. Por exemplo, com uma atividade de origem de Get-AzureRmVm, a sintaxe a seguir pode ser usada para um link de pipeline condicional para recuperar somente máquinas virtuais no grupo de recursos chamado *grupo1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Para um link de sequência, a condição é avaliada apenas uma vez, pois uma única matriz é retornada contendo todos os objetos de saída da atividade de origem. Por isso, um link de sequência não pode ser usado para filtragem como um link de pipeline, mas simplesmente determinará se a próxima atividade será executada ou não. Veja, por exemplo, o seguinte conjunto de atividades em nosso runbook de VM inicial.

![Link condicional com sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Há três links de sequência diferentes que estão verificando se os valores foram fornecidos a dois parâmetros de entrada de runbook que representam o nome da VM e o nome do grupo de recursos para determinar qual é a ação apropriada a ser executada em uma única VM, iniciar todas as VMs no recurso grupo ou todas as VMs em uma assinatura. Para o link de sequência entre conectar-se ao Azure e obter uma única VM, aqui está a lógica da condição:

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

Quando você usa um link condicional, os dados disponíveis da atividade de origem para outras atividades nessa ramificação são filtrados pela condição. Se uma atividade for a origem para vários links, os dados disponíveis para atividades em cada ramificação dependem da condição no link que se conecta a essa ramificação.

Por exemplo, a atividade **Start-AzureRmVm** no runbook abaixo inicia todas as máquinas virtuais. Ele tem dois links condicionais. O primeiro link condicional usa a expressão *$ActivityOutput [' Start-AzureRmVM ']. IsSuccessStatusCode-EQ $true* para filtrar se a atividade Start-AzureRmVm foi concluída com êxito. O segundo usa a expressão *$ActivityOutput [' Start-AzureRmVM ']. IsSuccessStatusCode-ne $true* para filtrar se a atividade Start-AzureRmVm falhou ao iniciar a máquina virtual.

![Exemplo de link condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que segue o primeiro link e usa a saída da atividade de Get-AzureVM só obterá as máquinas virtuais que foram iniciadas no momento em que o Get-AzureVM foi executado. Qualquer atividade que segue o segundo link só obtém as máquinas virtuais que foram interrompidas no momento em que Get-AzureVM foi executado. Qualquer atividade após o terceiro link obtém todas as máquinas virtuais, independentemente de seu estado de execução.

### <a name="junctions"></a>Junções

Uma junção é uma atividade especial que aguarda até que todas as ramificações de entrada sejam concluídas. Isso permite que você execute várias atividades em paralelo e certifique-se de que todas tenham sido concluídas antes de prosseguir.

Embora uma junção possa ter um número ilimitado de links de entrada, não mais de um desses links pode ser um pipeline. O número de links de sequência de entrada não é restrito. Você tem permissão para criar a junção com vários links de pipeline de entrada e salvar o runbook, mas ele falha quando é executado.

O exemplo a seguir faz parte de um runbook que inicia um conjunto de máquinas virtuais ao baixar simultaneamente os patches a serem aplicados a esses computadores. Uma junção é usada para garantir que ambos os processos sejam concluídos antes de o runbook continuar.

![Junction](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciclos

Um ciclo é quando uma atividade de destino é vinculada de volta à sua atividade de origem ou a outra atividade que, eventualmente, se vincula de volta à sua origem. Atualmente, os ciclos não são permitidos na criação gráfica. Se o runbook tiver um ciclo, ele será salvo corretamente, mas receberá um erro quando for executado.

![Cíclico](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Compartilhando dados entre atividades

Todos os dados que são gerados por uma atividade com um link de saída são gravados nas *DataBus* do runbook. Qualquer atividade no runbook pode usar dados em DataBus para popular valores de parâmetro ou incluir no código de script. Uma atividade pode acessar a saída de qualquer atividade anterior no fluxo de trabalho.

A forma como os dados são gravados nas DataBus depende do tipo de link na atividade. Para um **pipeline**, os dados são gerados como vários objetos. Para um link de **sequência** , os dados são gerados como uma matriz. Se houver apenas um valor, ele será apresentado como uma matriz de elemento único.

Você pode acessar dados nas DataBus usando um dos dois métodos. O primeiro é usar uma fonte de dados de **saída de atividade** para popular um parâmetro de outra atividade. Se a saída for um objeto, você poderá especificar uma única propriedade.

![Saída da atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Você também pode recuperar a saída de uma atividade em uma fonte de dados de **expressão do PowerShell** ou de uma atividade de script de **fluxo de trabalho** com uma variável ActivityOutput. Se a saída for um objeto, você poderá especificar uma única propriedade. As variáveis ActivityOutput usam a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Pontos de verificação

Você pode definir [pontos de verificação](automation-powershell-workflow.md#checkpoints) em um runbook de fluxo de trabalho gráfico do PowerShell selecionando *runbook de ponto de verificação* em qualquer atividade. Isso faz com que um ponto de verificação seja definido após a execução da atividade.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Os pontos de verificação são habilitados apenas em runbooks gráficos de fluxo de trabalho do PowerShell, não estão disponíveis em runbooks gráficos. Se o runbook usar cmdlets do Azure, você deverá seguir qualquer atividade de ponto de verificação com um Connect-AzureRmAccount, caso o runbook seja suspenso e reinicie a partir desse ponto de verificação em um trabalho diferente.

## <a name="authenticating-to-azure-resources"></a>Autenticando para recursos do Azure

Os Runbooks na automação do Azure que gerenciam os recursos do Azure exigem autenticação no Azure. A [conta Executar como](automation-create-runas-account.md) (também conhecida como entidade de serviço) é o método padrão para acessar Azure Resource Manager recursos em sua assinatura com Runbooks de automação. Você pode adicionar essa funcionalidade a um runbook gráfico adicionando o ativo de conexão **AzureRunAsConnection** , que está usando o cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) do PowerShell e o cmdlet [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) à tela. Isso é ilustrado no exemplo a seguir:

![Atividades de autenticação executar como](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A atividade obter conexão executar como (ou seja, Get-AutomationConnection) é configurada com uma fonte de dados de valor constante chamada AzureRunAsConnection.

![Configuração de conexão executar como](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A próxima atividade, Connect-AzureRmAccount, adiciona a conta Executar como autenticada para uso no runbook.

![Conjunto de parâmetros Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá atualizar seus módulos na sua conta de automação.

Para os parâmetros **APPLICATIONID**, **CERTIFICATETHUMBPRINT**e **tenantid** , você precisa especificar o nome da propriedade para o caminho do campo porque a atividade produz um objeto com várias propriedades. Caso contrário, quando você executar o runbook, ele não tentará se autenticar. Isso é o que você precisa no mínimo para autenticar seu runbook com a conta Executar como.

Para manter a compatibilidade com versões anteriores para assinantes que criaram uma conta de automação usando uma [conta de usuário do Azure ad](automation-create-aduser-account.md) para gerenciar a implantação clássica do Azure ou para Azure Resource Manager recursos, o método de autenticação é o cmdlet Add-AzureAccount com um [ativo de credencial](automation-credentials.md) que representa um usuário Active Directory com acesso à conta do Azure.

Você pode adicionar essa funcionalidade a um runbook gráfico adicionando um ativo de credencial à tela, seguido por uma atividade Add-AzureAccount. Add-AzureAccount usa a atividade de credencial para sua entrada. Isso é ilustrado no exemplo a seguir:

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

Você precisa autenticar no início do runbook e depois de cada ponto de verificação. Isso significa adicionar uma atividade Add-AzureAccount adicional após qualquer atividade de fluxo de trabalho. Você não precisa de uma atividade de credencial de adição, pois pode usar o mesmo

![Saída da atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Entrada e saída de runbook

### <a name="runbook-input"></a>Entrada de runbook

Um runbook pode exigir uma entrada de um usuário quando ele iniciar o runbook por meio da portal do Azure ou de outro runbook, se o atual for usado como um filho.
Por exemplo, se você tiver um runbook que cria uma máquina virtual, talvez seja necessário fornecer informações como o nome da máquina virtual e outras propriedades sempre que iniciar o runbook.

Você aceita a entrada de um runbook definindo um ou mais parâmetros de entrada. Você fornece valores para esses parâmetros cada vez que o runbook é iniciado. Quando você inicia um runbook com o portal do Azure, ele solicita que você forneça valores para cada um dos parâmetros de entrada do runbook.

Você pode acessar os parâmetros de entrada para um runbook clicando no botão **entrada e saída** na barra de ferramentas do runbook.

Isso abre o controle **entrada e saída** , onde você pode editar um parâmetro de entrada existente ou criar um novo clicando em **Adicionar entrada**.

![Adicionar entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| Nome |O nome exclusivo do parâmetro. Isso pode conter apenas caracteres alfanuméricos e não pode conter um espaço. |
| Descrição |Uma descrição opcional para o parâmetro de entrada. |
| Tipo |Tipo de dados esperado para o valor do parâmetro. O portal do Azure fornece um controle apropriado para o tipo de dados para cada parâmetro ao solicitar entrada. |
| Obrigatório |Especifica se um valor deve ser fornecido para o parâmetro. O runbook não poderá ser iniciado se você não fornecer um valor para cada parâmetro obrigatório que não tenha um valor padrão definido. |
| Default Value |Especifica qual valor será usado para o parâmetro se um não for fornecido. Isso pode ser nulo ou um valor específico. |

### <a name="runbook-output"></a>Resultado do runbook

Os dados criados por qualquer atividade que não tenha um link de saída são salvos na [saída do runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A saída é salva com o trabalho de runbook e está disponível para um runbook pai quando o runbook é usado como um filho.

## <a name="powershell-expressions"></a>Expressões do PowerShell

Uma das vantagens da criação gráfica é fornecer a capacidade de criar um runbook com o mínimo de conhecimento do PowerShell. Atualmente, você precisa saber um pouco do PowerShell para preencher determinados [valores de parâmetro](#activities) e definir [condições de vínculo](#links-and-workflow). Esta seção fornece uma breve introdução às expressões do PowerShell para os usuários que talvez não estejam familiarizados com ele. Os detalhes completos do PowerShell estão disponíveis em [scripts com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Fonte de dados de expressão do PowerShell
Você pode usar uma expressão do PowerShell como uma fonte de dados para popular o valor de um [parâmetro de atividade](#activities) com os resultados de algum código do PowerShell. Isso pode ser uma única linha de código que executa uma função simples ou várias linhas que executam uma lógica complexa. Qualquer saída de um comando que não esteja atribuído a uma variável é saída para o valor do parâmetro.

Por exemplo, o comando a seguir produziria a data atual.

Por exemplo, o comando a seguir produziria a data atual.

```powershell-interactive
Get-Date
```

Os comandos a seguir criam uma cadeia de caracteres a partir da data atual e a atribuem a uma variável. O conteúdo da variável é enviado para a saída

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Os comandos a seguir avaliam a data atual e retornam uma cadeia de caracteres que indica se o dia atual é um fim de semana ou dia da semana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Saída da atividade

Para usar a saída de uma atividade anterior no runbook, use a variável $ActivityOutput com a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Por exemplo, você pode ter uma atividade com uma propriedade que requer o nome de uma máquina virtual, caso em que você poderia usar a seguinte expressão:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Se a propriedade que exigia o objeto de máquina virtual, em vez de apenas uma propriedade, você retornaria o objeto inteiro usando a sintaxe a seguir.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Você também pode usar a saída de uma atividade em uma expressão mais complexa, como a seguinte, que concatena o texto ao nome da máquina virtual.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Condições

Use [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou determinar se um valor corresponde a um padrão especificado. Uma comparação retorna um valor de $true ou $false.

Por exemplo, a condição a seguir determina se a máquina virtual de uma atividade denominada *Get-AzureVM* está atualmente *parada*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A condição a seguir verifica se a mesma máquina virtual está em qualquer estado diferente de *parado*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Você pode unir várias condições usando um [operador lógico](https://technet.microsoft.com/library/hh847789.aspx) , como **-e** ou **-ou**. Por exemplo, a condição a seguir verifica se a mesma máquina virtual no exemplo anterior está em um estado de *parado* ou *parando*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hash

As [Hashtables](https://technet.microsoft.com/library/hh847780.aspx) são pares de nome/valor que são úteis para retornar um conjunto de valores. As propriedades de determinadas atividades podem esperar uma tabela de hash em vez de um valor simples. Você também pode ver como uma tabela de hash referenciada como um dicionário.

Você cria uma tabela de hash com a sintaxe a seguir. Uma tabela de hash pode conter qualquer número de entradas, mas cada uma é definida por um nome e um valor.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Por exemplo, a expressão a seguir cria uma tabela de hash a ser usada na fonte de dados para um parâmetro de atividade que esperava uma tabela de hash com valores para uma pesquisa na Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

O exemplo a seguir usa a saída de uma atividade chamada *obter conexão do Twitter* para preencher uma tabela de hash.

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
* Para entender como autenticar usando a conta Executar como de automação, consulte [configurar conta Executar como do Azure](automation-sec-configure-azure-runas-account.md)
