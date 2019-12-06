---
title: Tipos de runbook de automação do Azure
description: 'Descreve os diferentes tipos de runbooks que você pode usar na automação do Azure e considerações que você deve levar em conta ao determinar qual tipo usar. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1991b7c227e62efcd8c0b637f3e732d737cabb34
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850691"
---
# <a name="azure-automation-runbook-types"></a>Tipos de runbook de automação do Azure

A automação do Azure dá suporte a vários tipos de runbooks descritos brevemente na tabela a seguir.  As seções a seguir fornecem mais informações sobre cada tipo, incluindo considerações sobre quando usar cada um deles.

| Tipo | Descrição |
|:--- |:--- |
| [Gráficos](#graphical-runbooks)|Baseado no Windows PowerShell e criado e editado completamente no editor gráfico no portal do Azure. |
| [Fluxo de trabalho gráfico do PowerShell](#graphical-runbooks)|Com base no fluxo de trabalho do Windows PowerShell e criado e editado completamente no editor gráfico no portal do Azure. |
| [PowerShell](#powershell-runbooks) |Runbook de texto com base no script do Windows PowerShell. |
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks)|Runbook de texto com base no fluxo de trabalho do Windows PowerShell. |
| [Python](#python-runbooks) |Runbook de texto baseado em Python. |

## <a name="graphical-runbooks"></a>Runbooks gráficos

Os runbooks de [fluxo de trabalho gráfico e gráfico](automation-runbook-types.md#graphical-runbooks) do PowerShell são criados e editados com o editor gráfico no portal do Azure.  Você pode exportá-los para um arquivo e, em seguida, importá-los para outra conta de automação. Mas você não pode criá-los ou editá-los com outra ferramenta. Runbooks gráficos geram código do PowerShell, mas você não pode exibir ou modificar o código diretamente. Os runbooks gráficos não podem ser convertidos em um dos [formatos de texto](automation-runbook-types.md), nem um runbook de texto pode ser convertido em formato gráfico. Os runbooks gráficos podem ser convertidos em runbooks gráficos do fluxo de trabalho do PowerShell durante a importação e o contrário.

### <a name="advantages"></a>Vantagens

* Visual Insert-link-configurar modelo de criação
* Concentre-se em como os dados fluem pelo processo
* Representar visualmente os processos de gerenciamento
* Incluir outros runbooks como runbooks filho para criar fluxos de trabalho de alto nível
* Incentiva a programação modular

### <a name="limitations"></a>Limitações

* Não é possível editar o runbook fora do portal do Azure.
* Pode exigir uma atividade de código que contém o código do PowerShell para executar lógica complexa.
* Não é possível exibir ou editar diretamente o código do PowerShell que é criado pelo fluxo de trabalho gráfico. Você pode exibir o código criado em qualquer atividade de código.
* Não pode ser executado em um Hybrid Runbook Worker Linux

## <a name="powershell-runbooks"></a>Runbooks do PowerShell

Os runbooks do PowerShell são baseados no Windows PowerShell.  Você edita diretamente o código do runbook usando o editor de texto na portal do Azure.  Você também pode usar qualquer editor de texto offline e [importar o runbook para a](manage-runbooks.md) automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com código do PowerShell sem as complexidades adicionais de fluxo de trabalho do PowerShell.
* Runbook inicia-se mais rapidamente do que os runbooks do fluxo de trabalho do PowerShell, uma vez que ele não precisa ser compilado antes de executar.
* Pode ser executado no Azure ou em trabalhos de runbook híbridos do Linux e do Windows

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com o script do PowerShell.
* Não é possível usar o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações em paralelo.
* Não é possível usar [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook se houver um erro.
* Runbooks de fluxo de trabalho do PowerShell e runbooks gráficos só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.

### <a name="known-issues"></a>Problemas Conhecidos

A seguir estão os problemas conhecidos atuais com runbooks do PowerShell.

* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) não criptografado com um valor nulo.
* Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) com *~* no nome.
* O Get-Process em um loop em um runbook do PowerShell pode falhar após cerca de 80 iterações.
* Um runbook do PowerShell poderá falhar se tentar gravar uma grande quantidade de dados no fluxo de saída ao mesmo tempo.   Normalmente, você pode contornar esse problema gerando apenas as informações necessárias ao trabalhar com objetos grandes.  Por exemplo, em vez de produzir algo como *Get-Process*, você pode gerar apenas os campos obrigatórios com *Get-Process | Selecione ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooks de fluxo de trabalho do PowerShell

Runbooks de fluxo de trabalho do PowerShell são runbooks de texto baseados no [fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md).  Você edita diretamente o código do runbook usando o editor de texto na portal do Azure.  Você também pode usar qualquer editor de texto offline e [importar o runbook para a](manage-runbooks.md) automação do Azure.

### <a name="advantages"></a>Vantagens

* Implemente toda a lógica complexa com o código de fluxo de trabalho do PowerShell.
* Use [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook se houver um erro.
* Uso [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para efetuar várias ações em paralelo.
* Pode incluir outros runbooks gráficos e runbooks de fluxo de trabalho do PowerShell como runbooks filho para criar fluxos de trabalho de alto nível.

### <a name="limitations"></a>Limitações

* O autor deve estar familiarizado com o fluxo de trabalho do PowerShell.
* O runbook deve lidar com a complexidade adicional do fluxo de trabalho do PowerShell, como [objetos desserializados](automation-powershell-workflow.md#code-changes).
* Demora mais tempo a iniciar-se que os runbooks do PowerShell, uma vez que ele precisa ser compilado antes de executar o Runbook.
* Os runbooks do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.
* Não pode ser executado em um Hybrid Runbook Worker Linux

## <a name="python-runbooks"></a>Runbooks do Python

Os runbooks do Python são compilados no Python 2.  Você pode editar diretamente o código do runbook usando o editor de texto na portal do Azure ou com um editor de texto offline e [importar o runbook para a](manage-runbooks.md) automação do Azure.

### <a name="advantages"></a>Vantagens

* Utilize as bibliotecas robustas do Python.
* Pode ser executado no Azure ou em ambos os Hybrid runbook Workers do Linux. Há suporte para Hybrid runbook Workers do Windows com o [Python 2.7](https://www.python.org/downloads/release/latest/python2) instalado.

### <a name="limitations"></a>Limitações

* Deve estar familiarizado com scripts Python.
* Somente o Python 2 tem suporte no momento, o que significa que as funções específicas do Python 3 falharão.
* Para usar bibliotecas de terceiros, você deve [importar o pacote](python-packages.md) para a conta de automação para que ele seja usado.

## <a name="considerations"></a>Considerações

Leve em consideração as seguintes considerações adicionais ao determinar qual tipo usar para um runbook específico.

* Você não pode converter runbooks de gráfico para tipo textual ou o contrário.
* Há limitações ao usar runbooks de diferentes tipos como um runbook filho. Para obter mais informações, consulte [Runbooks filho na automação do Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a criação de runbook gráfico, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md)
* Para entender as diferenças entre os fluxos de trabalho do PowerShell e do PowerShell para runbooks, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)
* Para obter mais informações sobre como criar ou importar um runbook, consulte [criando ou importando um runbook](manage-runbooks.md)
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
