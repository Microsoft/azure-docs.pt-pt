---
title: Visão geral do SDK do runbook do Grpahical de automação do Azure
description: Este artigo descreve como usar o SDK de runbook gráfico de automação do Azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a06c190931fdd0f49132f815b153c08ece68c9f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849552"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Usar o SDK de runbook gráfico da automação do Azure

[Runbooks gráficos](automation-graphical-authoring-intro.md) são runbooks que ajudam a gerenciar as complexidades do código subjacente do Windows PowerShell ou do fluxo de trabalho do PowerShell. O SDK de criação gráfica de automação Microsoft Azure permite que os desenvolvedores criem e editem Runbooks gráficos para uso com o serviço de automação do Azure. Os trechos de código a seguir mostram o fluxo básico de criação de um runbook gráfico do seu código.

## <a name="pre-requisites"></a>Pré-requisitos

Para iniciar, importe o pacote de `Microsoft.Azure.Automation.GraphicalRunbook.Model` em seu projeto.

## <a name="create-a-runbook-object-instance"></a>Criar uma instância de objeto de runbook

Referencie o assembly `Orchestrator.GraphRunbook.Model` e crie uma instância da classe `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Adicionar parâmetros de runbook

Instancie `Orchestrator.GraphRunbook.Model.Parameter` objetos e adicione-os ao runbook:

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

Instancie atividades de tipos apropriados e adicione-as ao runbook:

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

As atividades são implementadas pelas seguintes classes no namespace `Orchestrator.GraphRunbook.Model`:

|Classe  |Atividade  |
|---------|---------|
|CommandActivity     | Invoca um comando do PowerShell (cmdlet, função, etc.).        |
|InvokeRunbookActivity     | Invoca outro runbook embutido.        |
|JunctionActivity     | Aguarda a conclusão de todas as ramificações de entrada.        |
|WorkflowScriptActivity     | Executa um bloco de código de fluxo de trabalho do PowerShell ou do PowerShell (dependendo do tipo de runbook) no contexto do runbook. Essa é uma ferramenta poderosa, mas não está em excesso: a interface do usuário mostrará esse bloco de script como texto; o mecanismo de execução tratará o bloco fornecido como uma caixa preta e não fará nenhuma tentativa de analisar seu conteúdo, com exceção de uma verificação de sintaxe básica. Se você só precisar invocar um único comando do PowerShell, prefira CommandActivity.        |

> [!NOTE]
> Não derive suas próprias atividades das classes fornecidas: a automação do Azure não poderá usar runbooks com tipos de atividade personalizados.

Os parâmetros CommandActivity e InvokeRunbookActivity devem ser fornecidos como descritores de valor, não valores diretos. Os descritores de valor especificam como os valores de parâmetro reais devem ser produzidos. Os descritores de valor a seguir são fornecidos atualmente:


|Descritor  |Definição  |
|---------|---------|
|ConstantValueDescriptor     | Refere-se a um valor constante embutido em código.        |
|RunbookParameterValueDescriptor     | Refere-se a um parâmetro de runbook por nome.        |
|ActivityOutputValueDescriptor     | Refere-se a uma saída de atividade upstream, permitindo que uma atividade "Assine" os dados produzidos por outra atividade.        |
|AutomationVariableValueDescriptor     | Refere-se a um ativo variável de automação por nome.         |
|AutomationCredentialValueDescriptor     | Refere-se a um ativo de certificado de automação por nome.        |
|AutomationConnectionValueDescriptor     | Refere-se a um ativo de conexão de automação por nome.        |
|PowerShellExpressionValueDescriptor     | Especifica uma expressão do PowerShell de forma livre que será avaliada pouco antes de invocar a atividade.  <br/>Essa é uma ferramenta poderosa, mas não está em excesso: a interface do usuário mostrará essa expressão como texto; o mecanismo de execução tratará o bloco fornecido como uma caixa preta e não fará nenhuma tentativa de analisar seu conteúdo, com exceção de uma verificação de sintaxe básica. Quando possível, prefira descritores de valor mais específicos.      |

> [!NOTE]
> Não derive seus próprios descritores de valor das classes fornecidas: a automação do Azure não poderá usar runbooks com tipos de descritor de valor personalizado.

Instancie links conectando atividades e adicione-os ao runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Salvar o runbook em um arquivo

Use `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` para serializar um runbook para uma cadeia de caracteres:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Essa cadeia de caracteres pode ser salva em um arquivo com a extensão **. graphrunbook** , e esse arquivo pode ser importado para a automação do Azure.
O formato serializado pode mudar nas versões futuras do `Orchestrator.GraphRunbook.Model.dll`. Prometemos compatibilidade com versões anteriores: qualquer runbook serializado com uma versão mais antiga do `Orchestrator.GraphRunbook.Model.dll` pode ser desserializado por qualquer versão mais recente. A compatibilidade de encaminhamento não é garantida: um runbook serializado com uma versão mais recente pode não ser desserializável por versões mais antigas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Runbooks gráficos na automação do Azure, confira [introdução de criação gráfica](automation-graphical-authoring-intro.md)

