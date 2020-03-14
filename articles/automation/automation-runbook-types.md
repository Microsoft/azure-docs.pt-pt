---
title: Tipos de livro de automação azure
description: 'Descreve os diferentes tipos de livros de execução que pode utilizar na Automação Azure e considera ções que deve ter em conta ao determinar que tipo usar. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278613"
---
# <a name="azure-automation-runbook-types"></a>Tipos de livro de execução da Automação Azure

A Azure Automation suporta vários tipos de livros de execução que são brevemente descritos na tabela seguinte.  As secções abaixo fornecem mais informações sobre cada tipo, incluindo considerações sobre quando usar cada um.

| Tipo | Descrição |
|:--- |:--- |
| [Gráficos](#graphical-runbooks)|Baseado no Windows PowerShell e criado e editado completamente em editor gráfico no portal Azure. |
| [Fluxo de trabalho de PowerShell Graphical PowerShell](#graphical-runbooks)|Baseado no Windows PowerShell Workflow e criado e editado completamente no editor gráfico no portal Azure. |
| [PowerShell](#powershell-runbooks) |Runbook de texto com base no script do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Runbook de texto com base no fluxo de trabalho do Windows PowerShell. |
| [python](#python-runbooks) |Livro de texto baseado em Python. |

## <a name="graphical-runbooks"></a>Livros gráficos

Os livros de execução de fluxo de trabalho de PowerShell [graphical](automation-runbook-types.md#graphical-runbooks) e gráfico são criados e editados com o editor gráfico no portal Azure.  Pode exportá-los para um ficheiro e depois importá-los para outra conta de automação. Mas não se pode criá-los ou editá-los com outra ferramenta. Os livros gráficos geram código PowerShell, mas não pode visualizar ou modificar diretamente o código. Os livros de execução gráficos não podem ser convertidos para um dos [formatos](automation-runbook-types.md)de texto, nem um livro de texto pode ser convertido em formato gráfico. Os livros gráficos podem ser convertidos para livros de fluxo de trabalho graphical PowerShell durante a importação e o contrário.

### <a name="advantages"></a>Vantagens

* Modelo de autor de inserção-link-configuração de ligação visual
* Concentre-se na forma como os dados fluem através do processo
* Visualmente representam processos de gestão
* Inclua outros livros de execução como livros infantis para criar fluxos de trabalho de alto nível
* Incentiva a programação modular

### <a name="limitations"></a>Limitações

* Não se pode editar o livro fora do portal Azure.
* Pode exigir uma atividade de Código contendo código PowerShell para executar lógica complexa.
* Não é possível visualizar ou editar diretamente o código PowerShell que é criado pelo fluxo de trabalho gráfico. Pode ver o código que cria em qualquer atividade do Código.
* Não se pode correr num Trabalhador de Runbook Híbrido Linux

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell são baseados no Windows PowerShell.  Edita diretamente o código do livro de execução utilizando o editor de texto no portal Azure.  Também pode utilizar qualquer editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com código do PowerShell sem as complexidades adicionais de fluxo de trabalho do PowerShell.
* Runbook inicia-se mais rapidamente do que os runbooks do fluxo de trabalho do PowerShell, uma vez que ele não precisa ser compilado antes de executar.
* Pode ser executado em Azure ou em linux e Windows Hybrid Runbook Workers

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o script powerShell.
* Não se pode usar [o processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar múltiplas ações paralelas.
* Não pode usar postos de [controlo](automation-powershell-workflow.md#checkpoints) para retomar o livro de execução se houver um erro.
* Os livros de execução powerShell Workflow e os livros gráficos só podem ser incluídos como livros infantis utilizando o cmdlet Start-AzureAutomationRunbook, o que cria um novo trabalho.

### <a name="known-issues"></a>Problemas Conhecidos

Seguem-se problemas conhecidos atuais com os livros de execução da PowerShell.

* Os livros de execução da PowerShell não conseguem recuperar um [ativo variável](automation-variables.md) não encriptado com um valor nulo.
* Os livros de execução da PowerShell não conseguem recuperar um [ativo variável](automation-variables.md) com *~* no nome.
* O Get-Process num ciclo num livro de corridas da PowerShell pode cair após cerca de 80 iterações.
* Um livro de execução powerShell pode falhar se tentar escrever uma grande quantidade de dados para o fluxo de saída de uma só vez.   Normalmente, pode contornar este problema, colocando apenas a informação de que necessita ao trabalhar com objetos grandes.  Por exemplo, em vez de colocar algo como *Get-Process,* pode sair apenas dos campos necessários com *o Get-Process [ Processo- Processo ] Selecione ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Livros de execução powerShell Workflow

Os livros de execução do PowerShell Workflow são livros de execução de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md).  Edita diretamente o código do livro de execução utilizando o editor de texto no portal Azure.  Também pode utilizar qualquer editor de texto offline e importar o livro de [corridas](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código de fluxo de trabalho do PowerShell.
* Utilize [os pontos](automation-powershell-workflow.md#checkpoints) de verificação para retomar o rumbook se houver um erro.
* Utilize [o processamento paralelo](automation-powershell-workflow.md#parallel-processing) para realizar múltiplas ações paralelas.
* Pode incluir outros livros de execução gráficos e livros de fluxo de trabalho powerShell como livros de corridas para crianças para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* O autor deve estar familiarizado com o PowerShell Workflow.
* O Livro de Execução deve lidar com a complexidade adicional do PowerShell Workflow, como [objetos desserializados.](automation-powershell-workflow.md#code-changes)
* Demora mais tempo a iniciar-se que os runbooks do PowerShell, uma vez que ele precisa ser compilado antes de executar o Runbook.
* Os livros de execução powerShell só podem ser incluídos como livros infantis utilizando o cmdlet Start-AzureAutomationRunbook, o que cria um novo trabalho.
* Não se pode correr num Trabalhador de Runbook Híbrido Linux

## <a name="python-runbooks"></a>Livros de execução python

Os livros de python compilam sob python 2.  Pode editar diretamente o código do livro de execução utilizando o editor de texto no portal Azure, ou com um editor de texto offline e importar o livro de [execução](manage-runbooks.md) para a Automação Azure.

### <a name="advantages"></a>Vantagens

* Utilize as robustas bibliotecas Python.
* Pode ser executado em Azure ou em ambos os Trabalhadores de Runbook Híbrido Linux. Os trabalhadores do Windows Hybrid Runbook são suportados com [python2.7](https://www.python.org/downloads/release/latest/python2) instalados.

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o guião python.
* Apenas python 2 é suportado no momento, o que significa que funções específicas python 3 falharão.
* Para utilizar bibliotecas de terceiros, deve [importar o pacote](python-packages.md) para a Conta de Automação para que seja utilizado.

## <a name="considerations"></a>Considerações

Tenha em conta as seguintes considerações adicionais ao determinar qual o tipo a utilizar para um determinado livro de execução.

* Não é possível converter livros de execução de tipo gráfico para textual ou ao contrário.
* Existem limitações usando livros de diferentes tipos como um livro de corridas para crianças. Para mais informações, consulte livros [infantis em Automação Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a autoria de um livro gráfico, consulte [a autoria gráfica em Automação Azure](automation-graphical-authoring-intro.md)
* Para compreender as diferenças entre powerShell e powerShell fluxos de trabalho para livros de execução, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um Livro de Execução, consulte [Criar ou Importar um Livro de Execução](manage-runbooks.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
