---
title: Tipos de livro de execução da Automação Azure
description: Descreve os diferentes tipos de livros de execução que pode utilizar na Automação Azure e considera ções que deve ter em conta ao determinar que tipo usar.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010176"
---
# <a name="azure-automation-runbook-types"></a>Tipos de livro de execução da Automação Azure

O serviço de automação de processos Azure Automation suporta vários tipos de livros de execução. Os tipos são brevemente definidos na tabela seguinte e descritos com mais detalhes nas secções abaixo. Para conhecer o ambiente de automação de processos, consulte a execução do Livro de [Corridas na Automação Azure.](automation-runbook-execution.md)

| Tipo | Descrição |
|:--- |:--- |
| [Gráficos](#graphical-runbooks)|Livro de execução gráfico baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal Azure. |
| [Fluxo de trabalho de PowerShell Graphical PowerShell](#graphical-runbooks)|Livro gráfico baseado no Windows PowerShell Workflow e criado e editado completamente no editor gráfico no portal Azure. |
| [PowerShell](#powershell-runbooks) |Livro de execução de texto baseado na escrita do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Livro de execução de texto baseado na scripting do Windows PowerShell Workflow. |
| [Python](#python-runbooks) |Livro de texto baseado no scripting Python. |

## <a name="graphical-runbooks"></a>Livros gráficos

Pode criar e editar livros de execução gráficos e gráficos powerShell Workflow utilizando o editor gráfico no portal Azure. No entanto, não é possível criar ou editar este tipo de livro com outra ferramenta.

Um livro de execução gráfico tem as seguintes características principais:

* Pode ser exportado para um ficheiro na sua conta de Automação e depois importado para outra conta de Automação. 
* Gera código PowerShell. 
* Pode ser convertido de ou para um livro de execução gráfico powerShell Workflow durante a importação. 

### <a name="advantages"></a>Vantagens

* Utiliza o modelo de autor de uma configuração de inserção visual.
* Foca-se na forma como os dados fluem através do processo.
* Visualmente representa processos de gestão.
* Inclui outros livros de execução como livros infantis para criar fluxos de trabalho de alto nível.
* Encoraja a programação modular.

### <a name="limitations"></a>Limitações

* Não pode ser criado ou editado fora do portal Azure.
* Pode requerer uma atividade de código contendo código PowerShell para executar lógica complexa.
* Não pode ser convertido num dos [formatos](automation-runbook-types.md)de texto, nem um livro de texto pode ser convertido em formato gráfico. 
* Não lhe permite visualizar ou editar diretamente o código PowerShell que o fluxo de trabalho gráfico cria. Pode ver o código que cria em qualquer atividade de código.
* Não funciona com um trabalhador de livros híbridos Linux. Consulte [os recursos automate no seu datacenter ou nuvem utilizando o Trabalhador do Livro de Corridas Híbrido](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Livros de execução PowerShell

Os livros de execução powerShell são baseados no Windows PowerShell. Edita diretamente o código do livro de execução utilizando o editor de texto no portal Azure.  Também pode utilizar qualquer editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Implementar toda a lógica complexa com código PowerShell sem as complexidades adicionais do PowerShell Workflow.
* O livro de corridas começa mais rápido do que os livros de workflow powerShell, uma vez que não precisa de ser compilado antes de correr.
* Pode correr em Azure ou em linux e windows híbrido saqueadores

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o script powerShell.
* Não se pode usar [o processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar múltiplas ações paralelas.
* Não pode usar postos de [controlo](automation-powershell-workflow.md#checkpoints) para retomar o livro de execução se houver um erro.
* Os livros de execução powerShell Workflow e os livros gráficos só podem ser incluídos como livros infantis utilizando o cmdlet Start-AzureAutomationRunbook, o que cria um novo trabalho.

### <a name="known-issues"></a>Problemas Conhecidos

Seguem-se problemas conhecidos atuais com os livros de execução da PowerShell.

* Os livros de execução da PowerShell não conseguem recuperar um [ativo variável](automation-variables.md) não encriptado com um valor nulo.
* Os livros de execução da PowerShell não conseguem recuperar um [ativo variável](automation-variables.md) com *~* o nome.
* O Get-Process num ciclo num livro de corridas da PowerShell pode cair após cerca de 80 iterações.
* Um livro de execução powerShell pode falhar se tentar escrever uma grande quantidade de dados para o fluxo de saída de uma só vez.   Normalmente, pode contornar este problema, colocando apenas a informação de que necessita ao trabalhar com objetos grandes.  Por exemplo, em vez de colocar algo como *Get-Process,* pode sair apenas dos campos necessários com *o Get-Process [ Processo- Processo ] Selecione ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Livros de execução powerShell Workflow

Os livros de execução do PowerShell Workflow são livros de execução de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md).  Edita diretamente o código do livro de execução utilizando o editor de texto no portal Azure.  Também pode utilizar qualquer editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Implementar toda a lógica complexa com o código powerShell Workflow.
* Utilize [os pontos](automation-powershell-workflow.md#checkpoints) de verificação para retomar o rumbook se houver um erro.
* Utilize [o processamento paralelo](automation-powershell-workflow.md#parallel-processing) para realizar múltiplas ações paralelas.
* Pode incluir outros livros de execução gráficos e livros de fluxo de trabalho powerShell como livros de corridas para crianças para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* O autor deve estar familiarizado com o PowerShell Workflow.
* O Livro de Execução deve lidar com a complexidade adicional do PowerShell Workflow, como [objetos desserializados.](automation-powershell-workflow.md#code-changes)
* O runbook demora mais tempo a começar do que os livros powerShell, uma vez que precisa de ser compilado antes de ser executado.
* Os livros de execução powerShell só podem ser incluídos como livros infantis utilizando o cmdlet Start-AzureAutomationRunbook, o que cria um novo trabalho.
* Não se pode correr com um Trabalhador de Livro Híbrido Linux.

## <a name="python-runbooks"></a>Livros de execução python

Os livros de python compilam sob python 2. Pode editar diretamente o código do livro de execução utilizando o editor de texto no portal Azure, ou com um editor de texto offline e importar o livro de [execução](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Utilize as robustas bibliotecas Python.
* Pode correr em Azure ou em ambos os Trabalhadores de Runbook Híbrido Linux. Os trabalhadores do Windows Hybrid Runbook são suportados com [python2.7](https://www.python.org/downloads/release/latest/python2) instalados.

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o guião python.
* Apenas python 2 é suportado no momento, o que significa que funções específicas python 3 falharão.
* Para utilizar bibliotecas de terceiros, deve [importar o pacote](python-packages.md) para a conta Automation para que seja utilizado.

## <a name="considerations"></a>Considerações

Tenha em conta as seguintes considerações adicionais ao determinar qual o tipo a utilizar para um determinado livro de execução.

* Não é possível converter livros de execução de tipo gráfico para textual ou ao contrário.
* Existem limitações usando livros de diferentes tipos como um livro de corridas para crianças. Para mais informações, consulte livros [infantis em Automação Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a autoria de um livro gráfico, consulte [a autoria gráfica em Automação Azure](automation-graphical-authoring-intro.md)
* Para compreender as diferenças entre powerShell e powerShell fluxos de trabalho para livros de execução, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um Livro de Execução, consulte [Criar ou Importar um Livro de Execução](manage-runbooks.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
