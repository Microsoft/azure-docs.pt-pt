---
title: Saída e mensagens do runbook na automação do Azure
description: Descreve como criar e recuperar mensagens de saída e de erro de runbooks na automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: df0842635d13180951f8d79a598fe0708ca64b8a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850528"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Saída e mensagens do runbook na automação do Azure

A maioria dos runbooks de automação do Azure tem alguma forma de saída. Essa saída pode ser uma mensagem de erro para o usuário ou um objeto complexo que você pretende usar com outro runbook. O Windows PowerShell fornece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para enviar a saída de um script ou fluxo de trabalho. A automação do Azure funciona com cada um desses fluxos de forma diferente. Você deve seguir as práticas recomendadas de como usar cada um quando estiver criando um runbook.

A tabela a seguir fornece uma breve descrição de cada um dos fluxos e seu comportamento na portal do Azure para runbooks publicados e ao [testar um runbook](automation-testing-runbook.md). Mais detalhes sobre cada fluxo são fornecidos nas seções posteriores.

| Stream | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Saída |Objetos destinados a ser consumidos por outros runbooks. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Aviso |Mensagem de aviso para o utilizador. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Erro |Mensagem de erro para o utilizador. Ao contrário de uma exceção, o runbook continua após uma mensagem de erro por predefinição. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Verboso |Mensagens que fornecem informações gerais ou de depuração. |Escrito no histórico da tarefa apenas se o registo verboso está ativado runbook. |Exibido no painel saída de teste somente se $VerbosePreference estiver definido para continuar no runbook. |
| Progresso |Registos gerados automaticamente antes e após cada atividade no runbook. O runbook não deve tentar criar seus próprios registros de progresso, pois eles são destinados a um usuário interativo. |Escrito no histórico da tarefa apenas se o registo de progressos estiver ativado para o runbook. |Não é apresentado no painel de resultados do teste. |
| depurar |Mensagens destinadas a um utilizador interativo. Não deve ser usado em runbooks. |Não é escrito no histórico da tarefa. |Não é escrito no painel de resultados de teste. |

