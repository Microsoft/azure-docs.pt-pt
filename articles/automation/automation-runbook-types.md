---
title: Tipos de runbook Azure Automation
description: Este artigo descreve os tipos de livros que pode utilizar na Azure Automation e considerações para determinar que tipo de utilização.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: 067096943cd95913077ada817c94640ff5264520
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634894"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook Azure Automation

A funcionalidade Azure Automation Process Automation suporta vários tipos de runbooks, conforme definido na tabela seguinte. Para conhecer o ambiente de automatização de processos, consulte [a execução do Runbook na Azure Automation.](automation-runbook-execution.md)

| Tipo | Description |
|:--- |:--- |
| [Gráficos](#graphical-runbooks)|Livro gráfico baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal Azure. |
| [Fluxo de trabalho gráfico powershell](#graphical-runbooks)|Livro gráfico baseado no Fluxo de Trabalho do Windows PowerShell e criado e editado completamente no editor gráfico no portal Azure. |
| [PowerShell](#powershell-runbooks) |Livro de texto baseado na scripting do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Livro de texto baseado na scripting do fluxo de trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Livro textual baseado na escrita python. |

Tenha em conta as seguintes considerações ao determinar qual o tipo de utilização para um determinado livro de bordo.

* Não é possível converter livros de gráfica para tipo de texto, ou o contrário.
* Existem limitações ao utilizar livros de diferentes tipos como livros infantis. Para mais informações, consulte [livros para crianças na Azure Automation.](automation-child-runbooks.md)

## <a name="graphical-runbooks"></a>Livros gráficos

Pode criar e editar livros gráficos e gráficos de fluxo de trabalho PowerShell utilizando o editor gráfico no portal Azure. No entanto, não é possível criar ou editar este tipo de livro com outra ferramenta. Principais características dos livros gráficos:

* Exportado para ficheiros na sua conta de Automação e depois importado para outra conta de Automação.
* Gerar código PowerShell.
* Convertidos para ou a partir de livros gráficos de fluxo de trabalho powerShell durante a importação.

### <a name="advantages"></a>Vantagens

* Utilize o modelo de autoria de inserção visual-configuração de ligação.
* Concentre-se na forma como os dados fluem através do processo.
* Visualmente representam processos de gestão.
* Inclua outros livros de ensaio como livros infantis para criar fluxos de trabalho de alto nível.
* Incentivar a programação modular.

### <a name="limitations"></a>Limitações

* Não é possível criar ou editar fora do portal Azure.
* Pode exigir uma atividade de código que contenha o código PowerShell para executar uma lógica complexa.
* Não é possível converter num dos [formatos](automation-runbook-types.md)de texto , nem converter um livro de texto para formato gráfico. 
* Não é possível visualizar ou editar diretamente o código PowerShell que o fluxo de trabalho gráfico cria. Pode ver o código que cria em quaisquer atividades de código.
* Não se pode publicar livros num Fisiário Híbrido Linux. Consulte [os recursos do seu datacenter ou nuvem utilizando o Trabalhador de Runbook Híbrido.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>Livros powerShell

Os livros powerShell são baseados no Windows PowerShell. Edita diretamente o código do livro de texto utilizando o editor de texto no portal Azure.  Também pode utilizar qualquer editor de texto offline e [importar o livro de bordo](manage-runbooks.md) para a Azure Automation.

### <a name="advantages"></a>Vantagens

* Implementar toda a lógica complexa com o código PowerShell sem as outras complexidades do Fluxo de Trabalho PowerShell.
* Comece mais rápido que os livros de fluxo de trabalho powerShell, uma vez que não precisam de ser compilados antes de serem executados.
* Corra em Azure e em Hybrid Runbook Workers tanto para Windows como Linux.

### <a name="limitations"></a>Limitações

* Deves estar familiarizado com os scripts da PowerShell.
* Os runbooks não podem usar [o processamento paralelo](automation-powershell-workflow.md#use-parallel-processing) para executar várias ações em paralelo.
* Os runbooks não podem usar [postos de controlo](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) para retomar o livro de contas se houver um erro.
* Pode incluir apenas livros de fluxo de trabalho PowerShell e runbooks gráficos como livros infantis utilizando o [cmdlet Start-AzAutomationRunbook,](/powershell/module/az.automation/start-azautomationrunbook) que cria um novo emprego.

### <a name="known-issues"></a>Problemas conhecidos

Seguem-se os problemas atuais conhecidos com os livros de execução PowerShell:

* Os livros powerShell não conseguem recuperar um [ativo variável](./shared-resources/variables.md) não encriptado com um valor nulo.
* Os livros powerShell não conseguem recuperar um ativo variável com `*~*` o nome.
* Uma operação [Get-Process](/powershell/module/microsoft.powershell.management/get-process) em loop num livro de execução PowerShell pode colidir após cerca de 80 iterações.
* Um livro de execução PowerShell pode falhar se tentar escrever uma grande quantidade de dados para o fluxo de saída de uma só vez. Normalmente, pode trabalhar em torno deste problema, tendo a saída do runbook apenas a informação necessária para trabalhar com objetos grandes. Por exemplo, em vez de utilizar `Get-Process` sem limitações, pode ter a saída de cmdlet apenas os parâmetros necessários como em `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>Livros de fluxo de trabalho PowerShell

Os livros de fluxo de trabalho PowerShell são livros de texto baseados no [fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md). Edita diretamente o código do livro de texto utilizando o editor de texto no portal Azure. Também pode utilizar qualquer editor de texto offline e [importar o livro de bordo](manage-runbooks.md) para a Azure Automation.

### <a name="advantages"></a>Vantagens

* Implementar toda a lógica complexa com o código de fluxo de trabalho PowerShell.
* Utilize [postos de controlo](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) para retomar o funcionamento se houver um erro.
* Utilize [o processamento paralelo](automation-powershell-workflow.md#use-parallel-processing) para fazer múltiplas ações em paralelo.
* Pode incluir outros runbooks gráficos e runbooks powerShell Workflow como livros infantis para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o fluxo de trabalho powerShell.
* Os livros de execução devem lidar com a complexidade adicional do fluxo de trabalho PowerShell, como [objetos desercializados](automation-powershell-workflow.md#deserialized-objects).
* Os livros de execução demoram mais tempo a começar do que os livros powerShell, uma vez que devem ser compilados antes de serem executados.
* Só pode incluir os livros powerShell como livros infantis utilizando o `Start-AzAutomationRunbook` cmdlet.
* Os runbooks não podem funcionar com um trabalhador de runbook híbrido Linux.

## <a name="python-runbooks"></a>Livros de python

Os livros de python compilam-se sob Python 2 e Python 3. Os livros de 100 python estão atualmente em pré-visualização. Pode editar diretamente o código do livro de texto utilizando o editor de texto no portal Azure. Também pode utilizar um editor de texto offline e [importar o livro de bordo](manage-runbooks.md) para a Azure Automation.

Os livros de produção Python 3 são suportados nas seguintes infraestruturas globais do Azure:

* Azure global
* Azure Government

### <a name="advantages"></a>Vantagens

* Use as robustas bibliotecas Python.
* Pode correr em Azure ou em Hybrid Runbook Workers.
* Para o Python 2, os Trabalhadores do Runbook Híbrido do Windows são suportados com [python 2.7](https://www.python.org/downloads/release/latest/python2) instalado.
* Para Python 3 Cloud Jobs, a versão Python 3.8 é suportada. Scripts e pacotes de qualquer versão 3.x podem funcionar se o código for compatível em diferentes versões.  
* Para trabalhos híbridos Python 3 em máquinas Windows, pode optar por instalar qualquer versão 3.x que queira utilizar.  
* Para trabalhos híbridos Python 3 em máquinas Linux, dependemos da versão Python 3 instalada na máquina para executar DSC OMSConfig e o Linux Hybrid Worker. Recomendamos a instalação de 3.6 em máquinas Linux. No entanto, diferentes versões também devem funcionar se não houver alterações nas assinaturas de métodos ou contratos entre versões de Python 3.

### <a name="limitations"></a>Limitações

* Deves estar familiarizado com os guiões dos Python.
* Para utilizar bibliotecas de terceiros, deve [importar os pacotes](python-packages.md) para a conta Automation.
* A utilização do cmdlet **Start-AutomationRunbook**   no PowerShell/PowerShell Workflow para iniciar um livro de bordo Python 3 (pré-visualização) não funciona. Pode utilizar o cmdlet **Start-AzAutomationRunbook** a partir do módulo Az.Automation ou o cmdlet **Start-AzureRmAutomationRunbook** da AzureRm.Automation module para contornar esta limitação.  
* Os livros de bordo Python 3 (pré-visualização) e os pacotes não funcionam com o PowerShell.
* A Azure Automation não suporta **sys.stderr**.

### <a name="known-issues"></a>Problemas conhecidos

Os trabalhos python 3 às vezes falham com uma mensagem de exceção *inválido caminho executável de intérpretes*. Poderá ver esta exceção se um trabalho for atrasado, a partir de mais de 10 minutos ou utilizando **o Start-AutomationRunbook** para iniciar os runbooks Python 3. Se o trabalho for atrasado, reiniciar o livro deve ser suficiente.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os livros de execução PowerShell, consulte [Tutorial: Crie um livro de regras PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para saber mais sobre os livros de fluxo de trabalho powerShell, consulte [Tutorial: Crie um manual de fluxo de trabalho PowerShell](learn/automation-tutorial-runbook-textual.md).
* Para saber mais sobre os runbooks gráficos, consulte [Tutorial: Crie um runbook gráfico](learn/automation-tutorial-runbook-graphical.md).
* Para saber mais sobre os livros python, consulte [Tutorial: Crie um livro de bordo Python](learn/automation-tutorial-runbook-textual-python2.md).
