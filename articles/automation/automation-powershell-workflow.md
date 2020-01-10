---
title: Aprendendo o fluxo de trabalho do PowerShell para automação do Azure
description: Este artigo destina-se a ser uma lição rápida para os autores familiarizados com o PowerShell para entender as diferenças específicas entre o fluxo de trabalho do PowerShell e do PowerShell e os conceitos aplicáveis aos runbooks de automação.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6e4c8057322b6208ea3b447b264e2bde1344540c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421560"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Principais conceitos de fluxo de trabalho do Windows PowerShell para runbooks de automação

Os Runbooks na automação do Azure são implementados como fluxos de trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo usuário.  Embora este artigo tenha a finalidade de ajudá-lo a escrever runbooks usando o fluxo de trabalho do PowerShell, recomendamos que você grave runbooks usando o PowerShell, a menos que precise de pontos de verificação.  Há várias diferenças de sintaxe ao criar runbooks de fluxo de trabalho do PowerShell e essas diferenças exigem um pouco mais de trabalho para escrever fluxos de trabalhos efetivos.

Um fluxo de trabalho é uma sequência de passos programados e ligados que efetuar tarefas de longa execução ou requerem a coordenação de vários passos em vários dispositivos ou nós geridos. As vantagens de um fluxo de trabalho ao longo de um script normal incluem a capacidade de efetuar simultaneamente uma ação em vários dispositivos e a capacidade de recuperar automaticamente de falhas. Um fluxo de trabalho do Windows PowerShell é um script do Windows PowerShell que usa Windows Workflow Foundation. Enquanto o fluxo de trabalho é escrito com a sintaxe do Windows PowerShell e lançado pelo Windows PowerShell, é processada pelo Windows Workflow Foundation.

