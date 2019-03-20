---
title: Fluxo de trabalho do PowerShell para a automatização do Azure de aprendizagem
description: Este artigo destina-se como uma lição rápida para os autores familiarizados com o PowerShell para compreender as diferenças específicas entre PowerShell e o fluxo de trabalho do PowerShell e conceitos aplicáveis para runbooks de automatização.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c5764c36a646b9639c0eb6463c39b9f014c4272d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168090"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Aprender os principais conceitos de fluxo de trabalho do Windows PowerShell para runbooks de automatização

Os Runbooks na automatização do Azure são implementados como fluxos de trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas, que podem ser confusas para um novo utilizador.  Embora este artigo destina-se que o ajudarão a escrever runbooks com o fluxo de trabalho do PowerShell, recomendamos que escrever runbooks com o PowerShell, a menos que precisa de pontos de verificação.  Existem várias diferenças de sintaxe durante a criação de runbooks de fluxo de trabalho do PowerShell e essas diferenças requerem um pouco mais trabalho escrever fluxos de trabalho em vigor.

Um fluxo de trabalho é uma sequência de passos programados e ligados que efetuar tarefas de longa execução ou requerem a coordenação de vários passos em vários dispositivos ou nós geridos. As vantagens de um fluxo de trabalho ao longo de um script normal incluem a capacidade de efetuar simultaneamente uma ação em vários dispositivos e a capacidade de recuperar automaticamente de falhas. Um fluxo de trabalho do Windows PowerShell é um script do Windows PowerShell que utiliza o Windows Workflow Foundation. Enquanto o fluxo de trabalho é escrito com a sintaxe do Windows PowerShell e lançado pelo Windows PowerShell, é processada pelo Windows Workflow Foundation.

