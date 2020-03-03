---
title: Erro ao Processar em Runbooks Gráficos da Automatização do Azure
description: Este artigo descreve como implementar a lógica de processamento de erros em runbooks gráficos da Automatização do Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 153df77c030180402b1e30bc456d681c232c390b
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226520"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Manipulação de erros em livros gráficos de automação Azure

Um princípio chave de design a considerar para o seu livro de execução gráfica azure Automation é a identificação de questões que o livro de execução pode experimentar durante a execução. Estes problemas poderão incluir o êxito, estados de erro esperados e condições de erro inesperadas.

Muitas vezes, se houver um erro não terminante que ocorra com uma atividade de livro de execução, o Windows PowerShell lida com a atividade processando qualquer atividade que se siga, independentemente do erro. É provável que o erro crie uma exceção. No entanto, a atividade seguinte continua a poder ser executada.

O seu livro de execução gráfico deve incluir código de manuseamento de erros para lidar com problemas de execução. Para validar a saída de uma atividade ou lidar com um erro, pode utilizar uma atividade de código PowerShell, definir lógica condicional na ligação de saída da atividade ou aplicar outro método.

Os livros de execução gráfico sografia Azure Automation foram melhorados com a capacidade de incluir o manuseamento de erros. Agora, pode converter exceções em erros de não terminação e criar ligações de erros entre atividades. O processo melhorado permite que o seu livro de execução apanhe erros e gere condições realizadas ou inesperadas. 

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Tipos de erro PowerShell

Os tipos de erros powerShell que podem ocorrer durante a execução do livro de corridas são erros de terminação e erros não terminadores.
 
### <a name="terminating-error"></a>Erro de terminação

Um erro de terminação é um erro grave durante a execução que interrompe completamente um comando ou execução de script. Exemplos incluem cmdlets inexistentes, erros de sintaxe que impedem que um cmdlet de funcionamento, e outros erros fatais.

### <a name="non-terminating-error"></a>Erro de não terminação

Um erro não terminando é um erro não grave que permite que a execução continue apesar da condição de erro. Exemplos incluem erros operacionais, tais como ficheiros não encontrados erros e problemas de permissões.

## <a name="when-to-use-error-handling"></a>Quando utilizar o processamento de erros

Utilize o manuseamento de erros no seu livro de execução quando uma atividade crítica der um erro ou exceção. É importante evitar que a próxima atividade no livro de ensaios processe e lide com o erro de forma adequada. Lidar com o erro é especialmente crítico quando os seus livros de execução estão a apoiar um processo de operações de negócio ou de serviço.

Para cada atividade que possa produzir um erro, pode adicionar uma ligação de erro apontando para qualquer outra atividade. A atividade de destino pode ser de qualquer tipo, incluindo a atividade de código, invocação de um cmdlet, invocação de outro livro de execução, e assim por diante. A atividade de destino também pode ter links de saída, quer regulares quer por erros. As ligações permitem que o livro de execução implemente uma lógica complexa de manipulação de erros sem recorrer a uma atividade de código.

A prática recomendada é criar um livro de execução dedicado ao manuseamento de erros com funcionalidade comum, mas esta prática não é obrigatória. Por exemplo, considere um livro de rumantes que tente iniciar uma máquina virtual e instalar uma aplicação nele. Se o VM não começar corretamente, é:

1. Envia uma notificação sobre este problema.
2. Inicia outro livro de execução que provise automaticamente um novo VM.

Uma solução é ter uma ligação de erro no livro de execução que aponta para uma atividade que lida com o passo um. Por exemplo, o livro de execução pode ligar o cmdlet **de aviso de escrita** a uma atividade para o segundo passo, como o cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

Você também pode generalizar este comportamento para uso em muitos livros de execução colocando estas duas atividades em um livro de execução de manipulação de erros separado. Antes de o seu livro original chamar este livro de execução de manipulação de erros, pode construir uma mensagem personalizada a partir dos seus dados e, em seguida, passá-la como parâmetro para o livro de execução de manipulação de erros.

## <a name="how-to-use-error-handling"></a>Como utilizar o processamento de erros

Cada atividade no seu livro de execução tem uma configuração que transforma exceções em erros não terminadores. Por predefinição, esta definição está desativada. Recomendamos que esta definição seja ativada em qualquer atividade em que o seu livro de recorram erros. Esta definição garante que o livro de execução lida com erros de terminação e não terminadores na atividade como erros não terminadores, utilizando uma ligação de erro.  

Depois de ativar a definição de configuração, o seu livro de execução crie uma atividade que lide com o erro. Se a atividade produzir algum erro, as ligações de erro de saída são seguidas. As ligações regulares não são seguidas, mesmo que a atividade produza uma saída regular também.<br><br> ![Exemplo de ligação de erro de um runbook de automatização](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo seguinte, um livro de execução recupera uma variável que contém o nome do computador de um VM. Em seguida, tenta iniciar o VM com a próxima atividade.<br><br> ![Exemplo de manipulação de erros de executo de automatização](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A atividade **Get-AutomationVariable** e o [cmdlet Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) estão configurados para converter exceções a erros. Se houver problemas em obter a variável ou iniciar o VM, o código gera erros.<br><br> ![Definição de atividade de manipulação de erros do livro de automação](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

As ligações de erro fluem destas atividades para uma única atividade de código de gestão de **erros.** Esta atividade é configurada com uma simples expressão PowerShell que usa a palavra-chave de **arremesso** para parar o processamento, juntamente com `$Error.Exception.Message` para obter a mensagem que descreve a exceção atual.<br><br> ![Automatização de código de manuseamento de erros de livro de executantes](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre links e tipos de links em livros gráficos, consulte [a autoria gráfica em Automação Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre a execução do livro de corridas, monitorização de trabalhos de livro de corridas e outros detalhes técnicos, consulte a execução do Livro de [Corridas na Automação Azure.](automation-runbook-execution.md)