Para obter detalhes completos sobre os tópicos deste artigo, consulte [introdução com o fluxo de trabalho do Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho

A primeira etapa para converter um script do PowerShell para um fluxo de trabalho do PowerShell é colocá-lo com a palavra-chave **Workflow** .  Um fluxo de trabalho começa com a palavra-chave **Workflow** seguida pelo corpo do script entre chaves. O nome do fluxo de trabalho segue a palavra-chave **Workflow** , conforme mostrado na seguinte sintaxe:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho tem de corresponder ao nome do runbook de automatização. Se o runbook estiver sendo importado, o nome do arquivo deverá corresponder ao nome do fluxo de trabalho e deverá terminar em *. ps1*.

Para adicionar parâmetros ao fluxo de trabalho, use a palavra-chave **param** da mesma forma que faria com um script.

## <a name="code-changes"></a>Alterações do código

O código de fluxo de trabalho do PowerShell parece quase idêntico ao código de script do PowerShell, exceto por algumas alterações significativas.  As seções a seguir descrevem as alterações que você precisa fazer em um script do PowerShell para que ele seja executado em um fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica num fluxo de trabalho. Tal como um script é composto por um ou mais comandos, um fluxo de trabalho é composta por uma ou mais atividades que são executadas numa sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell em atividades quando é executada um fluxo de trabalho. Quando você especifica um desses cmdlets em seu runbook, a atividade correspondente é executada por Windows Workflow Foundation. No caso dos cmdlets sem atividade correspondente, o fluxo de trabalho do Windows PowerShell executa automaticamente o cmdlet dentro de um [InlineScript](#inlinescript) atividade. Existe um conjunto de cmdlets que são excluídos e não podem ser utilizados num fluxo de trabalho, a menos que sejam explicitamente incluídos num bloco InlineScript. Para obter mais detalhes sobre estes conceitos, veja [utilizar atividades em fluxos de trabalho de Script](https://technet.microsoft.com/library/jj574194.aspx).

Atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns para configurar o funcionamento deles. Para obter detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais

Você não pode usar parâmetros posicionais com atividades e cmdlets em um fluxo de trabalho.  Tudo isso significa que você deve usar nomes de parâmetro.

Por exemplo, considere o código a seguir que obtém todos os serviços em execução.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se você tentar executar esse mesmo código em um fluxo de trabalho, receberá uma mensagem como "o conjunto de parâmetros não pode ser resolvido usando os parâmetros nomeados especificados".  Para corrigir isso, forneça o nome do parâmetro como no seguinte.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos desserializados

Os objetos nos fluxos de trabalho são desserializados.  Isso significa que suas propriedades ainda estão disponíveis, mas não seus métodos.  Por exemplo, considere o seguinte código do PowerShell que interrompe um serviço usando o método Stop do objeto de serviço.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se você tentar executá-lo em um fluxo de trabalho, receberá um erro dizendo "não há suporte para invocação de método em um fluxo de trabalho do Windows PowerShell".

Uma opção é encapsular essas duas linhas de código em um bloco [InlineScript](#inlinescript) , caso em que $Service seria um objeto de serviço dentro do bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é usar outro cmdlet que execute a mesma funcionalidade que o método, se houver um disponível.  Em nosso exemplo, o cmdlet Stop-Service fornece a mesma funcionalidade que o método stop, e você pode usar o seguinte para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

A atividade **InlineScript** é útil quando você precisa executar um ou mais comandos como o script do PowerShell tradicional em vez do fluxo de trabalho do PowerShell.  Enquanto os comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, os comandos num bloco InlineScript são processados pelo Windows PowerShell.

InlineScript usa a seguinte sintaxe mostrada abaixo.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Você pode retornar a saída de um InlineScript atribuindo a saída a uma variável. O exemplo a seguir interrompe um serviço e, em seguida, gera o nome do serviço.

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

Você pode passar valores para um bloco InlineScript, mas você deve usar **$using** modificador de escopo.  O exemplo a seguir é idêntico ao exemplo anterior, exceto pelo fato de que o nome do serviço é fornecido por uma variável.

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

Embora as atividades de InlineScript possam ser críticas em determinados fluxos de trabalho, elas não dão suporte a construções de fluxo de trabalho e só devem ser usadas quando necessário pelos seguintes motivos:

* Você não pode usar [pontos de verificação](#checkpoints) dentro de um bloco InlineScript. Se ocorrer uma falha dentro do bloco, ele deverá ser retomado do início do bloco.
* Não é possível usar a [execução paralela](#parallel-processing) dentro de um InlineScriptBlock.
* O InlineScript afeta a escalabilidade do fluxo de trabalho, pois contém a sessão do Windows PowerShell por todo o tamanho do bloco InlineScript.

Para obter mais informações sobre como usar o InlineScript, consulte [executando comandos do Windows PowerShell em um fluxo de trabalho](https://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Processamento paralelo

Uma das vantagens de fluxos de trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente como acontece com um script típico.

Você pode usar a palavra-chave **Parallel** para criar um bloco de script com vários comandos que são executados simultaneamente. Isso usa a seguinte sintaxe mostrada abaixo. Nesse caso, Atividade1 e da atividade2 são iniciados ao mesmo tempo. A atividade3 começará inicia somente depois que Atividade1 e da atividade2 forem concluídos.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos do PowerShell que copiam vários arquivos para um destino de rede.  Esses comandos são executados em sequência para que um arquivo deva concluir a cópia antes que o próximo seja iniciado.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

O fluxo de trabalho a seguir executa esses mesmos comandos em paralelo para que todos comecem a copiar ao mesmo tempo.  Somente depois de todas elas serem copiadas, a mensagem de conclusão é exibida.

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

Pode utilizar o **ForEach-Parallel** construção para processar comandos para cada item na coleção em simultâneo. Os itens na coleção são processados paralelamente enquanto os comandos no bloco de script são executados sequencialmente. Isso usa a seguinte sintaxe mostrada abaixo. Nesse caso, Atividade1 é iniciado ao mesmo tempo para todos os itens na coleção. Para cada item, da atividade2 é iniciado após a conclusão de Atividade1. A atividade3 começará inicia somente depois que Atividade1 e da atividade2 forem concluídos para todos os itens. Usamos o parâmetro `ThrottleLimit` para limitar o paralelismo. Um `ThrottleLimit` muito alto pode causar problemas. O valor ideal para o parâmetro `ThrottleLimit` depende de muitos fatores em seu ambiente. Você deve tentar começar com um valor baixo e experimentar diferentes valores crescentes até encontrar um que funcione para sua circunstância específica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo a seguir é semelhante ao exemplo anterior copiando arquivos em paralelo.  Nesse caso, uma mensagem é exibida para cada arquivo após a cópia.  Somente depois que todos eles forem completamente copiados, a mensagem de conclusão final será exibida.

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
> Não recomendamos a execução de runbooks filho em paralelo, pois isso foi mostrado para fornecer resultados não confiáveis. Às vezes, a saída do runbook filho não aparece, e as configurações em um runbook filho podem afetar os outros runbooks filho paralelos. Variáveis como $VerbosePreference, $WarningPreference e outras não podem ser propagadas para os runbooks filho. E se o runbook filho alterar esses valores, eles poderão não ser restaurados corretamente após a invocação.

## <a name="checkpoints"></a>Pontos de verificação

Um *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual para variáveis e qualquer saída gerada para esse ponto. Se um fluxo de trabalho terminar com erro ou for suspenso, na próxima vez em que for executado, ele será iniciado a partir do último ponto de verificação, em vez do início do fluxo de trabalho.  Pode definir um ponto de verificação num fluxo de trabalho com o **Checkpoint-Workflow** atividade. A automação do Azure tem um recurso chamado [Fair share](automation-runbook-execution.md#fair-share), em que qualquer runbook executado por 3 horas é descarregado para permitir que outros runbooks sejam executados. Eventualmente, o runbook descarregado será recarregado e, quando for, ele continuará a execução do último ponto de verificação feito no runbook. Para garantir que o runbook seja concluído eventualmente, você deve adicionar pontos de verificação em intervalos executados por menos de 3 horas. Se, durante cada execução, um novo ponto de verificação for adicionado, e se o runbook for removido após três horas devido a um erro, o runbook será retomado indefinidamente.

No código de exemplo a seguir, uma exceção ocorre depois de da atividade2, fazendo com que o fluxo de trabalho seja encerrado. Quando o fluxo de trabalho é executado novamente, ele começa executando da atividade2, já que isso estava logo após o último ponto de verificação definido.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Você deve definir pontos de verificação em um fluxo de trabalho após atividades que podem ser propensas a exceções e não devem ser repetidas se o fluxo de trabalho for retomado. Por exemplo, o fluxo de trabalho pode criar uma máquina virtual. Poderia definir um ponto de verificação antes e depois os comandos para criar a máquina virtual. Se a criação falhar, os comandos seriam repetidos se o fluxo de trabalho for iniciado novamente. Se o fluxo de trabalho falhar depois que a criação for bem-sucedida, a máquina virtual não será criada novamente quando o fluxo de trabalho for retomado.

O exemplo a seguir copia vários arquivos para um local de rede e define um ponto de verificação após cada arquivo.  Se o local de rede for perdido, o fluxo de trabalho terminará com erro.  Quando ele for iniciado novamente, ele será retomado no último ponto de verificação, o que significa que apenas os arquivos que já foram copiados serão ignorados.

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

Como as credenciais de nome de usuário não são persistidas depois de chamar a atividade [suspender fluxo de trabalho](https://technet.microsoft.com/library/jj733586.aspx) ou após o último ponto de verificação, você precisa definir as credenciais como NULL e, em seguida, recuperá-las novamente no repositório de ativos após **suspender-Workflow** ou Checkpoint ser chamado.  Caso contrário, você pode receber a seguinte mensagem de erro: *não é possível retomar a tarefa de fluxo de trabalho porque os dados de persistência não puderam ser salvos completamente ou os dados de persistência salvos foram corrompidos. Você deve reiniciar o fluxo de trabalho.*

O código a seguir demonstra como lidar com isso em seus runbooks de fluxo de trabalho do PowerShell.

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
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá atualizar seus módulos na sua conta de automação.

Isso não será necessário se você estiver autenticando usando uma conta Executar como configurada com uma entidade de serviço.

Para obter mais informações sobre pontos de verificação, consulte [adicionar pontos de verificação para um fluxo de trabalho de Script](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Passos seguintes

* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)

