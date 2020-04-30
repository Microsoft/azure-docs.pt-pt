---
title: Tipos de livro de execução da Automação Azure
description: Descreve os diferentes tipos de livros de execução que pode utilizar na Automação Azure e considerações para determinar que tipo usar.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535524"
---
# <a name="azure-automation-runbook-types"></a>Tipos de livro de execução da Automação Azure

O serviço de automação de processos Azure Automation suporta vários tipos de livros de execução, tal como definidos na tabela seguinte. Para conhecer o ambiente de automação de processos, consulte a execução do Livro de [Corridas na Automação Azure.](automation-runbook-execution.md)

| Tipo | Descrição |
|:--- |:--- |
| [Gráficos](#graphical-runbooks)|Livro de execução gráfico baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal Azure. |
| [Fluxo de trabalho de PowerShell Graphical PowerShell](#graphical-runbooks)|Livro gráfico baseado no Windows PowerShell Workflow e criado e editado completamente no editor gráfico no portal Azure. |
| [PowerShell](#powershell-runbooks) |Livro de execução de texto baseado na escrita do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Livro de execução de texto baseado na scripting do Windows PowerShell Workflow. |
| [Python](#python-runbooks) |Livro de texto baseado no scripting Python. |

Tenha em conta as seguintes considerações ao determinar qual o tipo a utilizar para um determinado livro de execução.

* Não é possível converter livros de execução de tipo gráfico para texto, ou o contrário.
* Existem limitações ao utilizar livros de diferentes tipos como livros infantis. Para mais informações, consulte livros [infantis em Automação Azure](automation-child-runbooks.md).

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Livros gráficos

Pode criar e editar livros de execução gráficos e gráficos powerShell Workflow utilizando o editor gráfico no portal Azure. No entanto, não é possível criar ou editar este tipo de livro com outra ferramenta. Principais características dos livros de execução gráficos:

* Pode ser exportado para ficheiros na sua conta Automation e depois importado para outra conta de Automação. 
* Gere o código PowerShell. 
* Pode ser convertido para ou a partir de livros de execução gráficos powerShell Workflow durante a importação. 

### <a name="advantages"></a>Vantagens

* Utilize o modelo de autor de uma configuração de ligação visual.
* Concentre-se na forma como os dados fluem através do processo.
* Representação visual dos processos de gestão.
* Inclua outros livros de execução como livros infantis para criar fluxos de trabalho de alto nível.
* Encorajar a programação modular.

### <a name="limitations"></a>Limitações

* Não se pode criar ou editar fora do portal Azure.
* Pode requerer uma atividade de código contendo código PowerShell para executar lógica complexa.
* Não pode converter-se num dos [formatos](automation-runbook-types.md)de texto, nem pode converter um livro de texto em formato gráfico. 
* Não é possível visualizar ou editar diretamente o código PowerShell que o fluxo de trabalho gráfico cria. Pode ver o código que cria em qualquer atividade de código.
* Não se pode publicar livros num Trabalhador de Livro Híbrido Linux. Consulte [os recursos automate no seu datacenter ou nuvem utilizando o Trabalhador do Livro de Corridas Híbrido](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Livros de execução PowerShell

Os livros de execução powerShell são baseados no Windows PowerShell. Edita diretamente o código do livro de execução utilizando o editor de texto no portal Azure.  Também pode utilizar qualquer editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Implementar toda a lógica complexa com código PowerShell sem as complexidades adicionais do PowerShell Workflow.
* Comece mais rápido do que os livros de workflow powerShell, uma vez que não precisam de ser compilados antes de correr.
* Corra em Azure e em Trabalhadores híbridos de runbook para windows e Linux.

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o script powerShell.
* Os livros de execução não podem usar [o processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar múltiplas ações paralelamente.
* Os livros de [execução](automation-powershell-workflow.md#checkpoints) não podem usar postos de controlo para retomar o livro de ensaios se houver um erro.
* Pode incluir apenas livros de execução powerShell Workflow e livros gráficos como livros de execução para crianças utilizando o cmdlet [Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) o que cria um novo trabalho.

### <a name="known-issues"></a>Problemas conhecidos

Seguem-se os problemas conhecidos atuais com os livros de execução da PowerShell:

* Os livros de execução da PowerShell não conseguem recuperar um [ativo variável](automation-variables.md) não encriptado com um valor nulo.
* Os livros de execução da PowerShell não conseguem recuperar um ativo variável com `*~*` o nome.
* Uma operação [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) num ciclo num livro de corridas da PowerShell pode falhar após cerca de 80 iterações.
* Um livro de execução powerShell pode falhar se tentar escrever uma grande quantidade de dados para o fluxo de saída de uma só vez. Normalmente, pode contornar este problema, tendo a saída do livro de corridas apenas a informação necessária para trabalhar com objetos grandes. Por exemplo, em `Get-Process` vez de utilizar sem limitações, pode ter a saída `Get-Process | Select ProcessName, CPU`de cmdlet apenas os parâmetros necessários como em .

## <a name="powershell-workflow-runbooks"></a>Livros de execução powerShell Workflow

Os livros de execução do PowerShell Workflow são livros de execução de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md). Edita diretamente o código do livro de execução utilizando o editor de texto no portal Azure. Também pode utilizar qualquer editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Implementar toda a lógica complexa com o código powerShell Workflow.
* Utilize [os pontos](automation-powershell-workflow.md#checkpoints) de verificação para retomar a operação se houver um erro.
* Utilize [o processamento paralelo](automation-powershell-workflow.md#parallel-processing) para fazer múltiplas ações paralelas.
* Pode incluir outros livros gráficos e livros de fluxo de trabalho powerShell como livros de corridas para crianças para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o PowerShell Workflow.
* Os livros de execução devem lidar com a complexidade adicional do PowerShell Workflow, como [objetos desserializados.](automation-powershell-workflow.md#code-changes)
* Os livros de execução demoram mais tempo a começar do que os livros powerShell, uma vez que devem ser compilados antes de serem executados.
* Só pode incluir os livros powerShell como `Start-AzAutomationRunbook` livros infantis utilizando o cmdlet.
* Os livros não funcionam com um Trabalhador do Livro Híbrido Linux.

## <a name="python-runbooks"></a>Livros de execução python

Os livros de python compilam sob python 2. Pode editar diretamente o código do livro de execução utilizando o editor de texto no portal Azure. Também pode usar um editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Use as robustas bibliotecas Python.
* Pode correr em Azure ou em Linux Hybrid Runbook Workers. Os trabalhadores do Windows Hybrid Runbook são suportados com [python2.7](https://www.python.org/downloads/release/latest/python2) instalados.

### <a name="limitations"></a>Limitações

* Deves estar familiarizado com o guião do Python.
* Apenas python 2 é suportado atualmente. Quaisquer funções específicas python 3 falham.
* Para utilizar bibliotecas de terceiros, deve [importar os pacotes](python-packages.md) para a conta Automation.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a autoria de um livro gráfico, consulte [a autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)
* Para compreender as diferenças entre powerShell e powerShell fluxos de trabalho para livros de execução, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md).
* Para obter mais informações sobre como criar ou importar um livro de execução, consulte [Gerir livros de execução em Automação Azure](manage-runbooks.md).
* Para saber mais sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
