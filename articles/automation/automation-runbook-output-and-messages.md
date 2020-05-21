---
title: Saída de livro de execução e mensagens na Automação Azure
description: Descreve como criar e recuperar mensagens de saída e erro a partir de livros de execução na Automatização Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 83813b30f30bf5aba62f2f94a8ec3cefd2e7090f
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715142"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Saída de livro de execução e mensagens na Automação Azure

A maioria dos livros de automação Azure têm alguma forma de saída. Esta saída pode ser uma mensagem de erro para o utilizador ou um objeto complexo destinado a ser utilizado com outro livro de execução. O Windows PowerShell fornece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para enviar saída a partir de um script ou fluxo de trabalho. A Azure Automation trabalha com cada um destes riachos de forma diferente. Deve seguir as melhores práticas para usar os streams quando está a criar um livro de corridas.

A tabela seguinte descreve brevemente cada stream com o seu comportamento no portal Azure para livros publicados e durante o [teste de um livro de execução](automation-testing-runbook.md). O fluxo de saída é o fluxo principal utilizado para a comunicação entre os livros de execução. Os outros fluxos são classificados como fluxos de mensagens, destinados a comunicar informações ao utilizador. 

| Transmitir em fluxo | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Erro |Mensagem de erro para o utilizador. Ao contrário de uma exceção, o livro de execução continua após uma mensagem de erro por padrão. |Escrito para a história do trabalho |Exibido no painel de saída do teste |
| Depurar |Mensagens destinadas a um utilizador interativo. Não deve ser usado em livros de corridas. |Não escrito para a história do trabalho |Não exibido no painel de saída do teste |
| Saída |Objetos destinados a ser consumidos por outros runbooks. |Escrito para a história do trabalho |Exibido no painel de saída do teste |
| Progresso |Registos gerados automaticamente antes e depois de cada atividade no runbook. O livro de execução não deve tentar criar os seus próprios registos de progresso, uma vez que se destinam a um utilizador interativo. |Escrito para o histórico de emprego apenas se o progresso da exploração é ligado para o livro de corridas |Não exibido no painel de saída do teste |
| Verboso |Mensagens que dão informações gerais ou depuradas. |Escrito para o histórico de emprego apenas se a exploração madeireira verbosa for ligada para o livro de corridas |Apresentado no painel de saída do teste apenas se a variável for definida para continuar no livro de `VerbosePreference` execução |
| Aviso |Mensagem de aviso para o utilizador. |Escrito para a história do trabalho |Exibido no painel de saída do teste |

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="output-stream"></a>Fluxo de saída

O fluxo de saída é utilizado para a saída de objetos criados por um script ou fluxo de trabalho quando funciona corretamente. A Azure Automation usa principalmente este fluxo para objetos a consumir por livros de execução dos pais que chamam o atual livro de [execução](automation-child-runbooks.md). Quando um progenitor chama um livro de [execução inline,](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution)a criança devolve os dados do fluxo de saída ao progenitor. 

