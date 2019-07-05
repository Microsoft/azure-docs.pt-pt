---
title: Descrição geral do runbook Grpahical de automatização do Azure SDK
description: Este artigo descreve como utilizar o SDK de Runbook de gráfico de automatização do Azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e4229079a1fa50295eef85b42f91bbc1b4a21fc3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478593"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Utilizar o SDK de runbook gráfico de automatização do Azure

[Os runbooks gráficos](automation-graphical-authoring-intro.md) são runbooks que ajuda a gerenciar as complexidades do código subjacente do Windows PowerShell ou o fluxo de trabalho do PowerShell. A automatização gráfica criação SDK do Microsoft Azure permite aos programadores criar e editar Runbooks gráficos para utilização com o serviço de automatização do Azure. Os fragmentos de código seguintes mostram o fluxo básico de criação de um runbook gráfico a partir do código.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, importar o `Microsoft.Azure.Automation.GraphicalRunbook.Model` pacote para o seu projeto.

## <a name="create-a-runbook-object-instance"></a>Criar uma instância de objeto do runbook

Referência a `Orchestrator.GraphRunbook.Model` assembly e criar uma instância do `Orchestrator.GraphRunbook.Model.GraphRunbook` classe:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Adicionar parâmetros do runbook

Criar uma instância `Orchestrator.GraphRunbook.Model.Parameter` objetos e adicioná-los ao runbook:

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

## <a name="add-activities-and-links"></a>Adicionar atividades e ligações

Criar uma instância de atividades de tipos apropriados e adicioná-los ao runbook:

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

Atividades são implementadas com as seguintes classes no `Orchestrator.GraphRunbook.Model` espaço de nomes:

|Classe  |Atividade  |
|---------|---------|
|CommandActivity     | Invoca um comando do PowerShell (cmdlet, função, etc.).        |
|InvokeRunbookActivity     | Invoca outro runbook inline.        |
|JunctionActivity     | Aguarda todos os ramos de entrada concluir.        |
|WorkflowScriptActivity     | Executa um bloco de código do PowerShell ou o fluxo de trabalho do PowerShell (consoante o tipo de runbook) no contexto do runbook. Esta é uma ferramenta poderosa, mas não uso excessivo-lo: a interface do Usuário irá mostrar este bloco de script como texto; o motor de execução tratará o bloco fornecido como uma caixa preta e fará com que não existem tentativas para analisar seu conteúdo, exceto para uma verificação de sintaxe básica. Se precisar apenas invocar um único comando do PowerShell, prefira CommandActivity.        |

> [!NOTE]
> Não derive suas próprias atividades a partir das classes fornecidas: A automatização do Azure não poderão utilizar runbooks com tipos de atividade personalizada.

Parâmetros CommandActivity e InvokeRunbookActivity tem de ser fornecidos como descritores de valor, valores não diretos. Descritores de valor especificar como os valores de parâmetro real devem ser produzidos. Atualmente são fornecidos os descritores de valor seguinte:


|Descritor  |Definição  |
|---------|---------|
|ConstantValueDescriptor     | Refere-se para um valor constante hard-coded.        |
|RunbookParameterValueDescriptor     | Refere-se a um parâmetro de runbook por nome.        |
|ActivityOutputValueDescriptor     | Refere-se a uma atividade a montante de saída, permitindo que uma atividade a "assinar" dados produzidos por outra atividade.        |
|AutomationVariableValueDescriptor     | Refere-se a um recurso de variável de automatização por nome.         |
|AutomationCredentialValueDescriptor     | Refere-se a um recurso de certificado de automatização por nome.        |
|AutomationConnectionValueDescriptor     | Refere-se a um recurso de ligação de automatização por nome.        |
|PowerShellExpressionValueDescriptor     | Especifica uma expressão do PowerShell de forma livre que será avaliada apenas antes de invocar a atividade.  <br/>Esta é uma ferramenta poderosa, mas não uso excessivo-lo: a interface do Usuário irá mostrar essa expressão como texto; o motor de execução tratará o bloco fornecido como uma caixa preta e fará com que não existem tentativas para analisar seu conteúdo, exceto para uma verificação de sintaxe básica. Sempre que possível, prefiro descritores de valor mais específicas.      |

> [!NOTE]
> Não derive seus próprios descritores de valor das classes fornecidas: A automatização do Azure não poderão utilizar runbooks com tipos de descritor de valor personalizado.

Criar uma instância de links ligar atividades e adicioná-los ao runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Guarde o runbook para um ficheiro

Utilize `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` para serializar um runbook para uma cadeia de caracteres:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Essa cadeia de caracteres pode ser guardada num ficheiro com o **. graphrunbook** extensão e este ficheiro podem ser importado para automatização do Azure.
O formato serializado podem ser alterados no futuro versões do `Orchestrator.GraphRunbook.Model.dll`. Prometemos compatibilidade com versões anteriores: qualquer runbook serializado com uma versão antiga do `Orchestrator.GraphRunbook.Model.dll` possa ser desserializado com qualquer versão mais recente. Compatibilidade com versões posteriores não é garantida: um runbook serializado com uma versão mais recente pode não ser são desserializáveis por versões mais antigas.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre Runbooks gráficos na automatização do Azure, veja [introdução de criação de gráficos](automation-graphical-authoring-intro.md)