Para obter detalhes completos sobre os tópicos neste artigo, consulte [introdução ao fluxo de trabalho do Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho

O primeiro passo para converter um script do PowerShell num fluxo de trabalho do PowerShell é colocá-lo com o **fluxo de trabalho** palavra-chave.  Um fluxo de trabalho começa com o **fluxo de trabalho** palavra-chave seguida do corpo do script entre chavetas. O nome do fluxo de trabalho segue a **fluxo de trabalho** palavra-chave, como mostrado na seguinte sintaxe:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho tem de corresponder ao nome do runbook de automatização. Se o runbook está a ser importado, então o nome de ficheiro tem de corresponder ao nome de fluxo de trabalho e tem de terminar em *. ps1*.

Para adicionar parâmetros ao fluxo de trabalho, utilize o **Param** palavra-chave tal como faria para um script.

## <a name="code-changes"></a>Alterações do código

Código de fluxo de trabalho do PowerShell parece quase idêntico ao código de script do PowerShell, com exceção de algumas alterações significativas.  As secções seguintes descrevem as alterações que precisa fazer para um script do PowerShell para o mesmo para ser executado num fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica num fluxo de trabalho. Tal como um script é composto por um ou mais comandos, um fluxo de trabalho é composta por uma ou mais atividades que são executadas numa sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell em atividades quando é executada um fluxo de trabalho. Quando especificar um destes cmdlets no runbook, a atividade correspondente é executada pelo Windows Workflow Foundation. No caso dos cmdlets sem atividade correspondente, o fluxo de trabalho do Windows PowerShell executa automaticamente o cmdlet dentro de um [InlineScript](#inlinescript) atividade. Existe um conjunto de cmdlets que são excluídos e não pode ser utilizado num fluxo de trabalho, a menos que explicitamente incluídos num bloco InlineScript. Para obter mais detalhes sobre estes conceitos, veja [utilizar atividades em fluxos de trabalho de Script](https://technet.microsoft.com/library/jj574194.aspx).

Atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns para configurar o funcionamento deles. Para obter detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais

Não é possível utilizar parâmetros posicionais com atividades e cmdlets num fluxo de trabalho.  Isso significa que é que tem de utilizar os nomes de parâmetros.

Por exemplo, considere o seguinte código que obtém todos os serviços em execução.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se tentar executar esse código mesmo num fluxo de trabalho, receberá uma mensagem, como "conjunto de parâmetros não pode ser resolvido utilizando especificado parâmetros nomeados."  Para corrigir isto, forneça o nome de parâmetro como a seguir.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos de serialização anulados

Objetos em fluxos de trabalho são anular a serialização.  Isso significa que as respetivas propriedades ainda estão disponíveis, mas não os respetivos métodos.  Por exemplo, considere o seguinte código do PowerShell que pára um serviço usando o método Stop do objeto de serviço.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se tentar executá-lo num fluxo de trabalho, receberá um erro dizendo "invocação de método não é suportada num fluxo de trabalho do Windows PowerShell."

Uma opção é encapsular estas duas linhas de código num [InlineScript](#inlinescript) bloquear no qual caso $Service seria um objeto de serviço no bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é usar outro cmdlet que executa a mesma funcionalidade que o método, se disponível.  Em nosso exemplo, o cmdlet Stop-Service fornece a mesma funcionalidade que o método Stop e poderia usar o seguinte para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

O **InlineScript** atividade é útil quando tiver de executar um ou mais comandos de script do PowerShell como tradicional em vez de fluxo de trabalho do PowerShell.  Enquanto os comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, os comandos num bloco InlineScript são processados pelo Windows PowerShell.

O InlineScript utiliza a seguinte sintaxe indicada abaixo.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Pode retornar a saída de um InlineScript ao atribuir a saída a uma variável. O exemplo a seguir para um serviço e, em seguida, devolve o nome do serviço.

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

Pode passar valores para um bloco InlineScript, mas tem de utilizar **$Using** modificador do âmbito.  O exemplo seguinte é idêntico do exemplo anterior, exceto que o nome do serviço é fornecido por uma variável.

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

Embora as atividades de InlineScript possam ser fundamentais em determinados fluxos de trabalho, que não suportam construções de fluxo de trabalho e só devem ser utilizadas pelos seguintes motivos:

* Não é possível usar [pontos de verificação](#checkpoints) dentro do bloco InlineScript. Se ocorrer uma falha no bloco, é necessário recomeçar desde o início do bloco.
* Não é possível usar [execução paralela](#parallel-processing) dentro de um InlineScriptBlock.
* InlineScript afeta a escalabilidade do fluxo de trabalho, uma vez que realiza a sessão do Windows PowerShell durante todo o bloco InlineScript.

Para obter mais informações sobre como utilizar o InlineScript, consulte [executar comandos do Windows PowerShell num fluxo de trabalho](https://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Processamento paralelo

Uma das vantagens de fluxos de trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente como acontece com um script típico.

Pode utilizar o **paralela** palavra-chave para criar um bloco de scripts com vários comandos executados simultaneamente. Esta opção utiliza a seguinte sintaxe indicada abaixo. Neste caso, Activity1 e Activity2 inicia-se ao mesmo tempo. Activity3 começa apenas depois de Activity1 e Activity2 forem concluídos.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos do PowerShell que a cópia de vários ficheiros para um destino de rede.  Estes comandos são executados sequencialmente, para que um arquivo tem de concluir a cópia antes da próxima é iniciada.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

O seguinte fluxo de trabalho executa esses mesmos comandos em paralelo, para que todos eles começaram a copiar ao mesmo tempo.  Apenas depois de serem todos copiados é conclusão apresentada a mensagem.

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

Pode utilizar o **ForEach-Parallel** construção para processar comandos para cada item na coleção em simultâneo. Os itens na coleção são processados paralelamente enquanto os comandos no bloco de script são executados sequencialmente. Esta opção utiliza a seguinte sintaxe indicada abaixo. Neste caso, Activity1 inicia-se ao mesmo tempo para todos os itens na coleção. Para cada item, Activity2 começa depois de activity1 ter sido concluído. Activity3 começa apenas depois de Activity1 e Activity2 concluiu todos os itens. Usamos o `ThrottleLimit` parâmetro para limitar o paralelismo. Demasiado elevado um `ThrottleLimit` pode causar problemas. O valor ideal para o `ThrottleLimit` parâmetro depende de vários fatores no seu ambiente. Deve começar com um valor baixo de experimentar e tente aumentar os valores diferentes até encontrar uma que funcione para suas circunstâncias específicas.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo seguinte é semelhante ao exemplo anterior, copiar os ficheiros em paralelo.  Neste caso, é apresentada uma mensagem para cada ficheiro após copia.  Apenas depois de serem todos copiados completamente é conclusão final apresentada a mensagem.

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
> Não recomendamos a execução de runbooks subordinados em paralelo, uma vez que isso tenha sido mostrado para dar resultados pouco fiáveis. A saída do runbook subordinado, às vezes, não é exibido e as definições no runbook subordinado de um podem afetar outros runbooks subordinados paralela. As variáveis como $VerbosePreference, $WarningPreference e outros não podem ser propagadas para os runbooks subordinados. E se o runbook subordinado alterar estes valores, eles poderão não ser corretamente restaurados depois de invocação.

## <a name="checkpoints"></a>Pontos de Verificação

R *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual para variáveis e qualquer resultado gerado até esse ponto. Se um fluxo de trabalho termina dentro de erro ou está suspenso, em seguida, na próxima vez que for executada iniciará do último ponto de verificação, em vez do início do fluxo de trabalho.  Pode definir um ponto de verificação num fluxo de trabalho com o **Checkpoint-Workflow** atividade. A automatização do Azure tem um recurso chamado [justa](automation-runbook-execution.md#fair-share), onde qualquer runbook que seja executada durante 3 horas é descarregado para permitir a execução de outros runbooks. Finalmente, o runbook descarregado irá ser recarregado e quando é, retomará a execução do último ponto de verificação decorrido no runbook. Para garantir que o runbook, eventualmente, será concluído, tem de adicionar pontos de verificação em intervalos que são executadas durante menos de 3 horas. Se durante cada execução é adicionado um novo ponto de verificação e, se o runbook obtém expulso depois de 3 horas devido a um erro, em seguida, o runbook vai ser retomado indefinidamente.

No código de exemplo a seguir, uma exceção ocorrer após Activity2 causando o fluxo de trabalho terminar. Quando o fluxo de trabalho é executado novamente, começa por executar Activity2 porque foi apenas depois de definir o último ponto de verificação.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Deve definir pontos de verificação num fluxo de trabalho depois de atividades que poderão estar expostos a exceção e não devem ser repetem se o fluxo de trabalho é retomado. Por exemplo, o seu fluxo de trabalho pode criar uma máquina virtual. Poderia definir um ponto de verificação antes e depois os comandos para criar a máquina virtual. Se a criação falhar, em seguida, os comandos seriam ser repetidos se o fluxo de trabalho é iniciado novamente. Se o fluxo de trabalho falhar após a criação for concluída com êxito, em seguida, a máquina virtual não será criada novamente quando o fluxo de trabalho é retomado.

O exemplo seguinte copia vários ficheiros para uma localização de rede e define um ponto de verificação após cada ficheiro.  Se a localização de rede for perdida, em seguida, o fluxo de trabalho termina em erro.  Quando é iniciada novamente, ele será retomada no último ponto de verificação que significa que apenas os ficheiros que já tenham sido copiados são ignorados.

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

Uma vez que as credenciais de nome de utilizador não são mantidas depois de chamar o [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) atividade ou após o último ponto de verificação, tem de definir as credenciais para nulo e, em seguida, recuperá-los novamente a partir do arquivo de recurso após  **Suspend-Workflow** ou ponto de verificação é chamado.  Caso contrário, poderá receber a seguinte mensagem de erro: *Não é possível retomar a tarefa de fluxo de trabalho, seja porque os dados de persistência não foi possível ser guardados completamente ou guardar dados de persistência está danificada. Tem de reiniciar o fluxo de trabalho.*

O mesmo código seguinte demonstra como lidar com isso em seus runbooks de fluxo de trabalho do PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Quando pesquisar a sua biblioteca de itens, se não vir **Connect-AzureRMAccount**, pode utilizar **Add-AzureRmAccount**, ou pode atualizar os módulos na conta de automatização.

Isto não é necessário se está a autenticar com uma conta Run As configuradas com um principal de serviço.

Para obter mais informações sobre pontos de verificação, consulte [adicionar pontos de verificação para um fluxo de trabalho de Script](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Passos Seguintes

* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)

