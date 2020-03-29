---
title: Visão geral do livro de execução Azure Automation Grpahical SDK
description: Este artigo descreve como usar o Azure Automation Graphical Runbook SDK
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: d4dcf6681ade977847c204dd1237f7cd7a67775e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75418251"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Utilize o livro de execução gráfica Azure Automation SDK

[Os livros de execução gráficos](automation-graphical-authoring-intro.md) são livros de execução que ajudam a gerir as complexidades do código de fluxo de trabalho do Windows PowerShell ou powerShell. O Microsoft Azure Automation Graphical Authoring SDK permite aos desenvolvedores criar e editar Os Rinc-Guias Gráficos para utilização com o serviço De automação Azure. Os seguintes fragmentos de código mostram o fluxo básico de criação de um livro gráfico a partir do seu código.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, `Microsoft.Azure.Automation.GraphicalRunbook.Model` importe o pacote para o seu projeto.

## <a name="create-a-runbook-object-instance"></a>Criar uma instância de objeto de livro de execução

Referenciar `Orchestrator.GraphRunbook.Model` o conjunto e `Orchestrator.GraphRunbook.Model.GraphRunbook` criar uma instância da classe:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Adicionar parâmetros de livro

Objetos `Orchestrator.GraphRunbook.Model.Parameter` instantâneos e adicioná-los ao livro de corridas:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Adicionar atividades e links

Atividades instantâneas de tipos apropriados e adicioná-las ao livro de execução:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

As atividades são implementadas `Orchestrator.GraphRunbook.Model` pelas seguintes aulas no espaço de nome:

|Classe  |Atividade  |
|---------|---------|
|Atividade de Comando     | Invoca um comando PowerShell (cmdlet, função, etc.).        |
|Atividade de InvokeRunbook     | Invoca outro livro de execução.        |
|Atividade de Junção     | Espera que todos os ramos que chegam terminem.        |
|Fluxo de trabalhoScriptActivity     | Executa um bloco de PowerShell ou PowerShell Workflow (dependendo do tipo de livro de execução) no contexto do livro de execução. Esta é uma ferramenta poderosa, mas não a use excessivamente: a UI mostrará este bloco de scripts como texto; o motor de execução tratará o bloco fornecido como uma caixa preta, e não tentará analisar o seu conteúdo, exceto para uma verificação de sintaxe básica. Se precisar apenas de invocar um único comando PowerShell, prefira o CommandActivity.        |

> [!NOTE]
> Não obtenha as suas próprias atividades a partir das classes fornecidas: a Azure Automation não poderá utilizar livros de execução com tipos de atividade personalizadas.

ComandoSActividade e InvocaçãoOs parâmetros de atividade devem ser fornecidos como descritores de valor e não valores diretos. Os descritores de valor especificam como os valores reais do parâmetro devem ser produzidos. Os seguintes descritores de valor são fornecidos atualmente:


|Descritor  |Definição  |
|---------|---------|
|Scriptscriptor ConstantValue     | Refere-se a um valor constante codificado.        |
|RunbookParameterValueDescriptor     | Refere-se a um parâmetro de livro por nome.        |
|ActivityOutputValueDescriptor     | Refere-se a uma produção de atividade a montante, permitindo que uma atividade "subscreva" os dados produzidos por outra atividade.        |
|AutomaçãoVariableValueDescriptor     | Refere-se a um ativo de Variável de Automação pelo nome.         |
|AutomaçãoCredentialValueDescript     | Refere-se a um ativo de Certificado de Automação pelo nome.        |
|AutomaçãoConnectionValueDescriptor     | Refere-se a um ativo de Ligação de Automação pelo nome.        |
|PowerShellExpressionValueDescriptor     | Especifica uma expressão PowerShell de forma livre que será avaliada pouco antes de invocar a atividade.  <br/>Esta é uma ferramenta poderosa, mas não a use excessivamente: a UI mostrará esta expressão como texto; o motor de execução tratará o bloco fornecido como uma caixa preta, e não tentará analisar o seu conteúdo, exceto para uma verificação de sintaxe básica. Quando possível, prefira descritores de valor mais específicos.      |

> [!NOTE]
> Não obtenha os seus próprios descritores de valor das classes fornecidas: a Automatização Azure não poderá utilizar livros de execução com tipos de descritores de valor personalizado.

Ligações instantâneas que ligam atividades de ligação e as adicionam ao livro de execução:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Guarde o livro de execução para um ficheiro

Utilize `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` para serializar um livro de execução numa corda:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Esta cadeia pode ser guardada num ficheiro com a extensão **.graphrunbook,** e este ficheiro pode ser importado para a Automação Azure.
O formato serializado pode mudar `Orchestrator.GraphRunbook.Model.dll`nas futuras versões de . Prometemos retrocompatibilidade: qualquer livro de corridas serializado `Orchestrator.GraphRunbook.Model.dll` com uma versão mais antiga pode ser desserializado por qualquer versão mais recente. A compatibilidade para a frente não está garantida: um livro de corridas serializado com uma versão mais recente pode não ser desserializável por versões mais antigas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Runbooks Gráficos na Automação Azure, consulte [introdução de AutorEs Gráficos](automation-graphical-authoring-intro.md)

