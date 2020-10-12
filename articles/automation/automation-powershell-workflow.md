---
title: Aprenda fluxo de trabalho PowerShell para Azure Automation
description: Este artigo ensina-lhe as diferenças entre o Fluxo de Trabalho PowerShell e o PowerShell e os conceitos aplicáveis aos livros de automação.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: f175e495af8e925c0d5a6c61669a5e2f44f73ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186010"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Aprenda fluxo de trabalho PowerShell para Azure Automation

Os runbooks em Azure Automation são implementados como fluxos de trabalho Windows PowerShell, scripts Windows PowerShell que utilizam a Windows Workflow Foundation. Um fluxo de trabalho é uma sequência de passos programados e ligados que efetuam tarefas de longa execução ou requerem a coordenação de vários passos em vários dispositivos ou nós geridos. 

Enquanto um fluxo de trabalho é escrito com a sintaxe Do Windows PowerShell e lançado pelo Windows PowerShell, é processado pela Windows Workflow Foundation. Os benefícios de um fluxo de trabalho sobre um script normal incluem o desempenho simultâneo de uma ação contra vários dispositivos e a recuperação automática de falhas. 

> [!NOTE]
> Um script PowerShell Workflow é muito semelhante a um script Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo utilizador. Por isso, recomendamos que escreva os seus livros de execução utilizando o Fluxo de Trabalho PowerShell [apenas](#use-checkpoints-in-a-workflow)se precisar de utilizar postos de controlo . 

Para obter detalhes completos sobre os tópicos neste artigo, consulte "Começar com o Fluxo de [Trabalho do Windows PowerShell".](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))

## <a name="use-workflow-keyword"></a>Use a palavra-chave do fluxo de trabalho

O primeiro passo para converter um script PowerShell num fluxo de trabalho PowerShell é a sua conversão com a `Workflow` palavra-chave. Um fluxo de trabalho começa com a `Workflow` palavra-chave seguida pelo corpo do script incluído em aparelhos. O nome do fluxo de trabalho segue a `Workflow` palavra-chave como mostrado na seguinte sintaxe:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho deve corresponder ao nome do livro de trabalho Automation. Se o livro de recortes estiver a ser importado, o nome do ficheiro deve corresponder ao nome do fluxo de trabalho e terminar em **.ps1**.

Para adicionar parâmetros ao fluxo de trabalho, use a `Param` palavra-chave tal como faria num script.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Saiba diferenças entre o código de fluxo de trabalho PowerShell e o código de script PowerShell

O código de fluxo de trabalho PowerShell parece quase idêntico ao código de script PowerShell, exceto algumas alterações significativas. As secções seguintes descrevem as alterações que precisa de fazer a um script PowerShell para que possa ser executada num fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica num fluxo de trabalho que é realizado numa sequência. A execução de um Fluxo de Trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell em atividades. Quando especificar um destes cmdlets no seu runbook, a atividade correspondente é gerida pela Windows Workflow Foundation. 

