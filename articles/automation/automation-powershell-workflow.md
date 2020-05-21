---
title: Conheça o fluxo de trabalho powerShell para a automação azure
description: Este artigo ensina-lhe as diferenças entre powerShell Workflow e PowerShell e conceitos aplicáveis aos livros de execução automation.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6156b28a3baa51e84e2c46b74e63a6c8e81491cc
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715482"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Conheça o fluxo de trabalho powerShell para a automação azure

Os livros de execução em Automação Azure são implementados como fluxos de trabalho Windows PowerShell, scripts Windows PowerShell que utilizam a Fundação Windows Workflow. Um fluxo de trabalho é uma sequência de passos programados e ligados que efetuam tarefas de longa execução ou requerem a coordenação de vários passos em vários dispositivos ou nós geridos. 

Enquanto um fluxo de trabalho é escrito com sintaxe Windows PowerShell e lançado pelo Windows PowerShell, este é processado pela Windows Workflow Foundation. Os benefícios de um fluxo de trabalho sobre um script normal incluem o desempenho simultâneo de uma ação contra vários dispositivos e a recuperação automática de falhas. 

> [!NOTE]
> Um script powerShell Workflow é muito semelhante a um script Do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo utilizador. Por isso, recomendamos que escreva os seus livros de execução usando powerShell Workflow apenas se precisar de utilizar pontos de [verificação](#use-checkpoints-in-a-workflow). 

Para mais detalhes sobre os tópicos deste artigo, consulte [Getting Started with Windows PowerShell Workflow](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="use-workflow-keyword"></a>Use a palavra-chave workflow

O primeiro passo para converter um script PowerShell num fluxo de trabalho PowerShell é engrossá-lo com a `Workflow` palavra-chave. Um fluxo de trabalho começa com a `Workflow` palavra-chave seguida pelo corpo do script fechado em aparelhos. O nome do fluxo de trabalho segue a `Workflow` palavra-chave, como mostra a seguinte sintaxe:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho deve coincidir com o nome do livro de execução automation. Se o livro de execução estiver a ser importado, o nome do ficheiro deve coincidir com o nome do fluxo de trabalho e deve terminar em **.ps1**.

Para adicionar parâmetros ao fluxo de trabalho, use a `Param` palavra-chave tal como faria num guião.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Saiba diferenças entre o código powerShell Workflow e o código de script PowerShell

O código powerShell Workflow parece quase idêntico ao código de script PowerShell, exceto algumas alterações significativas. As seguintes secções descrevem alterações que precisa de fazer para um script PowerShell para que possa funcionar num fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica num fluxo de trabalho que é realizado numa sequência. A execução de um Fluxo de Trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell em atividades. Quando especifica um destes cmdlets no seu livro de execução, a atividade correspondente é gerida pela Fundação Windows Workflow. 

Se um cmdlet não tiver uma atividade correspondente, o Fluxo de Trabalho do Windows PowerShell executa automaticamente o cmdlet numa atividade [inlineScript.](#use-inlinescript) Alguns cmdlets são excluídos e não podem ser usados num fluxo de trabalho a menos que os inclua explicitamente num bloco InlineScript. Para mais informações, consulte [a utilização de Atividades em Fluxos](https://technet.microsoft.com/library/jj574194.aspx)de Trabalho de Script .

As atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns que permitem configurar o funcionamento delas. Veja [about_WorkflowCommonParameters.](https://technet.microsoft.com/library/jj129719.aspx)

### <a name="positional-parameters"></a>Parâmetros posicionais

Não é possível utilizar parâmetros posicionais com atividades e cmdlets num fluxo de trabalho. Portanto, deve usar nomes de parâmetros. Considere o seguinte código que obtém todos os serviços de funcionamento:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se tentar executar este código num fluxo de trabalho, recebe uma mensagem como `Parameter set cannot be resolved using the specified named parameters.` Corrigir para este problema, forneça o nome do parâmetro, como no seguinte exemplo:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos desserializados

Os objetos em fluxos de trabalho são desserializados, o que significa que as suas propriedades ainda estão disponíveis, mas não os seus métodos.  Por exemplo, considere o seguinte código PowerShell, que impede um serviço utilizando o `Stop` método do `Service` objeto.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se tentar escorrer isto num fluxo de trabalho, recebe um erro dizendo`Method invocation is not supported in a Windows PowerShell Workflow.`

Uma opção é embrulhar estas duas linhas de código num bloco [InlineScript.](#use-inlinescript) Neste caso, representa um objeto de `Service` serviço dentro do bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é utilizar outro cmdlet que tenha a mesma funcionalidade que o método, se um estiver disponível. No nosso exemplo, o `Stop-Service` cmdlet fornece a mesma funcionalidade que o `Stop` método, podendo utilizar o seguinte código para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Utilizar o InlineScript

A `InlineScript` atividade é útil quando precisa executar um ou mais comandos como script tradicional PowerShell em vez de PowerShell workflow.  Enquanto os comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, os comandos num bloco InlineScript são processados pelo Windows PowerShell.

O InlineScript utiliza a seguinte sintaxe mostrada abaixo.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Pode devolver a saída de um InlineScript atribuindo a saída a uma variável. O exemplo seguinte para um serviço e, em seguida, produz o nome do serviço.

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

Pode passar valores num bloco InlineScript, mas tem de utilizar **$Using** modificador de âmbito.  O exemplo que se segue é idêntico ao exemplo anterior, exceto que o nome do serviço é fornecido por uma variável.

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

Embora as atividades do InlineScript possam ser críticas em certos fluxos de trabalho, não suportam as construções de fluxo de trabalho. Só deve utilizá-las quando necessário pelas seguintes razões:

* Não é possível utilizar pontos de [verificação](#use-checkpoints-in-a-workflow) dentro de um bloco InlineScript. Se ocorrer uma falha dentro do bloco, deve ser retomado a partir do início do bloco.
* Não pode utilizar [execução paralela](#use-parallel-processing) dentro de um bloco InlineScript.
* O InlineScript afeta a escalabilidade do fluxo de trabalho uma vez que detém a sessão Do Windows PowerShell durante todo o comprimento do bloco InlineScript.

Para obter mais informações sobre a utilização do InlineScript, consulte [executar comandos PowerShell do Windows num fluxo de trabalho](https://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="use-parallel-processing"></a>Utilizar processamento paralelo

Uma das vantagens dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos paralelamente, em vez de sequencialmente como acontece com um script normal.

Pode usar a `Parallel` palavra-chave para criar um bloco de scripts com vários comandos que funcionam simultaneamente. Isto utiliza a seguinte sintaxe mostrada abaixo. Neste caso, a Atividade1 e Atividade2 começa ao mesmo tempo. A atividade 3 só começa depois de a Atividade1 e a Atividade2 terem concluído.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos PowerShell que copiam vários ficheiros para um destino de rede. Estes comandos são executados sequencialmente de modo que um ficheiro deve terminar a cópia antes do próximo ser iniciado.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

O fluxo de trabalho seguinte executa estes mesmos comandos em paralelo para que todos comecem a copiar ao mesmo tempo.  Só depois de todos copiados é a mensagem de conclusão exibida.

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

Pode utilizar a `ForEach -Parallel` construção para processar comandos para cada item numa coleção simultaneamente. Os itens na coleção são processados paralelamente enquanto os comandos no bloco de scripts são executados sequencialmente. Este processo utiliza a seguinte sintaxe mostrada abaixo. Neste caso, a Atividade1 começa ao mesmo tempo para todos os itens da coleção. Para cada item, a Atividade2 começa após a Atividade1 estar completa. A Atividade3 só começa depois de tanto a Atividade1 como a Atividade2 terem concluído para todos os itens. Usamos o `ThrottleLimit` parâmetro para limitar o paralelismo. Uma pessoa demasiado alta `ThrottleLimit` pode causar problemas. O valor ideal para o parâmetro depende de `ThrottleLimit` muitos fatores no seu ambiente. Comece com um valor baixo e experimente diferentes valores crescentes até encontrar um que funcione para a sua circunstância específica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo seguinte é semelhante ao exemplo anterior que copia ficheiros em paralelo.  Neste caso, é apresentada uma mensagem para cada ficheiro após a cópia.  Só depois de todos copiados é a mensagem final de conclusão exibida.

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
> Não recomendamos que a execução de livros infantis seja paralela, uma vez que se tem demonstrado que isto dá resultados pouco fiáveis. A saída do livro de corridas para crianças por vezes não aparece, e as configurações de um livro infantil podem afetar os outros livros paralelos para crianças. Variáveis `VerbosePreference` `WarningPreference` como, e outras, podem não se propagar aos livros infantis. E se o livro de crianças mudar estes valores, podem não ser devidamente restaurados após a invocação.

## <a name="use-checkpoints-in-a-workflow"></a>Utilize postos de controlo num fluxo de trabalho

Um checkpoint é um instantâneo do estado atual do fluxo de trabalho que inclui os valores atuais para variáveis e qualquer saída gerada até esse ponto. Se um fluxo de trabalho terminar em erro ou for suspenso, começa a partir do seu último posto de controlo da próxima vez que funcionar, em vez de começar no início. 

Pode definir um ponto de verificação num fluxo de trabalho com a `Checkpoint-Workflow` atividade. A Azure Automation tem uma funcionalidade chamada [fair share,](automation-runbook-execution.md#fair-share)para a qual qualquer livro de execução que funciona durante três horas é descarregado para permitir que outros livros de execução possam ser executados. Eventualmente, o livro de corridas descarregado é recarregado. Quando estiver, retoma a execução do último posto de controlo tomado no livro de corridas.

Para garantir que o livro de revolução acaba por ser concluído, deve adicionar pontos de verificação em intervalos que se esgotem durante menos de três horas. Se durante cada execução for adicionado um novo ponto de verificação e se o livro de execução for despejado após três horas devido a um erro, o livro de execução é retomado indefinidamente.

No exemplo seguinte, ocorre uma exceção após a Atividade2, fazendo com que o fluxo de trabalho termine. Quando o fluxo de trabalho é executado novamente, começa por executar a Atividade2, uma vez que esta atividade foi logo após o último conjunto de pontos de verificação.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Desloque os postos de controlo num fluxo de trabalho após atividades que possam ser suscetíveis de ser excededas e não devem ser repetidas se o fluxo de trabalho for retomado. Por exemplo, o seu fluxo de trabalho pode criar uma máquina virtual. Pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, os comandos são repetidos se o fluxo de trabalho for reiniciado. Se o fluxo de trabalho falhar após o sucesso da criação, a máquina virtual não será novamente criada quando o fluxo de trabalho for retomado.

O exemplo seguinte copia vários ficheiros para uma localização da rede e define um ponto de verificação após cada ficheiro.  Se a localização da rede for perdida, o fluxo de trabalho termina por engano.  Quando é reiniciado, recomeça no último posto de controlo. Só os ficheiros que já foram copiados são ignorados.

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

Uma vez que as credenciais de nome do utilizador não são persistidas após a chamada de utilização da atividade de Fluxo de Trabalho suspenso ou após o último ponto de verificação, é necessário definir as credenciais para anular e, em seguida, [recuperá-las](https://technet.microsoft.com/library/jj733586.aspx) novamente da loja de ativos após ou posto de `Suspend-Workflow` controlo é chamado.  Caso contrário, poderá receber a seguinte mensagem de erro:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

O mesmo código que se segue demonstra como lidar com esta situação nos seus livros de workflow PowerShell.

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
> Para livros de execução powerShell não gráficos, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation. A utilização destes cmdlets não é necessária se estiver a autenticar utilizando uma conta Run As configurada com um diretor de serviço.

Para obter mais informações sobre os pontos de verificação, consulte a adição de pontos de [verificação a um fluxo de trabalho](https://technet.microsoft.com/library/jj574114.aspx)de script .

## <a name="next-steps"></a>Próximos passos

* [O meu primeiro livro de fluxo de trabalho PowerShell](automation-first-runbook-textual.md)