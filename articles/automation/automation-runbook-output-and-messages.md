---
title: Configurar saídas de runbook e streams de mensagens
description: Este artigo diz como implementar a lógica de manipulação de erros e descreve streams de saída e mensagens nos runbooks da Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 033900ddd0bd19332b4a9a996c68b3b187d631c4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833567"
---
# <a name="configure-runbook-output-and-message-streams"></a>Configurar saídas de runbook e streams de mensagens

A maioria dos runbooks da Azure Automation têm alguma forma de saída. Esta saída pode ser uma mensagem de erro para o utilizador ou um objeto complexo destinado a ser utilizado com outro livro de aplicação. O Windows PowerShell fornece [vários streams](/powershell/module/microsoft.powershell.core/about/about_redirection) para enviar saída a partir de um script ou fluxo de trabalho. A Azure Automation trabalha com cada um destes fluxos de forma diferente. Deve seguir as melhores práticas para usar os streams quando estiver a criar um livro de corridas.

A tabela seguinte descreve brevemente cada fluxo com o seu comportamento no portal Azure para os livros de ensaios publicados e durante [o teste de um livro de bordo](./manage-runbooks.md). O fluxo de saída é o fluxo principal utilizado para a comunicação entre os livros de execução. Os outros streams são classificados como streams de mensagens, destinados a comunicar informações ao utilizador.

| Fluxo | Description | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Erro |Mensagem de erro para o utilizador. Ao contrário de uma exceção, o livro de recortes continua após uma mensagem de erro por padrão. |Escrito para a história do trabalho |Exibido no painel de saída do teste |
| Depurar |Mensagens destinadas a um utilizador interativo. Não deve ser usado em livros. |Não escrito para a história do trabalho |Não exibido no painel de saída do teste |
| Saída |Objetos destinados a ser consumidos por outros runbooks. |Escrito para a história do trabalho |Exibido no painel de saída do teste |
| Progresso |Registos gerados automaticamente antes e depois de cada atividade no runbook. O livro não deve tentar criar os seus próprios registos de progresso, uma vez que se destinam a um utilizador interativo. |Escrito para o histórico de emprego apenas se o registo de progresso for ligado para o runbook |Não exibido no painel de saída do teste |
| Verboso |Mensagens que dão informações gerais ou depurativas. |Escrito para o histórico de trabalho apenas se a exploração madeireira verbose for ligada para o runbook |Exibido no painel de saída do teste apenas se `VerbosePreference` a variável estiver definida para Continuar no runbook |
| Aviso |Mensagem de aviso para o utilizador. |Escrito para a história do trabalho |Exibido no painel de saída do teste |

## <a name="use-the-output-stream"></a>Use o fluxo de saída

O fluxo de saída é utilizado para a saída de objetos criados por um script ou fluxo de trabalho quando funciona corretamente. A Azure Automation utiliza principalmente este fluxo para que os objetos sejam consumidos por livros de bordo dos pais que chamam o [livro de bordo atual](automation-child-runbooks.md). Quando um progenitor [chama um diário de bordo,](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution)a criança devolve os dados do fluxo de saída para o progenitor.