Se um cmdlet não tiver atividade correspondente, o fluxo de trabalho do Windows PowerShell executa automaticamente o cmdlet numa atividade [InlineScript.](#use-inlinescript) Alguns cmdlets estão excluídos e não podem ser utilizados num fluxo de trabalho a menos que os inclua explicitamente num bloco InlineScript. Para obter mais informações, consulte [a Utilização de Atividades em Fluxos de Trabalho de Script](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

As atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns que permitem configurar o funcionamento delas. Ver [about_WorkflowCommonParameters.](/powershell/module/psworkflow/about/about_workflowcommonparameters)

### <a name="positional-parameters"></a>Parâmetros posicionais

Não pode utilizar parâmetros posicionais com atividades e cmdlets num fluxo de trabalho. Portanto, deve usar nomes de parâmetros. Considere o seguinte código que obtém todos os serviços de execução:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se tentar executar este código num fluxo de trabalho, recebe uma mensagem como `Parameter set cannot be resolved using the specified named parameters.` Para corrigir este problema, forneça o nome do parâmetro, como no seguinte exemplo:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos deserizados

Os objetos em fluxos de trabalho são desseerizados, o que significa que as suas propriedades ainda estão disponíveis, mas não os seus métodos.  Por exemplo, considere o seguinte código PowerShell, que para um serviço utilizando o `Stop` método do `Service` objeto.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se tentar executá-lo num fluxo de trabalho, recebe um erro dizendo `Method invocation is not supported in a Windows PowerShell Workflow.`

Uma opção é embrulhar estas duas linhas de código num bloco [InlineScript.](#use-inlinescript) Neste caso, `Service` representa um objeto de serviço dentro do bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é usar outro cmdlet que tenha a mesma funcionalidade que o método, se estiver disponível. No nosso exemplo, o `Stop-Service` cmdlet fornece a mesma funcionalidade que o `Stop` método, e pode utilizar o seguinte código para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Utilizar InlineScript

A `InlineScript` atividade é útil quando precisa executar um ou mais comandos como script tradicional PowerShell em vez de fluxo de trabalho PowerShell.  Enquanto os comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, os comandos num bloco InlineScript são processados pelo Windows PowerShell.

O InlineScript utiliza a seguinte sintaxe mostrada abaixo.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Pode devolver a saída de um InlineScript atribuindo a saída a uma variável. O exemplo a seguir para um serviço e, em seguida, produz o nome de serviço.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Pode passar valores para um bloco InlineScript, mas tem de utilizar **$Using** modificador de âmbito.  O exemplo a seguir é idêntico ao exemplo anterior, exceto que o nome de serviço é fornecido por uma variável.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Embora as atividades do InlineScript possam ser críticas em certos fluxos de trabalho, não suportam construções de fluxos de trabalho. Deve usá-las apenas quando necessário pelas seguintes razões:

* Não é possível utilizar [pontos de verificação](#use-checkpoints-in-a-workflow) dentro de um bloco InlineScript. Se ocorrer uma falha dentro do bloco, deve ser retomado a partir do início do bloco.
* Não é possível utilizar [a execução paralela](#use-parallel-processing) dentro de um bloco InlineScript.
* O InlineScript afeta a escalabilidade do fluxo de trabalho uma vez que detém a sessão Windows PowerShell durante todo o comprimento do bloco InlineScript.

Para obter mais informações sobre a utilização do InlineScript, consulte [os Comandos PowerShell do Windows em fluxo de trabalho](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) e [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Utilizar processamento paralelo

Uma das vantagens dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos paralelamente, em vez de sequencialmente como acontece com um script normal.

Pode utilizar a `Parallel` palavra-chave para criar um bloco de scripts com vários comandos que funcionam simultaneamente. Isto utiliza a seguinte sintaxe mostrada abaixo. Neste caso, a Atividade1 e a Atividade2 começam ao mesmo tempo. A atividade 3 só começa depois de concluída a Atividade1 e a Atividade2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos PowerShell que copiam vários ficheiros para um destino de rede. Estes comandos são executados sequencialmente para que um ficheiro termine de copiar antes do início do próximo.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

O fluxo de trabalho que se segue executa estes mesmos comandos em paralelo para que todos comecem a copiar ao mesmo tempo.  Só depois de todas serem copiadas é que a mensagem de conclusão é exibida.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Pode utilizar a `ForEach -Parallel` construção para processar comandos para cada item numa coleção em simultâneo. Os itens na coleção são processados paralelamente enquanto os comandos no bloco de scripts são executados sequencialmente. Este processo utiliza a seguinte sintaxe mostrada abaixo. Neste caso, a Activity1 começa ao mesmo tempo para todos os itens da coleção. Para cada item, a Activity2 começa depois da Atividade1 estar completa. A atividade3 só começa depois de tanto a Atividade1 como a Activity2 terem concluído para todos os itens. Usamos o `ThrottleLimit` parâmetro para limitar o paralelismo. Um pouco alto `ThrottleLimit` pode causar problemas. O valor ideal para o `ThrottleLimit` parâmetro depende de muitos fatores no seu ambiente. Comece com um valor baixo e tente diferentes valores de aumento até encontrar um que funcione para a sua circunstância específica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo a seguir é semelhante ao exemplo anterior que copia ficheiros em paralelo.  Neste caso, é apresentada uma mensagem para cada ficheiro depois de cópias.  Só depois de todas copiadas é que a mensagem final de conclusão é exibida.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Não recomendamos a execução de livros para crianças em paralelo, uma vez que este foi demonstrado para dar resultados pouco fiáveis. A saída do livro de crianças às vezes não aparece, e as configurações num livro de ensaios para crianças podem afetar os outros livros paralelos para crianças. Variáveis `VerbosePreference` `WarningPreference` como, e outras, podem não se propagar aos livros infantis. E se o livro de crianças alterar estes valores, estes podem não ser devidamente restaurados após a invocação.

## <a name="use-checkpoints-in-a-workflow"></a>Utilize postos de controlo num fluxo de trabalho

Um ponto de verificação é um instantâneo do estado atual do fluxo de trabalho que inclui os valores atuais para variáveis e qualquer saída gerada até esse ponto. Se um fluxo de trabalho termina por engano ou é suspenso, começa a partir do seu último ponto de verificação da próxima vez que funcionar, em vez de começar no início. 

Pode definir um ponto de verificação num fluxo de trabalho com a `Checkpoint-Workflow` atividade. A Azure Automation tem uma funcionalidade chamada [fair share](automation-runbook-execution.md#fair-share), para a qual qualquer livro de execução que tenha uma duração de três horas é descarregado para permitir a execução de outros runbooks. Eventualmente, o livro de bordo descarregado é recarregado. Quando estiver, retoma a execução do último ponto de verificação tirado no livro de recortes.

Para garantir que o livro de bordo eventualmente se completa, deve adicionar pontos de verificação em intervalos que duram menos de três horas. Se durante cada execução for adicionado um novo ponto de verificação, e se o livro de execuções for despejado após três horas devido a um erro, o livro de recortes é retomado indefinidamente.

No exemplo seguinte, ocorre uma exceção após a Atividade2, fazendo com que o fluxo de trabalho termine. Quando o fluxo de trabalho é executado novamente, começa por executar a Atividade2, uma vez que esta atividade foi logo após o último checkpoint definido.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Definir postos de controlo num fluxo de trabalho após atividades que possam ser propensas a exceções e não devem ser repetidos se o fluxo de trabalho for retomado. Por exemplo, o seu fluxo de trabalho pode criar uma máquina virtual. Pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, os comandos são repetidos se o fluxo de trabalho for reiniciado. Se o fluxo de trabalho falhar após o sucesso da criação, a máquina virtual não voltará a ser criada quando o fluxo de trabalho for retomado.

O exemplo seguinte copia vários ficheiros para uma localização de rede e define um ponto de verificação após cada ficheiro.  Se a localização da rede for perdida, o fluxo de trabalho termina por engano.  Quando é reinicia, retoma no último posto de controlo. Apenas os ficheiros que já foram copiados são ignorados.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Como as credenciais de nome de utilizador não são persistidos depois de ligar para a atividade suspender o [fluxo de trabalho](/powershell/module/psworkflow/about/about_suspend-workflow) ou após o último ponto de verificação, é necessário definir as credenciais para nulos e, em seguida, recuperá-las novamente da loja de ativos depois ou o ponto de `Suspend-Workflow` verificação é chamado.  Caso contrário, poderá receber a seguinte mensagem de erro: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

O mesmo código que se segue demonstra como lidar com esta situação nos seus livros de fluxo de trabalho PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Para livros de execução powershell não gráficos, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Poderá ter de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation. A utilização destes cmdlets não é necessária se estiver a autenticar utilizando uma conta Run As configurada com um titular de serviço.

Para obter mais informações sobre os pontos de verificação, consulte [adicionar pontos de verificação a um fluxo de trabalho do script](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os livros de fluxo de trabalho powerShell, consulte [Tutorial: Crie um manual de fluxo de trabalho PowerShell](learn/automation-tutorial-runbook-textual.md).