## <a name="output-stream"></a>Fluxo de saída
O fluxo de saída destina-se à saída de objetos, que são criados por um script ou fluxo de trabalho quando ele é executado corretamente. Na automação do Azure, esse fluxo é usado principalmente para objetos destinados a serem consumidos por [runbooks pai que chamam o runbook atual](automation-child-runbooks.md). Quando [chamar um runbook inline](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) partir de um runbook de principal, são devolvidos dados do fluxo de saída para o elemento principal. Use apenas o fluxo de saída para comunicar informações gerais de volta para o usuário se você souber que o runbook nunca é chamado por outro runbook. Como melhor prática, no entanto, deve geralmente usa o [Stream verboso](#verbose-stream) para comunicar informações gerais ao utilizador.

Pode escrever dados para o fluxo de saída através de [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) ou ao colocar o objeto na sua própria linha no runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Saída de uma função

Quando você grava no fluxo de saída em uma função que está incluída no seu runbook, a saída é passada de volta para o runbook. Se o runbook atribuir essa saída a uma variável, em seguida, ele não está escrito para o fluxo de saída. Gravar em quaisquer outros fluxos de dentro da função grava no fluxo correspondente para o runbook.

Considere o seguinte runbook de exemplo:

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

Depois de publicar o runbook e antes de iniciá-lo, você também deve ativar o log detalhado nas configurações do runbook para obter a saída de fluxo detalhado.

### <a name="declaring-output-data-type"></a>Declarando tipo de dados de saída

Um fluxo de trabalho pode especificar o tipo de dados das respetivas saídas ao utilizar o [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Este atributo não tem qualquer efeito durante o tempo de execução, mas ela fornece uma indicação para o autor do runbook no tempo de design de saída prevista do runbook. Como o conjunto de ferramentas para runbooks continua a evoluir, a importância de declarar tipos de dados de saída em tempo de design aumenta em importância. Como resultado, é uma prática recomendada incluir essa declaração em todos os runbooks que você criar.

Aqui está uma lista de tipos de saída de exemplo:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

O runbook de exemplo seguintes produz um objeto de cadeia de caracteres e inclui uma declaração do respetivo tipo de saída. Se o seu runbook devolve uma matriz de um determinado tipo, em seguida, ainda deverá especificar o tipo em vez de uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Para declarar um tipo de saída em runbooks de fluxo de trabalho gráfico ou gráfico do PowerShell, você pode selecionar a opção de menu **entrada e saída** e digitar o nome do tipo de saída. É recomendável que você use o nome completo da classe .NET para torná-lo facilmente identificável ao fazer referência a ele de um runbook pai. Isso expõe todas as propriedades dessa classe para o barramento de dados no runbook e fornece muita flexibilidade ao usá-las para lógica condicional, registro em log e referência como valores para outras atividades no runbook.<br> ![Opção de entrada e saída do runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

No exemplo a seguir, você tem dois runbooks gráficos para demonstrar esse recurso. Se você aplicar o modelo de design modular runbook, terá um runbook, que serve como o *modelo de runbook de autenticação* Gerenciando a autenticação com o Azure usando a conta Executar como. Nosso segundo runbook, que normalmente executaria a lógica principal para automatizar um determinado cenário, nesse caso, irá executar o modelo de *runbook de autenticação* e exibir os resultados para o painel de saída de **teste** . Em circunstâncias normais, esse runbook faria algo em relação a um recurso que aproveita a saída do runbook filho.

Aqui está a lógica básica do runbook **authenticateto-Azure** .<br> ![Exemplo](media/automation-runbook-output-and-messages/runbook-authentication-template.png)de autenticar modelo de runbook.

Ele inclui o tipo de saída *Microsoft. Azure. Commands. Profile. Models. PSAzureContext*, que retorna as propriedades do perfil de autenticação.<br> ![Exemplo de tipo de saída de runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Embora esse runbook seja direto, há um item de configuração para chamar aqui. A última atividade está executando o cmdlet **Write-Output** e grava os dados de perfil em uma variável $ _ usando uma expressão do PowerShell para o parâmetro InputObject, que é necessário para esse cmdlet.

Para o segundo runbook neste exemplo, chamado *Test-ChildOutputType*, você simplesmente tem duas atividades.<br> ![Exemplo de runbook de tipo de saída filho](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

A primeira atividade chama o runbook **autenticato-Azure** e a segunda atividade está executando o **cmdlet Write-Verbose** com a fonte de **dados** da saída da **atividade** e o valor para o caminho do **campo** é  **Context. Subscription. Subscriptionname**, que está especificando a saída de contexto do runbook **autenticato-Azure** .<br> ![Fonte de dados de parâmetro de cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

A saída resultante é o nome da assinatura.<br> ![Resultados do runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

> [!NOTE]
> Depois de inserir um valor na caixa **tipo de saída** no painel **Propriedades de entrada e saída** , você precisa clicar fora do controle para que sua entrada possa ser reconhecida pelo controle.

## <a name="message-streams"></a>Fluxos de mensagens

Ao contrário do fluxo de saída, fluxos de mensagens destinam-se para comunicar informações ao usuário. Há vários fluxos de mensagens para diferentes tipos de informações, e cada um é tratado de forma diferente pela automação do Azure.

### <a name="warning-and-error-streams"></a>Fluxos de aviso e de erro

Os fluxos de avisos e erros destinam-se a registar problemas que ocorrem num runbook. Eles são gravados no histórico de trabalho quando um runbook é executado e são incluídos no painel de saída de teste no portal do Azure quando um runbook é testado. Por predefinição, o runbook continuará a executar após um aviso ou erro. Pode especificar que o runbook deve ser suspenso após um aviso ou erro ao definir uma [variável de preferência](#preference-variables) no runbook antes de criar a mensagem. Por exemplo, para fazer com que um runbook suspenda um erro como seria uma exceção, defina **$ErrorActionPreference** para parar.

Criar um aviso ou erro mensagem através da [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet. Atividades também podem escrever nestes fluxos.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Fluxo verboso

O fluxo de mensagens verbosas destina-se a informações gerais sobre o funcionamento do runbook. Como o [fluxo de depuração](#debug-stream) não está disponível em um runbook, as mensagens detalhadas devem ser usadas para informações de depuração. Por padrão, as mensagens detalhadas de runbooks publicados não são armazenadas no histórico de trabalhos. Para armazenar mensagens detalhadas, configure runbooks publicados para registros detalhados de log na guia Configurar do runbook no portal do Azure. Na maioria dos casos, deve manter a predefinição de não criar registos verbosos dos runbooks por motivos de desempenho. Ative esta opção apenas para solucionar problemas ou depurar um runbook.

Ao [testar um runbook](automation-testing-runbook.md), as mensagens detalhadas não são exibidas mesmo se o runbook estiver configurado para registrar em log os registros detalhados. Para exibir mensagens detalhadas durante [o teste de um runbook](automation-testing-runbook.md), você deve definir a variável $VerbosePreference como continuar. Com esse conjunto de variáveis, as mensagens detalhadas são exibidas no painel saída de teste do portal do Azure.

Criar um através de mensagens verbosas a [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Fluxo de depuração

O fluxo de depuração destina-se para utilização com um utilizador interativo e não deve ser utilizado em runbooks.

## <a name="progress-records"></a>Registos de progresso

Se você configurar um runbook para registrar em log os registros de andamento (na guia Configurar do runbook no portal do Azure), um registro será gravado no histórico do trabalho antes e depois que cada atividade for executada. Na maioria dos casos, deve manter a predefinição de não criar registos de progresso de um runbook para maximizar o desempenho. Ative esta opção apenas para solucionar problemas ou depurar um runbook. Ao testar um runbook, não são apresentadas mensagens de progresso, mesmo que o runbook esteja configurado para registos de progressos.

O cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) não é válido em um runbook, pois esse cmdlet destina-se ao uso com um usuário interativo.

## <a name="preference-variables"></a>Variáveis de preferência

Windows PowerShell usa [variáveis de preferência](https://technet.microsoft.com/library/hh847796.aspx) para determinar como responder a dados enviados para diferentes fluxos de saída. Você pode definir essas variáveis em um runbook para controlar como ele responde aos dados enviados em diferentes fluxos.

A tabela seguinte lista as variáveis de preferências que podem ser utilizadas em runbooks com seus válido e os valores predefinidos. Essa tabela inclui apenas os valores que são válidos em um runbook. Valores adicionais são válidos para as variáveis de preferência quando usados no Windows PowerShell fora da automação do Azure.

| Variável | Valor Predefinido | Valores válidos |
|:--- |:--- |:--- |
| WarningPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| ErrorActionPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Parar<br>Continuar<br>SilentlyContinue |

A tabela seguinte lista o comportamento para os valores das variáveis de preferência que são válidos em runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continuar |Regista a mensagem e continua a executar o runbook. |
| SilentlyContinue |Continua a executar o runbook sem registar a mensagem. Esse valor tem o efeito de ignorar a mensagem. |
| Parar |Regista a mensagem e suspende o runbook. |

## <a name="runbook-output"></a>Recuperando mensagens e saída do runbook

### <a name="azure-portal"></a>Portal do Azure

Você pode exibir os detalhes de um trabalho de runbook na portal do Azure na guia trabalhos de um runbook. O resumo do trabalho exibe os parâmetros de entrada e o [fluxo de saída](#output-stream) , além das informações gerais sobre o trabalho e quaisquer exceções, se elas ocorreram. O histórico inclui mensagens do [fluxo de saída](#output-stream) e dos [fluxos de aviso e de erro](#warning-and-error-streams) , bem como os [registros de andamento](#progress-records) e [fluxo detalhados](#verbose-stream) se o runbook estiver configurado para registrar em log os registros detalhados e de andamento.

### <a name="windows-powershell"></a>Windows PowerShell

No Windows PowerShell, você pode recuperar a saída e as mensagens de um runbook usando o cmdlet [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) . Esse cmdlet requer a ID do trabalho e tem um parâmetro chamado Stream, em que você especifica qual fluxo deve ser retornado. Pode especificar **qualquer** para retornar todos os fluxos da tarefa.

O exemplo seguinte inicia um runbook de exemplo e, em seguida, aguarda que este seja concluído. Depois de concluído, o seu fluxo de saída é recolhido da tarefa.

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

### <a name="graphical-authoring"></a>Criação gráfica

Para runbooks gráficos, o log extra está disponível na forma de rastreamento de nível de atividade. Há dois níveis de rastreamento: Básico e detalhado. No rastreamento básico, você pode ver a hora de início e de término de cada atividade no runbook mais informações relacionadas a qualquer nova tentativa de atividade. Alguns exemplos são, o número de tentativas e a hora de início da atividade. No rastreamento detalhado, você obtém o rastreamento básico, além de dados de entrada e saída para cada atividade. Atualmente, os registros de rastreamento são gravados usando o fluxo detalhado, portanto, você deve habilitar o log detalhado ao habilitar o rastreamento. Para runbooks gráficos com o rastreamento habilitado, não é necessário registrar em log os registros de andamento. O rastreamento básico tem a mesma finalidade e é mais informativo.

![Exibição de fluxos de trabalho de criação gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Você pode ver na captura de tela anterior que, ao habilitar o log detalhado e o rastreamento para runbooks gráficos, muito mais informações estão disponíveis no modo de exibição fluxos de trabalho de produção. Essas informações extras podem ser essenciais para solucionar problemas de produção com um runbook e, portanto, você deve habilitá-lo apenas para essa finalidade e não como uma prática geral. Os registros de rastreamento podem ser especialmente numerosos. Com o rastreamento de runbook gráfico, você pode obter de dois a quatro registros por atividade, dependendo se você configurou o rastreamento básico ou detalhado. A menos que você precise dessas informações para acompanhar o progresso de um runbook para solução de problemas, talvez você queira manter o rastreamento desativado.

**Para habilitar o rastreamento em nível de atividade, execute as seguintes etapas:**

1. No portal do Azure, abra a sua conta da Automatização.
2. Em **automação de processo**, selecione **Runbooks** para abrir a lista de runbooks.
3. Na página Runbooks, clique para selecionar um runbook gráfico na lista de runbooks.
4. Em **configurações**, clique em **registro em log e rastreamento**.
5. Na página log e rastreamento, em registros detalhados de log, clique **em** ativar para habilitar o log detalhado e, em rastreamento de nível de atividade, altere o nível de rastreamento para **básico** ou **detalhado** com base no nível de rastreamento que você precisa.<br>

   ![Página de rastreamento e log de criação gráfica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure monitorar logs

A automação pode enviar o status do trabalho de runbook e fluxos de trabalho para seu espaço de trabalho Log Analytics. Com os logs de Azure Monitor que você pode,

* Obtenha informações sobre seus trabalhos de automação
* Disparar um email ou alerta com base no status do trabalho de runbook (por exemplo, com falha ou suspenso)
* Escrever consultas avançadas em seus fluxos de trabalho
* Correlacionar trabalhos entre contas de automação
* Visualizar seu histórico de trabalho ao longo do tempo

Para obter mais informações sobre como configurar a integração com os logs de Azure Monitor para coletar, correlacionar e agir sobre os dados do trabalho, consulte [encaminhar fluxos de trabalho e status do trabalho da automação para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md)
* Para entender como projetar e usar runbooks filho, consulte [Runbooks filho na automação do Azure](automation-child-runbooks.md)
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](/powershell/scripting/overview).
