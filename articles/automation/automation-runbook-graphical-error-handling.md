---
title: Erro ao Processar em Runbooks Gráficos da Automatização do Azure
description: Este artigo descreve como implementar a lógica de processamento de erros em runbooks gráficos da Automatização do Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: db14ee3d7e28ba7896b7558a7d01cbe77ad4496b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421020"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Erro ao Processar em Runbooks Gráficos da Automatização do Azure

Um principal de design de runbook chave a considerar é a identificação de diferentes problemas que poderão ocorrer num runbook. Estes problemas poderão incluir o êxito, estados de erro esperados e condições de erro inesperadas.

Os runbooks devem incluir o processamento de erros. Para validar o resultado de uma atividade ou processar um erro de runbooks gráficos, pode utilizar uma atividade de código do Windows PowerShell, definir a lógica condicional na ligação de saída da atividade ou aplicar outro método.          

Muitas vezes, se ocorrer um erro de não terminação numa atividade de runbook, qualquer atividade que se siga é processada independentemente do erro. É provável que o erro crie uma exceção. No entanto, a atividade seguinte continua a poder ser executada. É assim que o PowerShell foi concebido para processar erros.    

Os tipos de erros do PowerShell que podem ocorrer durante a execução são de terminação ou não terminação. As diferenças entre os erros de não terminação e os erros de terminação são os seguintes:

* **Erro de terminação**: um erro grave durante a execução que para completamente o comando (ou a execução do script). Alguns exemplos incluem cmdlets não existentes, erros de sintaxe que impedem a execução de cmdlets ou outros erros fatais.

* **Erro de não terminação**: um erro não grave que permite a continuação da execução apesar da falha. Alguns exemplos incluem erros operacionais, como ficheiros não encontrados e problemas de permissões.

Os runbooks gráficos de automação do Azure foram aprimorados com a capacidade de incluir o tratamento de erros. Agora, pode converter exceções em erros de não terminação e criar ligações de erros entre atividades. Esse processo permite que um autor de runbook Capture erros e gerencie condições percebidas ou inesperadas.  

## <a name="when-to-use-error-handling"></a>Quando utilizar o processamento de erros

Sempre que existir uma atividade crítica que produza um erro ou exceção, é importante impedir que a atividade seguinte no seu runbook seja processada de forma a processar o erro corretamente. Isto é fundamental sobretudo se os seus runbooks estiverem a suportar um processo de operações empresariais ou de serviços.

Para cada atividade que possa produzir um erro, o autor do runbook pode adicionar uma ligação de erro que aponte para outra atividade qualquer. A atividade de destino pode ser de qualquer tipo, incluindo atividades de código, invocações de cmdlets, invocações de outros runbooks e assim sucessivamente.

Além disso, a atividade de destino também pode ter ligações de saída. Estas ligações podem ser ligações normais ou de erro. Isto significa que o autor do runbook pode implementar uma lógica complexa de processamento de erros sem recorrer a uma atividade de código. A prática recomendada é criar um runbook dedicado de processamento de erros com funcionalidades comuns, embora tal não seja obrigatório. A lógica de processamento de erros numa atividade de código do PowerShell não é a única opção.  

Por exemplo, considere um runbook que tente iniciar uma VM e instalar uma aplicação na mesma. Se a VM não iniciar corretamente, o runbook executa duas ações:

1. Envia uma notificação sobre o problema.
2. Inicia outro runbook que aprovisiona automaticamente uma nova VM.

Uma solução consiste em ter uma ligação de erro que aponte para uma atividade que processe o passo um. Por exemplo, pode ligar o cmdlet **Write-Warning** a uma atividade para o passo dois, tal como o cmdlet **Start-AzureRmAutomationRunbook**.

Também pode generalizar este comportamento para ser utilizado em vários runbooks ao colocar estas duas atividades num runbook de processamento de erros separado e ao seguir as orientações sugeridas anteriormente. Antes de chamar este runbook de processamento de erros, pode criar uma mensagem personalizada a partir dos dados do runbook original e, em seguida, transmiti-la como parâmetro para o runbook de processamento de erros.

## <a name="how-to-use-error-handling"></a>Como utilizar o processamento de erros

Cada atividade tem uma definição de configuração que converte as exceções em erros de não terminação. Por predefinição, esta definição está desativada. Recomendamos que ative esta definição em qualquer atividade em que queira processar erros.  

Ao ativar esta configuração, está a garantir que tanto os erros de terminação como os de não terminação na atividade são processados como erros de não terminação, pelo que poderão ser tratados com uma ligação de erro.  

Depois de configurar esta definição, crie uma atividade que processe o erro. Se uma atividade produzir um erro, as ligações de erro de saída são seguidas, mas as ligações normais não, mesmo se a atividade produzir também uma saída normal.<br><br> ![Exemplo de ligação de erro de um runbook de automatização](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo seguinte, o runbook obtém uma variável que contém o nome do computador de uma máquina virtual. Em seguida, tenta iniciar a máquina virtual com a atividade seguinte.<br><br> ![Exemplo de processamento de erros de um runbook de automatização](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

As atividades **Get-AutomationVariable** e **Start-AzureRmVm** estão configuradas para converter exceções em erros. Se ocorrerem problemas ao obter a variável ou ao iniciar a VM, serão gerados erros.<br><br> ![Definições de atividade de processamento de erros de um runbook de automatização](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

As ligações de erro são enviadas destas atividades para uma única atividade de **gestão de erros** (uma atividade de código). Esta atividade é configurada com uma expressão simples do PowerShell que utiliza a palavra-chave *Throw* para parar o processamento, juntamente com *$Error.Exception.Message* para obter a mensagem que descreve a exceção atual.<br><br> ![Exemplo de código de processamento de erros de um runbook de automatização](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre ligações e tipos de ligações em runbooks gráficos, veja [Criação de runbooks gráficos na Automatização do Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).

