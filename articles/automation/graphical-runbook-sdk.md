---
title: Utilize o runbook gráfico Azure Automation SDK (pré-visualização)
description: Este artigo diz como utilizar o runbook gráfico Azure Automation SDK (pré-visualização).
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 969e60cd08a65adb1dd731aa7c6c3f9872e288fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83835041"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk-preview"></a>Utilize o runbook gráfico Azure Automation SDK (pré-visualização)

[Os livros gráficos](automation-graphical-authoring-intro.md) ajudam a gerir as complexidades do código de trabalho do Windows PowerShell ou do PowerShell. A autoria gráfica da Microsoft Azure Automation permite aos desenvolvedores criar e editar runbooks gráficos para uso com Azure Automation. Este artigo descreve passos básicos na criação de um livro gráfico a partir do seu código.

## <a name="prerequisites"></a>Pré-requisitos

Importe o `Orchestrator.GraphRunbook.Model.dll` pacote descarregando o [SDK.](https://www.microsoft.com/download/details.aspx?id=50734)

## <a name="create-a-runbook-object-instance"></a>Criar uma instância de objeto de runbook

Fazer referência à `Orchestrator.GraphRunbook.Model` montagem e criar uma instância da `Orchestrator.GraphRunbook.Model.GraphRunbook` classe:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Adicionar parâmetros de livro de execução

Instantiar `Orchestrator.GraphRunbook.Model.Parameter` objetos e adicioná-los ao livro de bordo:

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

Instantiizar as atividades dos tipos apropriados e adicioná-los ao livro de bordo:

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

As atividades são implementadas pelas seguintes classes no `Orchestrator.GraphRunbook.Model` espaço de nomes.

|Classe  |Atividade  |
|---------|---------|
|Atividade de Comando     | Invoca um comando PowerShell (cmdlet, função, etc.).        |
|Invocar AAAAAção do Livro DeReserdação     | Invoca outro livro de bordo.        |
|JunçãoActivity     | Espera que todos os ramos que chegam terminem.        |
|WorkflowScriptActivity     | Executa um bloco de código de fluxo de trabalho PowerShell ou PowerShell (dependendo do tipo de livro de execução) no contexto do livro de execução. Esta é uma ferramenta poderosa, mas não a exutilará excessivamente: a UI mostrará este bloco de scripts como texto; o motor de execução tratará o bloco fornecido como uma caixa preta, e não tentará analisar o seu conteúdo, exceto para uma verificação básica da sintaxe. Se só precisa de invocar um único comando PowerShell, prefere o CommandActivity.        |

> [!NOTE]
> Não desmente as suas próprias atividades das aulas fornecidas. A Azure Automation não pode utilizar livros com tipos de atividade personalizados.

Deve fornecer `CommandActivity` e `InvokeRunbookActivity` parâmetros como descritores de valor, não valores diretos. Os descritores de valor especificam como produzir os valores reais dos parâmetros. Os seguintes descritores de valor são fornecidos atualmente:


|Descritor  |Definição  |
|---------|---------|
|ConstantValueDscriptor     | Refere-se a um valor constante codificado.        |
|RunbookParameterValueDescriptor     | Refere-se a um parâmetro de livro de bordo pelo nome.        |
|Repartidor de Avaliação de Atividades     | Refere-se a uma produção de atividade a montante, permitindo que uma atividade "subscreva" os dados produzidos por outra atividade.        |
|Automation VariaableValueDescriptor     | Refere-se a um ativo de Variável de Automação pelo nome.         |
|Automation     | Refere-se a um ativo certificado de automação pelo nome.        |
|AutomationConnectionValueDescriptor     | Refere-se a um ativo de Ligação de Automação pelo nome.        |
|PowerShellExpressionValueDescriptor     | Especifica uma expressão PowerShell de forma livre que será avaliada pouco antes de invocar a atividade.  <br/>Esta é uma ferramenta poderosa, mas não a exutilará excessivamente: a UI mostrará esta expressão como texto; o motor de execução tratará o bloco fornecido como uma caixa preta, e não tentará analisar o seu conteúdo, exceto para uma verificação básica da sintaxe. Quando possível, prefira descritores de valor mais específicos.      |

> [!NOTE]
> Não desmente os seus próprios descritores de valor das aulas fornecidas. A Azure Automation não pode utilizar livros com tipos de descritores de valor personalizado.

Ligações instantâneas que ligam as atividades e adicioná-las ao livro de bordo:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Guarde o livro de execução para um ficheiro

Use `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` para serializar um livro de bordo para uma corda:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Pode guardar este fio para um ficheiro com a extensão **.graphrunbook.** O diário correspondente pode ser importado para a Azure Automation.
O formato serializado pode mudar nas futuras versões de `Orchestrator.GraphRunbook.Model.dll` . Prometemos retrocompatibilidade: qualquer livro serializado com uma versão mais antiga `Orchestrator.GraphRunbook.Model.dll` pode ser desseerializado por qualquer versão mais recente. A compatibilidade para a frente não é garantida: um livro de corridas serializado com uma versão mais recente pode não ser desercializável por versões mais antigas.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [os livros gráficos do Autor na Azure Automation.](automation-graphical-authoring-intro.md)