---
title: Runbook Output and Messages na automatização do Azure
description: Desribes como criar e obter resultados e erro mensagens através de runbooks na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ec71f8998f7db07cafca7f8141acb9898b016328
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821358"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Resultado do Runbook e mensagens na automatização do Azure
A maioria dos runbooks de automatização do Azure têm alguma forma de saída. Este resultado pode ser uma mensagem de erro para o utilizador ou um objeto complexo que pretende utilizar com outro runbook. Windows PowerShell fornece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para enviar a saída de um script ou o fluxo de trabalho. A automatização do Azure funciona com cada um desses fluxos de forma diferente. Deve seguir as melhores práticas para saber como utilizar cada quando estiver a criar um runbook.

A tabela seguinte fornece uma breve descrição de cada um dos fluxos e do respetivo comportamento no portal do Azure para runbooks publicados e quando [testar um runbook](automation-testing-runbook.md). Existem mais detalhes sobre cada fluxo são fornecidos nas secções posteriores.

| Transmitir em fluxo | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Saída |Objetos destinados a ser consumidos por outros runbooks. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Aviso |Mensagem de aviso para o utilizador. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Erro |Mensagem de erro para o utilizador. Ao contrário de uma exceção, o runbook continua após uma mensagem de erro por predefinição. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Verboso |Mensagens que fornecem informações gerais ou depuração. |Escrito no histórico da tarefa apenas se o registo verboso está ativado runbook. |Apresentado no painel de resultados de teste do apenas se $VerbosePreference for definida para continuar no runbook. |
| Progresso |Registos gerados automaticamente antes e após cada atividade no runbook. O runbook não deve tentar criar seus próprios registos de progresso, uma vez que eles se destinam a ser um utilizador interativo. |Escrito no histórico da tarefa apenas se o registo de progressos estiver ativado para o runbook. |Não é apresentado no painel de resultados do teste. |
| depurar |Mensagens destinadas a um utilizador interativo. Não devem ser utilizadas em runbooks. |Não é escrito no histórico da tarefa. |Não é escrito no painel de resultados de teste. |

