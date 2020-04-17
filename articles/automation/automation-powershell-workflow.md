---
title: Learning PowerShell Workflow for Azure Automation
description: Este artigo pretende ser uma lição rápida para autores familiarizados com a PowerShell entenderem as diferenças específicas entre powerShell e PowerShell Workflow e conceitos aplicáveis aos livros de execução automation.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457540"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Conceitos de workflow windows PowerShell para livros de automação

Os livros de execução em Automação Azure são implementados como Fluxos de Trabalho Windows PowerShell.  Um Workflow Do Windows PowerShell é semelhante a um script Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo utilizador.  Embora este artigo se destine a ajudá-lo a escrever livros de execução usando o fluxo de trabalho powerShell, recomendamos que escreva livros de execução usando powerShell, a menos que precise de pontos de verificação.  Existem várias diferenças de sintaxe quando se escreve manuais de powerShell Workflow e estas diferenças requerem um pouco mais de trabalho para escrever fluxos de trabalho eficazes.

Um fluxo de trabalho é uma sequência de passos programados e ligados que efetuam tarefas de longa execução ou requerem a coordenação de vários passos em vários dispositivos ou nós geridos. Nas vantagens que um fluxo de trabalho tem em relação a um script normal incluem-se a capacidade de efetuar simultaneamente uma ação em vários dispositivos e a capacidade de recuperar automaticamente de falhas. Um Windows PowerShell Workflow é um script Windows PowerShell que utiliza a Fundação Windows Workflow. O fluxo de trabalho é processado pelo Windows Workflow Foundation ao mesmo tempo que está a ser escrito com a sintaxe do Windows PowerShell e lançado pelo Windows PowerShell.

