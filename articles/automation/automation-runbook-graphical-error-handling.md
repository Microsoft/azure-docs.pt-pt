---
title: Lidar com erros em runbooks gráficos da Azure Automation
description: Este artigo diz como implementar a lógica de manipulação de erros em livros gráficos.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00ed73a1b95558f256caa01ad8eafbefe11b0f32
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830489"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Processamento de erros em runbooks gráficos

Um princípio de design chave a ter em conta para o seu runbook gráfico Azure Automation é a identificação de problemas que o runbook pode experimentar durante a execução. Estes problemas poderão incluir o êxito, estados de erro esperados e condições de erro inesperadas.

Muitas vezes, se houver um erro não-terminal que ocorra com uma atividade de runbook, o Windows PowerShell lida com a atividade processando qualquer atividade que se siga, independentemente do erro. É provável que o erro crie uma exceção. No entanto, a atividade seguinte continua a poder ser executada.

O seu livro gráfico deve incluir código de manuseamento de erros para lidar com problemas de execução. Para validar a saída de uma atividade ou lidar com um erro, pode utilizar uma atividade de código PowerShell, definir lógica condicional na ligação de saída da atividade ou aplicar outro método.

Os runbooks gráficos da Automatização do Azure foram melhorados com a capacidade de incluir o processamento de erros. Agora, pode converter exceções em erros de não terminação e criar ligações de erros entre atividades. O processo melhorado permite que o seu runbook apanhe erros e gere condições realizadas ou inesperadas. 

## <a name="powershell-error-types"></a>Tipos de erro PowerShell

Os tipos de erros PowerShell que podem ocorrer durante a execução do livro de execuções estão a terminar erros e erros não terminantes.
 
### <a name="terminating-error"></a>Erro de encerramento

Um erro de terminação é um erro grave durante a execução que interrompe completamente uma execução de comando ou script. Exemplos incluem cmdlets inexistentes, erros de sintaxe que impedem que um cmdlet corra, e outros erros fatais.

### <a name="non-terminating-error"></a>Erro não-terminal

Um erro de não rescisão é um erro não grave que permite que a execução continue apesar da condição de erro. Exemplos incluem erros operacionais, tais como erros de ficheiros e permissões.

## <a name="when-to-use-error-handling"></a>Quando utilizar o processamento de erros

Utilize o manuseamento de erros no seu runbook quando uma atividade crítica lança um erro ou exceção. É importante evitar que a próxima atividade no livro de ensaios seja processada e lidar com o erro adequadamente. O manuseamento do erro é especialmente crítico quando os seus livros de execução estão a apoiar um processo de operações de negócios ou serviços.

## <a name="add-error-links"></a>Adicionar links de erro

Para cada atividade que possa produzir um erro, pode adicionar um link de erro apontando para qualquer outra atividade. A atividade de destino pode ser de qualquer tipo, incluindo atividade de código, invocação de um cmdlet, invocação de outro runbook, e assim por diante. A atividade de destino também pode ter links de saída, ou regulares ou de erro. Os links permitem que o runbook implemente uma lógica complexa de manipulação de erros sem recorrer a uma atividade de código.

A prática recomendada é criar um runbook dedicado de manipulação de erros com funcionalidade comum, mas esta prática não é obrigatória. Por exemplo, considere um livro de execução que tente iniciar uma máquina virtual e instale uma aplicação no mesmo. Se o VM não começar corretamente, é:

1. Envia uma notificação sobre este problema.
2. Inicia um novo runbook que fornece automaticamente um novo VM.

Uma solução é ter uma ligação de erro no runbook que aponta para uma atividade que lida com o primeiro passo. Por exemplo, o livro pode ligar o `Write-Warning` cmdlet a uma atividade para o segundo passo, como o [cmdlet Start-AzAutomationRunbook.](/powershell/module/az.automation/start-azautomationrunbook)

Também pode generalizar este comportamento para uso em muitos runbooks colocando estas duas atividades num runbook separado de manipulação de erros. Antes de o seu livro original ligar para este runbook de tratamento de erros, pode construir uma mensagem personalizada a partir dos seus dados e, em seguida, passá-la como parâmetro para o runbook de tratamento de erros.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Transformar exceções em erros não-terminantes

Cada atividade no seu runbook tem uma definição de configuração que transforma exceções em erros não terminantes. Por predefinição, esta definição está desativada. Recomendamos que esta definição seja ativada em qualquer atividade em que o seu runbook lida com erros. Esta definição garante que o livro de bordo lida com erros de terminação e não terminação na atividade como erros não terminantes, utilizando uma ligação de erro.  

Depois de ativar a definição de configuração, mandem o seu runbook a criar uma atividade que lide com o erro. Se a atividade produzir algum erro, as ligações de erro de saída são seguidas. Os links regulares não são seguidos, mesmo que a atividade produza também uma produção regular.<br><br> ![Exemplo de ligação de erro de um runbook de automatização](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo seguinte, um livro de bordo recupera uma variável que contém o nome do computador de um VM. Em seguida, tenta iniciar o VM com a próxima atividade.<br><br> ![Exemplo de manuseamento de erros de executo de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A `Get-AutomationVariable` atividade e o cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) estão configurados para converter exceções a erros. Se houver problemas em obter a variável ou iniciar o VM, o código gera erros.<br><br> ![Definições de atividade de tratamento de erros do runbook de automatização ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) .

As ligações de erro fluem destas atividades para uma `error management` única atividade de código. Esta atividade é configurada com uma expressão simples PowerShell que usa a `throw` palavra-chave para parar o processamento, juntamente com `$Error.Exception.Message` para obter a mensagem que descreve a exceção atual.<br><br> ![Exemplo de código de manipulação de erro de verificação de código de verificação de executo por automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a resolução de erros gráficos do [runbook, consulte os problemas do livro de contas de resolução de problemas](troubleshoot/runbooks.md).