O seu livro de execução utiliza o fluxo de saída para comunicar informações gerais ao cliente apenas se nunca for chamado por outro livro de execução. Como uma boa prática, no entanto, os livros de execução devem normalmente usar o [fluxo Verbose](#verbose-stream) para comunicar informações gerais ao utilizador.

Faça com que o seu livro de execução escreva dados para o fluxo de saída utilizando a [Saída de Escrita](https://technet.microsoft.com/library/hh849921.aspx). Em alternativa, pode colocar o objeto na sua própria linha no script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Manuseamento da saída a partir de uma função

Quando uma função de livro de execução escreve para o fluxo de saída, a saída é passada de volta para o livro de execução. Se o livro de execução atribuir essa saída a uma variável, a saída não está escrita no fluxo de saída. Escrever a quaisquer outros fluxos de dentro da função escreve para o fluxo correspondente para o livro de execução. Considere o seguinte livro de execução powerShell Workflow.

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

O fluxo de saída para o trabalho do livro de corridas é:

```output
Output inside of function
Output outside of function
```

O fluxo verbose para o trabalho do livro de corridas é:

```output
Verbose outside of function
Verbose inside of function
```

Depois de publicar o livro de execução e antes de o iniciar, também deve ligar a madeira verbosa nas definições do livro de execução para obter a saída de fluxo verbose.

### <a name="declaring-output-data-type"></a>Declarar tipo de dados de saída

Seguem-se exemplos de tipos de dados de saída:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Declarar tipo de dados de saída num fluxo de trabalho

Um fluxo de trabalho especifica o tipo de dados da sua saída utilizando o [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Este atributo não tem qualquer efeito durante o tempo de funcionação, mas fornece-lhe uma indicação no momento do design da saída esperada do livro de execução. À medida que o conjunto de ferramentas para livros de execução continua a evoluir, a importância de declarar tipos de dados de saída no tempo de design aumenta. Portanto, é uma boa prática incluir esta declaração em quaisquer livros que você cria.

O runbook de exemplo seguinte cria uma saída de objeto de cadeia e inclui uma declaração do respetivo tipo de saída. Se o seu runbook criar uma saída de matriz de um determinado tipo, não deve deixar de especificar o tipo, por oposição a uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Declarar tipo de dados de saída num livro de execução gráfico

Para declarar um tipo de saída num livro de execução de fluxo de trabalho de PowerShell gráfico ou gráfico, pode selecionar a opção de menu **De entrada e saída** e introduzir o tipo de saída. Recomenda-se utilizar o nome completo da classe .NET para tornar o tipo facilmente identificável quando um livro-mãe o refere. O uso do nome completo expõe todas as propriedades da classe ao databus no livro de execução e aumenta a flexibilidade quando as propriedades são usadas para lógica condicional, exploração madeireira e referenciação como valores para outras atividades do livro de corridas.<br> ![Opção de entrada e saída do livro de execução](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Depois de introduzir um valor no campo Tipo de **Saída** no painel de propriedades de Entrada e Saída, certifique-se de clicar fora do controlo para que reconheça a sua entrada.

O exemplo seguinte mostra dois livros gráficos para demonstrar a funcionalidade de entrada e saída. Aplicando o modelo modular de design de runbook, tem um livro de execução como o modelo Authenticate Runbook gerindo a autenticação com o Azure usando a conta Run As. O segundo livro de corridas, que normalmente executa a lógica central para automatizar um determinado cenário, neste caso executa o modelo Authenticate Runbook. Apresenta os resultados no painel de saída do teste. Em circunstâncias normais, você faria com que este livro de execução fizesse algo contra um recurso alavancando a saída do livro infantil.

Aqui está a lógica básica do livro de execução **AuthenticateTo-Azure.**<br> ![Autenticar modelo de livro de execução ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) exemplo .

O livro de execução inclui o tipo de `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` saída, que devolve as propriedades do perfil de autenticação.<br> ![Exemplo do tipo de saída do livro de execução](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Embora este livro seja simples, há um item de configuração para chamar aqui. A última atividade executa o `Write-Output` cmdlet para escrever dados de perfil para uma variável usando uma expressão PowerShell para o `Inputobject` parâmetro. Este parâmetro é necessário para `Write-Output` .

O segundo livro de ensaios neste exemplo, denominado **Test-ChildOutputType,** simplesmente define duas atividades.<br> ![Exemplo, livro de execução do tipo de saída infantil](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

A primeira atividade chama o livro de execução **AuthenticateTo-Azure.** A segunda atividade executa o `Write-Verbose` cmdlet com **fonte** de dados definida para **a saída**de Atividade . Além disso, **o caminho de campo** está definido para **Context.Subscription.SubscriptionName**, a saída de contexto do livro de execução **AuthenticateTo-Azure.**<br> ![Fonte de dados do parâmetro cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

A saída resultante é o nome da subscrição.<br> ![Resultados do livro de execução test-childoutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Fluxos de mensagens

Ao contrário do fluxo de saída, os streams de mensagens comunicam informações ao utilizador. Existem vários fluxos de mensagens para diferentes tipos de informação, e a Azure Automation lida com cada fluxo de forma diferente.

### <a name="warning-and-error-streams"></a>Fluxos de advertência e erro

O Aviso e erro transmite problemas de registo que ocorrem num livro de execução. A Azure Automation escreve estes fluxos para o histórico de trabalho ao executar um livro de corridas. A automatização inclui os fluxos no painel de saída do Teste no portal Azure quando um livro de execução é testado. 

Por predefinição, um livro de execução continua a ser executado após um aviso ou erro. Pode especificar que o seu livro de execução deve suspender com um aviso ou erro, fazendo com que o livro de execução detete uma [variável de preferência](#preference-variables) antes de criar a mensagem. Por exemplo, para fazer com que o livro de execução suspenda um erro como faz numa exceção, defino a `ErrorActionPreference` variável para Parar.

Crie uma mensagem de aviso ou erro utilizando o cmdlet [de aviso de escrita](https://technet.microsoft.com/library/hh849931.aspx) ou de erro de [escrita.](https://technet.microsoft.com/library/hh849962.aspx) As atividades também podem escrever para os streams de Aviso e Erro.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Fluxo de depuração

A Azure Automation utiliza o fluxo de mensagens Debug para utilizadores interativos. Não deve ser usado em livros de execução.

### <a name="verbose-stream"></a>Fluxo verboso

O fluxo de mensagens Verbose suporta informações gerais sobre a operação do livro de recortes. Uma vez que o fluxo Debug não está disponível para um livro de execução, o seu livro de execução deve utilizar mensagens verbosas para depurar informações. 

Por padrão, o histórico de trabalho não armazena mensagens verbosas de livros publicados, por razões de desempenho. Para armazenar mensagens verbosas, utilize o separador **Configurar** do portal Azure com a definição **de Log Verbose Records** para configurar os seus livros publicados para registar mensagens verbosas. Ative esta opção apenas para resolver problemas ou para depurar um runbook. Na maioria dos casos, deve manter a definição padrão de não registar registos verbosos.

Ao testar um livro de [execução,](automation-testing-runbook.md)as mensagens verbosas não são apresentadas mesmo que o livro de execução esteja configurado para registar registos verbosos. Para exibir mensagens verbosas enquanto testa um livro de [execução,](automation-testing-runbook.md)tem de definir a `VerbosePreference` variável para continuar. Com este conjunto variável, as mensagens verbosas são exibidas no painel de saída do portal Azure.

O seguinte código cria uma mensagem verbosa utilizando o cmdlet [Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Registos de progresso

Pode utilizar o separador **Configure** do portal Azure para configurar um livro de execução para registar registos de progresso. A definição predefinida é não registar os registos, para maximizar o desempenho. Na maioria dos casos, deve manter a definição predefinida. Ative esta opção apenas para resolver problemas ou para depurar um runbook. 

Se permitir a exploração de registos de progresso, o seu livro de execução escreve um registo para o histórico de empregos antes e depois de cada atividade ser executada. Testar um livro de execução não mostra mensagens de progresso mesmo que o livro de execução esteja configurado para registar registos de progresso.

>[!NOTE]
>O cmdlet Write-Progress não é válido num livro de execução, uma vez que este cmdlet [destina-se](https://technet.microsoft.com/library/hh849902.aspx) a ser utilizado com um utilizador interativo.

## <a name="preference-variables"></a>Variáveis preferenciais

Pode definir certas [variáveis preferenciais](https://technet.microsoft.com/library/hh847796.aspx) do Windows PowerShell nos seus livros de execução para controlar a resposta aos dados enviados para diferentes fluxos de saída. A tabela seguinte lista as variáveis preferenciais que podem ser usadas em livros de execução, com os seus valores predefinidos e válidos. Valores adicionais estão disponíveis para as variáveis preferenciais quando usados no Windows PowerShell fora da Automação Azure.

| Variável | Valor Predefinido | Valores válidos |
|:--- |:--- |:--- |
| `WarningPreference` |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| `ErrorActionPreference` |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Parar<br>Continuar<br>SilentlyContinue |

A tabela seguinte enumera o comportamento dos valores variáveis preferenciais que são válidos em livros de execução.

| Valor | Comportamento |
|:--- |:--- |
| Continuar |Regista a mensagem e continua a executar o runbook. |
| SilentlyContinue |Continua a executar o runbook sem registar a mensagem. Este valor tem o efeito de ignorar a mensagem. |
| Parar |Regista a mensagem e suspende o runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperação da saída do livro de corridas e das mensagens

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperar saída de livro de corridas e mensagens no portal Azure

Pode ver os detalhes de um trabalho de livro de corridas no portal Azure usando o separador **Jobs** para o livro de execução. O resumo do trabalho apresenta os parâmetros de entrada e o fluxo de [saída,](#output-stream)para além de informações gerais sobre o trabalho e quaisquer exceções que tenham ocorrido. O histórico de trabalho inclui mensagens dos fluxos de saída e dos fluxos de [aviso e erro.](#warning-and-error-streams) Também inclui mensagens do [fluxo verbose](#verbose-stream) e [registos](#progress-records) de progresso se o livro de execução estiver configurado para registar registos verbosos e de progresso.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperar saída de livro de corridas e mensagens no Windows PowerShell

No Windows PowerShell, é possível recuperar a saída e as mensagens de um livro de execução utilizando o cmdlet [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Este cmdlet requer a identificação do trabalho e tem um parâmetro chamado `Stream` para especificar o fluxo para recuperar. Pode especificar um valor de Qualquer para este parâmetro para recuperar todos os fluxos para o trabalho.

O exemplo seguinte inicia um runbook de exemplo e depois aguarda que este seja concluído. Assim que o livro de execução completar a execução, o script recolhe o fluxo de saída do livro de execução do trabalho.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperar saída de livro de corridas e mensagens em livros gráficos

Para livros de execução gráficos, o registo extra de saída e mensagens está disponível sob a forma de rastreio ao nível da atividade. Existem dois níveis de rastreio: Básico e Detalhado. O rastreio básico exibe o tempo de início e fim de cada atividade no livro de execução, além de informações relacionadas com eventuais repetições de atividade. Alguns exemplos são o número de tentativas e o tempo de início da atividade. O rastreio detalhado inclui características básicas de rastreio, além do registo de dados de entrada e de saída para cada atividade. 

Atualmente, o rastreio ao nível da atividade escreve registos usando a corrente verbose. Por isso, deve ativar a exploração verbosa quando ativa o rastreio. Para os livros gráficos com rastreio habilitado, não há necessidade de registar registos de progresso. O rastreio básico serve o mesmo propósito e é mais informativo.

![Visão de fluxos de trabalho de autoria gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Pode ver pela imagem que permitir a exploração verbosa e o rastreio de livros gráficos disponibiliza muito mais informação na vista **De produção Job Streams.** Esta informação extra pode ser essencial para resolver problemas de produção com um livro de execução. 

No entanto, a menos que necessite desta informação para acompanhar o progresso de um livro de corridas para resolução de problemas, talvez queira continuar a rastrear desligado como uma prática geral. Os registos de vestígios podem ser especialmente numerosos. Com o rastreio de rcadirgráficos gráficos, pode obter dois a quatro discos por atividade, dependendo da sua configuração de rastreio básico ou detalhado.

**Para permitir o rastreio ao nível da atividade:**

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.
3. Na página Runbooks, selecione um livro de execução gráfico da sua lista de livros de execução.
4. Em **Definições,** clique em **Registar e rastrear**.
5. Na página Logging and Tracing, sob **registos verbosos**de Log, clique **em On** para ativar a exploração verbosa.
6. No rastreio **ao nível da atividade,** altere o nível de rastreio para **Básico** ou **Detalhado,** com base no nível de rastreio necessário.<br>

   ![Página de autorgráfico gráfico de login e rastreio](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperar saída de livro de corridas e mensagens nos registos do Microsoft Azure Monitor

A Azure Automation pode enviar o estado de trabalho do livro de recortes e os fluxos de trabalho para o seu espaço de trabalho Log Analytics. O Monitor Azure suporta registos que lhe permitem:

* Obtenha informações sobre os seus trabalhos de Automação.
* Desencadeie um e-mail ou alerta com base no estado do seu trabalho no livro de recortes, por exemplo, Falhado ou Suspenso.
* Escreva consultas avançadas através de fluxos de emprego.
* Correlacionar os empregos através das contas da Automação.
* Visualizar o histórico de emprego.

Para obter mais informações sobre a configuração da integração com os registos do Monitor Azure para recolher, correlacionar e agir sobre os dados de emprego, consulte o estado de trabalho avançado e os [fluxos de emprego da Automação para os registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre a execução do livro de corridas, monitorização de trabalhos de livros de corridas e outros detalhes técnicos, consulte [Track a runbook job](automation-runbook-execution.md).
* Para entender como desenhar e usar livros infantis, consulte [livros infantis em Automação Azure](automation-child-runbooks.md).
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](/powershell/scripting/overview).