Para mais detalhes sobre os tópicos deste artigo, consulte [Getting Started with Windows PowerShell Workflow](https://technet.microsoft.com/library/jj134242.aspx).

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho

O primeiro passo para converter um script PowerShell num fluxo de `Workflow` trabalho PowerShell é engrossá-lo com a palavra-chave.  Um fluxo de `Workflow` trabalho começa com a palavra-chave seguida pelo corpo do script fechado em aparelhos. O nome do fluxo `Workflow` de trabalho segue a palavra-chave, como mostra a seguinte sintaxe:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho deve coincidir com o nome do livro de execução automation. Se o livro de execução estiver a ser importado, o nome de ficheiro deve coincidir com o nome do fluxo de trabalho e deve terminar em *.ps1*.

Para adicionar parâmetros ao fluxo `Param` de trabalho, use a palavra-chave tal como faria num guião.

## <a name="code-changes"></a>Alterações do código

O código de fluxo de trabalho PowerShell parece quase idêntico ao código de script PowerShell, exceto algumas alterações significativas.  As seguintes secções descrevem alterações que precisa de fazer para um script PowerShell para que possa funcionar num fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica num fluxo de trabalho. Tal como um script é composto por um ou mais comandos, um fluxo de trabalho é composto por uma ou mais atividades que são executadas numa sequência. A execução de um Fluxo de Trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell em atividades. Quando especifica um destes cmdlets no seu livro de execução, a atividade correspondente é gerida pela Fundação Windows Workflow. Para aqueles cmdlets sem uma atividade correspondente, o Fluxo de Trabalho do Windows PowerShell executa automaticamente o cmdlet dentro de uma atividade [InlineScript.](#inlinescript) Existe um conjunto de cmdlets que são excluídos e não podem ser usados num fluxo de trabalho a menos que os inclua explicitamente num bloco InlineScript. Para mais detalhes sobre estes conceitos, consulte [A Utilização de Atividades em Fluxos](https://technet.microsoft.com/library/jj574194.aspx)de Trabalho de Script .

As atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns que permitem configurar o funcionamento delas. Para mais detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais

Não é possível utilizar parâmetros posicionais com atividades e cmdlets num fluxo de trabalho.  Tudo isto significa que tens de usar nomes de parâmetros.

Por exemplo, considere o seguinte código que obtém todos os serviços de funcionamento.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se tentar executar este mesmo código num fluxo de trabalho, recebe uma mensagem como "O conjunto de parâmetros não pode ser resolvido utilizando os parâmetros nomeados especificados."  Para corrigir isto, forneça o nome do parâmetro como no seguinte.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos desserializados

Objetos em fluxos de trabalho são desserializados.  Isto significa que as suas propriedades ainda estão disponíveis, mas não os seus métodos.  Por exemplo, considere o seguinte código PowerShell que para um serviço utilizando o método Stop do objeto De serviço.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se tentar executá-lo num fluxo de trabalho, recebe um erro dizendo que "A invocação do método não é suportada num Fluxo de Trabalho do Windows PowerShell."

Uma opção é embrulhar estas duas linhas de código num bloco [InlineScript,](#inlinescript) caso em que $Service seria um objeto de serviço dentro do bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é utilizar outro cmdlet que execute a mesma funcionalidade que o método, se um estiver disponível.  Na nossa amostra, o cmdlet stop-service fornece a mesma funcionalidade que o método Stop, e você poderia usar o seguinte para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

A`InlineScript` atividade é útil quando precisa executar um ou mais comandos como script tradicional PowerShell em vez de PowerShell workflow.  Enquanto os comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, os comandos num bloco InlineScript são processados pelo Windows PowerShell.

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

Embora as atividades do InlineScript possam ser críticas em certos fluxos de trabalho, não suportam as construções de fluxo de trabalho e só devem ser utilizadas quando necessário pelas seguintes razões:

* Não é possível utilizar pontos de [verificação](#checkpoints) dentro de um bloco InlineScript. Se ocorrer uma falha dentro do bloco, deve ser retomada a partir do início do bloco.
* Não é possível utilizar [uma execução paralela](#parallel-processing) dentro de um InlineScriptBlock.
* O InlineScript afeta a escalabilidade do fluxo de trabalho uma vez que detém a sessão Do Windows PowerShell durante todo o comprimento do bloco InlineScript.

Para obter mais informações sobre a utilização do InlineScript, consulte [executar comandos PowerShell do Windows num fluxo de trabalho](https://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Processamento paralelo

Uma das vantagens dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos paralelamente, em vez de sequencialmente como acontece com um script normal.

Pode usar `Parallel` a palavra-chave para criar um bloco de scripts com vários comandos que funcionam simultaneamente. Isto utiliza a seguinte sintaxe mostrada abaixo. Neste caso, a Atividade1 e Atividade2 começa ao mesmo tempo. A atividade 3 só começa depois de a Atividade1 e a Atividade2 terem concluído.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos PowerShell que copiam vários ficheiros para um destino de rede.  Estes comandos são executados sequencialmente de modo que um ficheiro deve terminar a cópia antes do próximo ser iniciado.

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

Pode utilizar `ForEach -Parallel` a construção para processar comandos para cada item numa coleção simultaneamente. Os itens na coleção são processados paralelamente enquanto os comandos no bloco de scripts são executados sequencialmente. Isto utiliza a seguinte sintaxe mostrada abaixo. Neste caso, a Atividade1 começa ao mesmo tempo para todos os itens da coleção. Para cada item, a Atividade2 começa após a Atividade1 estar completa. A Atividade3 só começa depois de tanto a Atividade1 como a Atividade2 terem concluído para todos os itens. Usamos o `ThrottleLimit` parâmetro para limitar o paralelismo. Uma `ThrottleLimit` pessoa demasiado alta pode causar problemas. O valor ideal `ThrottleLimit` para o parâmetro depende de muitos fatores no seu ambiente. Deve tentar começar com um valor baixo e tentar diferentes valores crescentes até encontrar um que funcione para a sua circunstância específica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo seguinte é semelhante ao exemplo anterior que copia ficheiros em paralelo.  Neste caso, é apresentada uma mensagem para cada ficheiro após a cópia.  Só depois de todos serem completamente copiados é a mensagem final de conclusão exibida.

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
> Não recomendamos que a execução de livros infantis seja paralela, uma vez que se tem demonstrado que isto dá resultados pouco fiáveis. A saída do livro de corridas para crianças por vezes não aparece, e as configurações de um livro infantil podem afetar os outros livros paralelos para crianças. Variáveis como $VerbosePreference, $WarningPreference e outras não podem ser propagadas aos livros infantis. E se o livro de rés-do-criança alterar estes valores, podem não ser devidamente restaurados após a invocação.

## <a name="checkpoints"></a>Pontos de verificação

Um *checkpoint* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual para variáveis e qualquer saída gerada até esse ponto. Se um fluxo de trabalho terminar por engano ou for suspenso, então da próxima vez que for executado, começará a partir do seu último ponto de verificação em vez do início do fluxo de trabalho.  Pode definir um ponto de verificação num fluxo de trabalho com a `Checkpoint-Workflow` atividade. A Azure Automation tem uma funcionalidade chamada [fair share,](automation-runbook-execution.md#fair-share)onde qualquer livro de execução que funciona durante 3 horas é descarregado para permitir que outros livros de execução possam ser executados. Eventualmente, o livro de execução descarregado será recarregado, e quando estiver, retomará a execução a partir do último posto de controlo tomado no livro de execução. Para garantir que o livro de execução irá eventualmente completo, deve adicionar pontos de verificação em intervalos que se esgotem durante menos de 3 horas. Se durante cada execução for adicionado um novo ponto de verificação e se o livro de execução for despejado após 3 horas devido a um erro, então o livro de execução será retomado indefinidamente.

No seguinte código de amostra, ocorre uma exceção após a Atividade2, fazendo com que o fluxo de trabalho termine. Quando o fluxo de trabalho é executado novamente, começa por executar a Atividade2 uma vez que este foi logo após o último conjunto de ponto de verificação.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Deve fixar postos de controlo num fluxo de trabalho após atividades que possam ser suscetíveis de ser excededas e não devem ser repetidas se o fluxo de trabalho for retomado. Por exemplo, o seu fluxo de trabalho pode criar uma máquina virtual. É possível definir um ponto de verificação antes e depois dos comandos para criar uma máquina virtual. Se a criação falhar, os comandos serão repetidos se o fluxo de trabalho for reiniciado. Se o fluxo de trabalho falhar após o sucesso da criação, então a máquina virtual não será criada novamente quando o fluxo de trabalho for retomado.

O exemplo seguinte copia vários ficheiros para uma localização da rede e define um ponto de verificação após cada ficheiro.  Se a localização da rede for perdida, o fluxo de trabalho termina por engano.  Quando for reiniciado, será retomado no último ponto de verificação, o que significa que apenas os ficheiros que já foram copiados são ignorados.

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

Uma vez que as credenciais de nome de utilizador não são persistidas após ligar para a atividade de Fluxo `Suspend-Workflow` de Trabalho suspenso ou após o último checkpoint, é necessário definir as credenciais para anular e, em seguida, recuperá-las novamente da loja de ativos após o chamado ponto de verificação. [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx)  Caso contrário, poderá receber a seguinte mensagem de erro:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

O mesmo código que se segue demonstra como lidar com isto nos seus livros de workflow PowerShell.

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
> Para livros de execução powerShell não gráficos, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.


Isto não é necessário se estiver a autenticar usando uma conta Run As configurada com um diretor de serviço.

Para obter mais informações sobre os pontos de verificação, consulte a adição de pontos de [verificação a um fluxo de trabalho](https://technet.microsoft.com/library/jj574114.aspx)de script .

## <a name="next-steps"></a>Passos seguintes

* Para começar com os livros de workflow powerShell, consulte o meu primeiro livro de corridas de fluxo de [trabalho PowerShell](automation-first-runbook-textual.md)