O seu runbook utiliza o fluxo de saída para comunicar informações gerais ao cliente apenas se nunca for chamado por outro runbook. Como uma boa prática, no entanto, os seus livros devem normalmente utilizar o [fluxo verboso](#write-output-to-verbose-stream) para comunicar informações gerais ao utilizador.

Faça com que o seu livro de execução escreva dados para o fluxo de saída utilizando [a Write-Output](/powershell/module/microsoft.powershell.utility/write-output). Em alternativa, pode colocar o objeto na sua própria linha no script.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Manuseie a saída de uma função

Quando uma função de runbook escreve para o fluxo de saída, a saída é repercutido no runbook. Se o livro atribui essa saída a uma variável, a saída não é escrita para o fluxo de saída. Escrever para quaisquer outros fluxos a partir da função escreve para o fluxo correspondente para o livro de execução. Considere a seguinte amostra PowerShell Workflow runbook.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

O fluxo de saída para o trabalho do runbook é:

```output
Output inside of function
Output outside of function
```

O fluxo verboso para o trabalho de runbook é:

```output
Verbose outside of function
Verbose inside of function
```

Depois de ter publicado o livro de recortes e antes de o iniciar, também deve ligar o registo verboso nas definições do runbook para obter a saída de fluxo verboso.

### <a name="declare-output-data-type"></a>Declarar tipo de dados de saída

Seguem-se exemplos de tipos de dados de saída:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Declarar o tipo de dados de saída num fluxo de trabalho

Um fluxo de trabalho especifica o tipo de dados da sua saída utilizando o [atributo OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Este atributo não tem efeito durante o tempo de funcionação, mas fornece-lhe uma indicação no momento de conceção da saída esperada do runbook. À medida que o conjunto de ferramentas para os runbooks continua a evoluir, a importância de declarar tipos de dados de saída no tempo de conceção aumenta. Portanto, é uma boa prática incluir esta declaração em todos os livros que criar.

O runbook de exemplo seguinte cria uma saída de objeto de cadeia e inclui uma declaração do respetivo tipo de saída. Se o seu runbook criar uma saída de matriz de um determinado tipo, não deve deixar de especificar o tipo, por oposição a uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Declare o tipo de dados de saída num livro gráfico

Para declarar um tipo de saída num livro de execução de fluxo de trabalho gráfico ou gráfico PowerShell, pode selecionar a opção de menu **entrada e saída** e introduzir o tipo de saída. Recomenda-se a utilização do nome de classe .NET completo para tornar o tipo facilmente identificável quando um livro de bordo dos pais o referencia. A utilização do nome completo expõe todas as propriedades da classe ao databus no livro de recortes e aumenta a flexibilidade quando as propriedades são usadas para lógica condicional, registo e referência como valores para outras atividades de runbook.<br> ![Opção de entrada e saída do runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Depois de introduzir um valor no campo **Tipo de Saída** no painel de propriedades de entrada e saída, certifique-se de clicar fora do controlo para que reconheça a sua entrada.

O exemplo a seguir mostra dois livros gráficos para demonstrar a função entrada e saída. Aplicando o modelo de design de runbook modular, tem um runbook como o modelo Authenticate Runbook que gere a autenticação com a Azure usando a conta Run As. O segundo runbook, que normalmente executa a lógica principal para automatizar um determinado cenário, neste caso executa o modelo Authenticate Runbook. Apresenta os resultados do painel de saída do teste. Em circunstâncias normais, você faria algo contra um recurso aproveitando a saída do livro infantil.

Aqui está a lógica básica do livro de aplicação **AuthenticateTo-Azure.**<br> ![Exemplo de modelo de runbook ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) autenticado.

O livro de aplicação inclui o tipo de `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` saída, que devolve as propriedades do perfil de autenticação.<br> ![Exemplo do tipo de saída do runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Embora este runbook seja simples, há um item de configuração para chamar aqui. A última atividade executa o `Write-Output` cmdlet para escrever dados de perfil a uma variável usando uma expressão PowerShell para o `Inputobject` parâmetro. Este parâmetro é necessário para `Write-Output` .

O segundo livro de aplicação deste exemplo, denominado **Test-ChildOutputType,** define simplesmente duas atividades.<br> ![Exemplo de runbook tipo de saída de criança](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

A primeira atividade chama o **livro de aplicação AuthenticateTo-Azure.** A segunda atividade executa o `Write-Verbose` cmdlet com **fonte de dados** definido para **a produção de Atividade**. Além disso, **o caminho de campo** está definido para **Context.Subscription.SubscriptionName,** a saída de contexto do livro de execução **AuthenticateTo-Azure.**<br> ![Fonte de dados do parâmetro do Cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

A saída resultante é o nome da subscrição.<br> ![Resultados do runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Trabalhar com fluxos de mensagens

Ao contrário do fluxo de saída, os fluxos de mensagens comunicam informações ao utilizador. Existem vários fluxos de mensagens para diferentes tipos de informação, e a Azure Automation lida com cada fluxo de forma diferente.

### <a name="write-output-to-warning-and-error-streams"></a>Escreva saída para fluxos de aviso e erro

O aviso e os fluxos de erro registam problemas que ocorrem num livro de recortes. A Azure Automation escreve estes fluxos para o histórico de trabalho ao executar um livro de corridas. A automatização inclui os fluxos no painel de saída do Teste no portal Azure quando um livro de execução é testado.

Por predefinição, um livro de bordo continua a ser executado após um aviso ou erro. Pode especificar que o seu livro de execução deve suspender um aviso ou erro, tendo o livro de execuções definido uma [variável de preferência](#work-with-preference-variables) antes de criar a mensagem. Por exemplo, para fazer com que o livro de bordo suspenda um erro como faz numa exceção, desacione a `ErrorActionPreference` variável para Parar.

Crie uma mensagem de aviso ou erro utilizando o cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) ou [Write-Error.](/powershell/module/microsoft.powershell.utility/write-error) As atividades também podem escrever para os fluxos de aviso e erro.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Escrever saída para depurar fluxo

A Azure Automation utiliza o fluxo de mensagens de depuração para utilizadores interativos. Por predefinição, a Azure Automation não captura quaisquer dados de fluxo de depuração, apenas dados de saída, erro e aviso são capturados, bem como dados verbosos se o livro de execuções estiver configurado para capturá-los.

Para capturar dados de fluxo de depurar, tem de realizar duas ações nos seus livros de execução:

1. Desave a variável `$GLOBAL:DebugPreference="Continue"` , que diz ao PowerShell para continuar sempre que uma mensagem de depurado for encontrada.  A **$GLOBAL:** a porção diz ao PowerShell para o fazer no âmbito global e não em qualquer âmbito local em que o script esteja no momento em que a declaração é executada.

1. Redirecione o fluxo de depurado que não capturamos para um fluxo que capturamos como *a saída.* Isto é feito definindo a reorientação PowerShell contra a declaração a executar. Para obter mais informações sobre a reorientação powerShell, consulte [Sobre a reorientação](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Exemplos

Neste exemplo, o livro de recortes é configurado usando os `Write-Output` `Write-Debug` cmdlets e os cmdlets com a intenção de fazer dois fluxos diferentes.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Se este livro de bordo fosse executado como está, o painel de saída para a tarefa do runbook transmitiria a seguinte saída:

```output
This is an output message.
```

Neste exemplo, o livro de bordo é configurado semelhante ao exemplo anterior, exceto que a declaração `$GLOBAL:DebugPreference="Continue"` é incluída com a adição de no final da `5>&1` `Write-Debug` declaração.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Se este livro de bordo fosse executado, o painel de saída para a função de runbook transmitiria a seguinte saída:

```output
This is an output message.
This is a debug message.
```

Isto ocorre porque a `$GLOBAL:DebugPreference="Continue"` declaração diz ao PowerShell para exibir mensagens de depurar, e a adição ao `5>&1` final da declaração diz ao `Write-Debug` PowerShell para redirecionar o fluxo 5 (depurg) para o stream 1 (saída).

### <a name="write-output-to-verbose-stream"></a>Escreva a saída para verbose stream

O fluxo de mensagens Verbose suporta informações gerais sobre o funcionamento do runbook. Uma vez que o fluxo de depurg não está disponível para um livro de execução, o seu runbook deve usar mensagens verbosas para obter informações de depurar.

Por padrão, o histórico de trabalho não armazena mensagens verbosas de livros publicados, por razões de desempenho. Para armazenar mensagens verbose, utilize o separador **Configure** portal Azure com a definição **Log Verbose Records** para configurar os seus livros publicados para registar mensagens verbose. Ative esta opção apenas para resolver problemas ou para depurar um runbook. Na maioria dos casos, deve manter a definição padrão de não registar registos verbosos.

Ao [testar um livro de execução,](./manage-runbooks.md)as mensagens verbosas não são apresentadas mesmo que o livro de execuções esteja configurado para registar registos verbosos. Para exibir mensagens verbosas durante o [teste de um livro de recortes,](./manage-runbooks.md)tem de definir a `VerbosePreference` variável para Continuar. Com este conjunto variável, as mensagens verbosas são apresentadas no painel de saída de Teste do portal Azure.

O código a seguir cria uma mensagem verbose utilizando o [cmdlet Write-Verbose.](/powershell/module/microsoft.powershell.utility/write-verbose)

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Lidar com os registos de progresso

Pode utilizar o **separador Configurar** do portal Azure para configurar um livro de execução para registar registos de progresso. A definição predefinida é não registar os registos, para maximizar o desempenho. Na maioria dos casos, deve manter a definição predefinida. Ative esta opção apenas para resolver problemas ou para depurar um runbook.

Se ativar a registo de registos de progresso, o seu livro escreve um registo para o histórico de trabalho antes e depois de cada atividade ser executada. Testar um livro de execução não apresenta mensagens de progresso mesmo que o livro de execuções esteja configurado para registar registos de progresso.

>[!NOTE]
>O [cmdlet Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) não é válido num livro de aplicação, uma vez que este cmdlet se destina a ser utilizado com um utilizador interativo.

## <a name="work-with-preference-variables"></a>Trabalhar com variáveis preferenciais

Pode definir [determinadas variáveis de preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables) do Windows PowerShell nos seus livros de execução para controlar a resposta aos dados enviados para diferentes fluxos de saída. A tabela que se segue lista as variáveis preferenciais que podem ser utilizadas em runbooks, com os seus valores predefinidos e válidos. Estão disponíveis valores adicionais para as variáveis preferenciais quando utilizadas no Windows PowerShell fora da Azure Automation.

| Variável | Valor Predefinido | Valores válidos |
|:--- |:--- |:--- |
| `WarningPreference` |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| `ErrorActionPreference` |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Parar<br>Continuar<br>SilentlyContinue |

A tabela seguinte lista o comportamento dos valores variáveis preferenciais que são válidos em livros de execução.

| Valor | Comportamento |
|:--- |:--- |
| Continuar |Regista a mensagem e continua a executar o runbook. |
| SilentlyContinue |Continua a executar o runbook sem registar a mensagem. Este valor tem o efeito de ignorar a mensagem. |
| Parar |Regista a mensagem e suspende o runbook. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperar saída e mensagens de runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recupere a saída do runbook e as mensagens no portal Azure

Pode ver os detalhes de um trabalho de runbook no portal Azure utilizando o **separador Jobs** para o runbook. O resumo do trabalho apresenta os parâmetros de entrada e o [fluxo de saída,](#use-the-output-stream)além de informações gerais sobre o trabalho e quaisquer exceções que tenham ocorrido. O histórico de trabalho inclui mensagens do fluxo de saída e [streams de aviso e erro](#write-output-to-warning-and-error-streams). Também inclui mensagens do [fluxo verboso](#write-output-to-verbose-stream) e [registos](#handle-progress-records) de progresso se o livro de execuções estiver configurado para registar registos verbosos e de progresso.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recupere a saída do runbook e as mensagens no Windows PowerShell

No Windows PowerShell, pode obter saídas e mensagens de um livro de execução utilizando o [cmdlet Get-AzAutomationJobOutput.](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) Este cmdlet requer a identificação do trabalho e tem um parâmetro chamado `Stream` para especificar o fluxo para recuperar. Pode especificar um valor de Any para este parâmetro para recuperar todos os fluxos para o trabalho.

O exemplo seguinte inicia um runbook de exemplo e depois aguarda que este seja concluído. Uma vez que o livro de execução completa a execução, o script recolhe o fluxo de saída do runbook a partir do trabalho.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recupere a saída do runbook e as mensagens em runbooks gráficos

Para os livros gráficos, o registo extra de saída e mensagens está disponível sob a forma de rastreio ao nível da atividade. Existem dois níveis de rastreio: básico e detalhado. O rastreio básico mostra o tempo de início e fim de cada atividade no runbook, além de informações relacionadas com qualquer retrete de atividade. Alguns exemplos são o número de tentativas e a hora de início da atividade. O rastreio detalhado inclui funcionalidades básicas de rastreio, além de registo de dados de entrada e saída para cada atividade. 

Atualmente, o rastreio ao nível da atividade escreve registos usando o fluxo verboso. Por isso, deve ativar a registo de verbose quando ativar o rastreio. Para livros gráficos com rastreio ativado, não há necessidade de registar registos de progresso. O rastreio básico serve o mesmo propósito e é mais informativo.

![Vista de fluxos de trabalho de autoria gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Pode ver pela imagem que permitir a exploração de verbose e o rastreio de livros gráficos torna muito mais informação disponível na vista **job streams** de produção. Esta informação extra pode ser essencial para resolver problemas de produção com um livro de bordo. 

No entanto, a menos que você requeira esta informação para acompanhar o progresso de um livro de corridas para resolução de problemas, você pode querer continuar a rastrear desligado como uma prática geral. Os registos podem ser especialmente numerosos. Com o rastreio gráfico do runbook, pode obter dois a quatro registos por atividade, dependendo da configuração do rastreio básico ou detalhado.

**Para permitir o rastreio ao nível da atividade:**

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Na página Runbooks, selecione um runbook gráfico da sua lista de runbooks.
4. Em **Definições**, clique em **Registar e rastrear**.
5. Na página 'Registar e Rastrear', nos **registos verbosos do Log,** clique **em Ligar** para permitir a registo verboso.
6. No **rastreio ao nível da atividade,** altere o nível de rastreio para **Básico** ou **Detalhado,** com base no nível de rastreio que necessita.<br>

   ![Página de registo e rastreio de autoria gráfica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recupere a saída do runbook e as mensagens nos registos do Microsoft Azure Monitor

A Azure Automation pode enviar o estado de trabalho do runbook e os fluxos de trabalho para o seu espaço de trabalho Log Analytics. O Azure Monitor suporta registos que lhe permitem:

* Obtenha informações sobre os seus trabalhos de Automação.
* Desacione um e-mail ou alerta com base no seu estado de trabalho do runbook, por exemplo, Falhado ou Suspenso.
* Escreva consultas avançadas através de fluxos de emprego.
* Correlacionar empregos em todas as contas de Automação.
* Visualizar o histórico de empregos.

Para obter mais informações sobre a configuração da integração com os Registos do Monitor Azure para recolher, correlacionar e agir em dados de trabalho, consulte [o estado do trabalho de encaminhamento e fluxos de trabalho da Automação para os Registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com runbooks, consulte [Gerir os runbooks na Azure Automation.](manage-runbooks.md)
* Se não estiver familiarizado com a scripting powerShell, consulte a documentação [powerShell.](/powershell/scripting/overview)
* Para obter a referência cmdlet Azure Automation PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
