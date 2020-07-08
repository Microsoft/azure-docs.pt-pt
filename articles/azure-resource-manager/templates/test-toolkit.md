---
title: Kit de ferramentas de teste de modelo ARM
description: Descreve como executar o kit de ferramentas de teste do modelo ARM no seu modelo. O conjunto de ferramentas permite-lhe ver se implementou práticas recomendadas.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255981"
---
# <a name="use-arm-template-test-toolkit"></a>Use o kit de ferramentas de teste do modelo ARM

O [kit de ferramentas de teste do modelo ARM](https://aka.ms/arm-ttk) verifica se o seu modelo utiliza práticas recomendadas. Quando o seu modelo não está em conformidade com as práticas recomendadas, devolve uma lista de avisos com as alterações sugeridas. Ao utilizar o kit de ferramentas de teste, pode aprender a evitar problemas comuns no desenvolvimento do modelo.

O conjunto de ferramentas de teste fornece um [conjunto de testes predefinidos](test-cases.md). Estes testes são recomendações, mas não requisitos. Pode decidir quais os testes que são relevantes para os seus objetivos e personalizar quais os testes que são realizados.

Este artigo descreve como executar o kit de ferramentas de teste e como adicionar ou remover testes. Para descrições dos testes predefinidos, consulte [os casos de teste do toolkit](test-cases.md).

O conjunto de ferramentas é um conjunto de scripts PowerShell que podem ser executados a partir de um comando em PowerShell ou CLI.

## <a name="download-test-toolkit"></a>Baixar kit de ferramentas de teste

Para utilizar o kit de ferramentas de teste, pode ou forquilha e clonar o [repositório](https://aka.ms/arm-ttk) que contém os scripts ou [baixar o ficheiro .zip mais recente](https://aka.ms/arm-ttk-latest).

Dependendo da política de execução do computador onde executa o script, poderá ter um erro ao executar scripts a partir da Internet. Tem de alterar a política de [execução](/powershell/module/microsoft.powershell.core/about/about_execution_policies) ou [desbloquear os ficheiros de scripts](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Executar em PowerShell

Antes de realizar os testes, importe o módulo.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Para escodar os testes no **PowerShell,** utilize o seguinte comando:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Correr em Linux

Antes de escorção dos testes, instale [o PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux).

Para realizar os testes em **Linux** em Bash, utilize o seguinte comando:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

Também pode fazer o teste no pwsh.exe.

## <a name="run-on-macos"></a>Correr no macOS

Antes de escorção dos testes, instale [o PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos). 

Instalar `coreutils`:

```bash
brew install coreutils
```

Para escodar os testes no **macOS,** utilize o seguinte comando:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Formato de resultados

Os testes que passam são apresentados em **verde** e prefaciados com **[+]**.

Os testes que falham são apresentados a **vermelho** e prefaciados com **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="ver resultados de testes":::

Os resultados do texto são:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Parâmetros de teste

Quando fornece o parâmetro **-TemplatePath,** o conjunto de ferramentas olha nessa pasta para um modelo nomeado azuredeploy.jsligado ou maintemplate.jsligado. Testa primeiro este modelo e depois testa todos os outros modelos na pasta e nas suas sub-dobras. Os outros modelos são testados como modelos ligados. Se o seu caminho incluir um ficheiro nomeado [CreateUiDefinition.js,](../managed-applications/create-uidefinition-overview.md)executa testes relevantes para a definição de UI.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Para testar um ficheiro nessa pasta, adicione o parâmetro **-Ficheiro.** No entanto, a pasta deve ainda ter um modelo principal nomeado azuredeploy.jsligado ou maintemplate.js.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Por predefinição, todos os testes são executados. Para especificar os testes individuais a executar, utilize o parâmetro **-Teste.** Forneça o nome do teste. Para obter os nomes, consulte [os casos de teste para o conjunto de ferramentas](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Personalizar testes

Para os modelos ARM, o conjunto de ferramentas executa todos os testes na pasta **\arm-ttk\testcases\deploymentTemplate**. Se pretender remover permanentemente um teste, elimine o ficheiro da pasta.

Para os ficheiros [CreateUiDefinition,](../managed-applications/create-uidefinition-overview.md) executa todos os testes na pasta **\arm-ttk\testcases\CreateUiDefinition**.

Para adicionar o seu próprio teste, crie um ficheiro com a convenção de nomeação: **Your-Custom-Test-Name.test.ps1**.

O teste pode obter o modelo como um parâmetro de objeto ou um parâmetro de corda. Normalmente, usa-se um ou outro, mas pode usar os dois.

Utilize o parâmetro do objeto quando precisar de obter uma secção do gabarito e iterar através das suas propriedades. Um teste que utiliza o parâmetro do objeto tem o seguinte formato:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

O objeto do modelo tem as seguintes propriedades:

* $schema
* parte de conteúdoVersão
* parâmetros
* variáveis
* resources
* saídas

Por exemplo, pode obter a recolha de parâmetros com `$TemplateObject.parameters` .

Utilize o parâmetro de corda quando precisar de fazer uma operação de corda em todo o modelo. Um teste que utiliza o parâmetro de corda tem o seguinte formato:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Por exemplo, pode executar uma expressão regular do parâmetro de corda para ver se uma sintaxe específica é usada.

Para saber mais sobre a implementação do teste, veja os outros testes nessa pasta.

## <a name="integrate-with-azure-pipelines"></a>Integrar-se com gasodutos Azure

Pode adicionar o kit de ferramentas de teste ao seu Pipeline Azure. Com um pipeline, pode executar o teste sempre que o modelo for atualizado, ou executá-lo como parte do seu processo de implementação.

A forma mais fácil de adicionar o kit de ferramentas de teste ao seu pipeline é com extensões de terceiros. Estão disponíveis as seguintes duas extensões:

* [Executar testes ARM TTK](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Teste de modelo de ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Ou pode implementar as suas próprias tarefas. O exemplo a seguir mostra como descarregar o kit de ferramentas de teste.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

O próximo exemplo mostra como fazer os testes.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre os testes predefinidos, consulte [os casos de teste para o conjunto de ferramentas](test-cases.md).