## <a name="output-stream"></a>Fluxo de saída
O fluxo de saída destina-se a saída de objetos, que são criados por um script ou o fluxo de trabalho quando é executada corretamente. Na automatização do Azure, este fluxo é utilizado sobretudo para objetos que se destinam a ser consumidos por [principal runbooks que chamem o runbook atual](automation-child-runbooks.md). Quando [chamar um runbook inline](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) partir de um runbook de principal, são devolvidos dados do fluxo de saída para o elemento principal. Utilize apenas o fluxo de saída para comunicar informações gerais para o utilizador se souber que o runbook nunca é chamado por outro runbook. Como melhor prática, no entanto, deve geralmente usa o [Stream verboso](#verbose-stream) para comunicar informações gerais ao utilizador.

Pode escrever dados para o fluxo de saída através de [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) ou ao colocar o objeto na sua própria linha no runbook.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Saída de uma função
Quando escreve o fluxo de saída numa função que está incluída no seu runbook, o resultado é passado para o runbook. Se o runbook atribuir essa saída a uma variável, em seguida, ele não está escrito para o fluxo de saída. Escrever em quaisquer outros fluxos a partir de dentro da função escreve no fluxo correspondente para o runbook.

Considere o runbook de exemplo abaixo:

```PowerShell
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

O fluxo de saída da tarefa de runbook seria:

```output
Output inside of function
Output outside of function
```

O fluxo verboso da tarefa de runbook seria:

```output
Verbose outside of function
Verbose inside of function
```

Depois de publicar o runbook e antes de iniciá-la, tem também de ativar o registo nas definições de runbook para obter o resultado de fluxo verboso verboso.

### <a name="declaring-output-data-type"></a>Tipo de dados de saída declarativo
Um fluxo de trabalho pode especificar o tipo de dados das respetivas saídas ao utilizar o [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Este atributo não tem qualquer efeito durante o tempo de execução, mas ela fornece uma indicação para o autor do runbook no tempo de design de saída prevista do runbook. À medida que o conjunto de ferramentas para runbooks continua a evoluir, a importância de declarar os tipos de dados de saída em tempo de design aumenta em importância. Como resultado, é melhor prática incluir esta declaração em todos os runbooks que criar.

Aqui está uma lista de exemplo tipos de saída:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

O runbook de exemplo seguintes produz um objeto de cadeia de caracteres e inclui uma declaração do respetivo tipo de saída. Se o seu runbook devolve uma matriz de um determinado tipo, em seguida, ainda deverá especificar o tipo em vez de uma matriz do tipo.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Para declarar um tipo de saída no gráfico ou fluxo de trabalho de PowerShell gráfico runbooks, pode selecionar o **de entrada e saída** opção de menu e escreva o nome do tipo de saída. Recomenda-se que utilizar o nome de classe .NET completo para torná-lo facilmente identificável quando fazendo referência a ele um runbook principal. Isso expõe todas as propriedades dessa classe no barramento de dados no runbook e proporciona muita flexibilidade quando usá-los para a lógica condicional, registo e referenciar como valores para outras atividades no runbook.<br> ![Opção de entrada do Runbook e de saída](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

No exemplo a seguir, tem duas runbooks gráficos para demonstrar esta funcionalidade. Se aplicar o modelo de design do modular runbook, tiver um runbook, que serve como a *modelo de autenticação de Runbook* gerenciar a autenticação com o Azure com a conta Run As. Nosso runbook segundo, o que normalmente executará a lógica central para automatizar um determinado cenário, neste caso vai executar o *modelo de autenticação de Runbook* e exibir os resultados para sua **teste** Painel de resultados. Em circunstâncias normais, teria este runbook fazer alguma coisa em relação a um recurso aproveitando a saída do runbook subordinado.

Segue-se a lógica básica do **AuthenticateTo Azure** runbook.<br> ![Exemplo de modelo do Runbook de autenticar](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Ele inclui o tipo de saída *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, que retorna a autenticação, as propriedades do perfil.<br> ![Exemplo de tipo de resultado do Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Embora este runbook seja simples, há um item de configuração de ressaltar aqui. A última atividade está a executar o **Write-Output** cmdlet e escreve os dados de perfil a uma variável de _ $ usando uma expressão do PowerShell para o **Inputobject** parâmetro, que é necessário para esse cmdlet.  

Para o runbook segundo neste exemplo, com o nome *teste ChildOutputType*, simplesmente tem duas atividades.<br> ![Runbook de tipo de saída de exemplo subordinado](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

As chamadas de atividade primeiro o **AuthenticateTo Azure** runbook e a segunda atividade está em execução a **Write-Verbose** cmdlet com o **origem de dados** de  **Saída da atividade** e o valor de **caminho do campo** é **Context.Subscription.SubscriptionName**, que está especificando a saída de contexto do  **O Azure AuthenticateTo** runbook.<br> ![Origem de dados do parâmetro de cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

A saída resultante é o nome da subscrição.<br> ![Resultados de Runbook de teste ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Uma observação sobre o comportamento do controle de tipo de saída. Quando escreve um valor no campo Tipo de saída no painel de propriedades de entrada e saída, tem de clicar fora do controle depois de escrever, para que sua participação a ser reconhecida pelo controle.  

## <a name="message-streams"></a>Fluxos de mensagens
Ao contrário do fluxo de saída, fluxos de mensagens destinam-se para comunicar informações ao usuário. Existem vários fluxos de mensagens para diferentes tipos de informações e cada um é tratado diferentemente pela automatização do Azure.

### <a name="warning-and-error-streams"></a>Fluxos de avisos e erros
Os fluxos de avisos e erros destinam-se a registar problemas que ocorrem num runbook. São escritos no histórico da tarefa quando um runbook é executado e que estão incluídos no painel de resultados do teste no portal do Azure, quando um runbook é testado. Por predefinição, o runbook continuará a executar após um aviso ou erro. Pode especificar que o runbook deve ser suspenso após um aviso ou erro ao definir uma [variável de preferência](#preference-variables) no runbook antes de criar a mensagem. Por exemplo, para fazer com que um runbook a suspender um erro na forma como seria uma exceção, defina **$ErrorActionPreference** para parar.

Criar um aviso ou erro mensagem através da [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet. Atividades também podem escrever nestes fluxos.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Fluxo verboso
O fluxo de mensagens verbosas destina-se a informações gerais sobre o funcionamento do runbook. Uma vez que o [Stream de depuração](#debug-stream) não está disponível num runbook, as mensagens verbosas devem ser utilizadas para informações de depuração. Por predefinição, as mensagens verbosas de runbooks publicados não são armazenadas no histórico da tarefa. Para armazenar as mensagens verbosas, configure os runbooks publicados para criar registos verbosos no separador Configurar do runbook no portal do Azure. Na maioria dos casos, deve manter a predefinição de não criar registos verbosos dos runbooks por motivos de desempenho. Ative esta opção apenas para solucionar problemas ou depurar um runbook.

Quando [testar um runbook](automation-testing-runbook.md), as mensagens verbosas não são apresentadas, mesmo que o runbook esteja configurado para criar registos verbosos. Para apresentar mensagens verbosas enquanto [testar um runbook](automation-testing-runbook.md), tem de definir a variável $VerbosePreference para continuar. Com essa variável definida, as mensagens verbosas são apresentadas no painel de resultados do teste do portal do Azure.

Criar um através de mensagens verbosas a [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Fluxo de depuração
O fluxo de depuração destina-se para utilização com um utilizador interativo e não deve ser utilizado em runbooks.

## <a name="progress-records"></a>Registos de progresso
Se configurar um runbook para iniciar o curso regista (no separador Configurar do runbook no portal do Azure), em seguida, um registo será escrito no histórico da tarefa antes e depois da execução de cada atividade. Na maioria dos casos, deve manter a predefinição de não criar registos de progresso de um runbook para maximizar o desempenho. Ative esta opção apenas para solucionar problemas ou depurar um runbook. Ao testar um runbook, não são apresentadas mensagens de progresso, mesmo que o runbook esteja configurado para registos de progressos.

O [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet não é válido num runbook, uma vez que este cmdlet destina-se a utilização com um utilizador interativo.

## <a name="preference-variables"></a>Variáveis de preferência
Windows PowerShell usa [variáveis de preferência](https://technet.microsoft.com/library/hh847796.aspx) para determinar como responder a dados enviados para diferentes fluxos de saída. Pode definir estas variáveis num runbook para controlar como ele responde a dados enviados para vários fluxos.

A tabela seguinte lista as variáveis de preferências que podem ser utilizadas em runbooks com seus válido e os valores predefinidos. Esta tabela inclui apenas os valores que são válidos em runbooks. Valores adicionais são válidos para as variáveis de preferências quando utilizados no Windows PowerShell fora da automatização do Azure.

| Variável | Valor Predefinido | Valores válidos |
|:--- |:--- |:--- |
| WarningPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| ErrorActionPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Parar<br>Continuar<br>SilentlyContinue |

A tabela seguinte lista o comportamento para os valores das variáveis de preferência que são válidos em runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continuar |Regista a mensagem e continua a executar o runbook. |
| SilentlyContinue |Continua a executar o runbook sem registar a mensagem. Este valor não tem o efeito de ignorar a mensagem. |
| Parar |Regista a mensagem e suspende o runbook. |

## <a name="runbook-output"></a>Obter mensagens e de saída do runbook
### <a name="azure-portal"></a>Portal do Azure
Pode ver os detalhes de uma tarefa de runbook no portal do Azure a partir do separador tarefas de um runbook. O resumo da tarefa apresenta os parâmetros de entrada e o [saída Stream](#output-stream) , além das informações gerais sobre a tarefa e eventuais exceções, caso tenham ocorrido. O histórico inclui as mensagens do [saída Stream](#output-stream) e [aviso e erro fluxos](#warning-and-error-streams) , bem como o [Stream verboso](#verbose-stream) e [registos de progresso](#progress-records) se o runbook estiver configurado para criar registos verbosos e registos de progresso.

### <a name="windows-powershell"></a>Windows PowerShell
No Windows PowerShell, pode obter resultados e mensagens de um runbook com o [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) cmdlet. Este cmdlet necessita do ID da tarefa e tem um parâmetro denominado Stream, onde pode especificar que fluxo deve retornar. Pode especificar **qualquer** para retornar todos os fluxos da tarefa.

O exemplo seguinte inicia um runbook de exemplo e, em seguida, aguarda que este seja concluído. Depois de concluído, o seu fluxo de saída é recolhido da tarefa.

```PowerShell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Criação de gráficos
Para runbooks gráficos, o registo extra está disponível na forma de rastreio do nível de atividade. Existem dois níveis de rastreio: Básica e detalhadas. No rastreamento básico, pode ver a hora de início e fim de cada atividade no runbook e informações relacionadas com a quaisquer tentativas de atividade. Alguns exemplos são o número de tentativas e hora de início da atividade. Rastreamento detalhado, receberá dados de saída e de rastreio básico adição de entrada para cada atividade. Atualmente os registos de rastreio são escritos usando o fluxo verboso, pelo que deve ativar o registo verboso quando ativar o rastreio. Para obter runbooks gráficos com o rastreamento ativado, não é necessário para registos de progressos. Tem a mesma finalidade de rastreamento básico e é mais informativo.

![Vista de fluxos de trabalho de criação gráfico](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Pode ver na captura de ecrã anterior que, quando ativa a verboso registo e o rastreio para runbooks gráficos, muito mais informações estão disponíveis na vista de fluxos de trabalho de produção. Estas informações adicionais podem ser essenciais para resolução de problemas de produção com um runbook e, portanto, só deverá ativá-lo para essa finalidade e não como uma prática geral. Os registos de rastreio podem ser especialmente inúmeras. Com o rastreamento de runbook gráfico, pode obter registos de duas a quatro por atividade, dependendo se configurou o rastreamento básico ou detalhado. A menos que precise estas informações para acompanhar o progresso de um runbook para resolução de problemas, pode querer manter rastreio desativado.

**Para ativar o rastreio de nível de atividade, execute os seguintes passos:**

1. No portal do Azure, abra a sua conta da Automatização.
2. Sob **automatização de processos**, selecione **Runbooks** para abrir a lista de runbooks.
3. Na página de Runbooks, clique para selecionar um runbook gráfico da sua lista de runbooks.
4. Sob **configurações**, clique em **registo e rastreio**.
5. O registo e rastreio de página, em registos verbosos, clique em **nos** para ativar o registo verboso e sob o rastreio do nível de atividade, alterar o nível de rastreio para **básica** ou **Detailed** com base no nível de rastreio que necessita.<br>
   
   ![Registo de criação gráfica e rastreamento de página](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Registos do Microsoft Azure Monitor
Automatização pode enviar as runbook fluxos de estado e a tarefa de trabalho para a área de trabalho do Log Analytics. Com os registos do Azure Monitor, pode,

* Obtenha informações sobre as tarefas de automatização 
* Acionar um e-mail ou o alerta com base no seu estado de tarefa de runbook (por exemplo, falha ou suspensas) 
* Escrever consultas avançadas nos seus fluxos de trabalho 
* Obtenha uma correlação tarefas entre contas de automatização 
* Visualizar o histórico de tarefas ao longo do tempo    

Para obter mais informações sobre como configurar a integração com os registos do Azure Monitor para recolher, correlacionar e atuar sobre os dados da tarefa, consulte [reencaminhar o estado da tarefa e fluxos de trabalho de automatização para registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md)
* Para compreender como criar e utilizar runbooks subordinados, veja [runbooks subordinados na automatização do Azure](automation-child-runbooks.md)


