---
title: Parâmetros globais
description: Desconfiem de parâmetros globais para cada um dos seus ambientes de Fábrica de Dados Azure
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 03/04/2021
ms.openlocfilehash: 06d04eb8679b4484f330b69a8cffb263d353bdcd
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197870"
---
# <a name="global-parameters-in-azure-data-factory"></a>Parâmetros globais na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os parâmetros globais são constantes em toda uma fábrica de dados que pode ser consumida por um oleoduto em qualquer expressão. São úteis quando se tem vários oleodutos com nomes e valores de parâmetros idênticos. Ao promover uma fábrica de dados utilizando o processo de integração e implantação contínua (CI/CD), pode sobrepor-se a estes parâmetros em cada ambiente. 

## <a name="creating-global-parameters"></a>Criação de parâmetros globais

Para criar um parâmetro global, aceda ao separador *parâmetros Global* na secção **Gerir.** Selecione **Novo** para abrir a criação side-nav.

![Screenshot que realça o novo botão que seleciona para criar parâmetros globais.](media/author-global-parameters/create-global-parameter-1.png)

Na navegação lateral, introduza um nome, selecione um tipo de dados e especifique o valor do seu parâmetro.

![Screenshot que mostra onde você adiciona o nome, tipo de dados e valor para o novo parâmetro global.](media/author-global-parameters/create-global-parameter-2.png)

Depois de criado um parâmetro global, pode editá-lo clicando no nome do parâmetro. Para alterar vários parâmetros de uma só vez, **selecione Editar todos**.

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Usando parâmetros globais em um oleoduto

Os parâmetros globais podem ser utilizados em qualquer [expressão de gasoduto](control-flow-expression-language-functions.md). Se um pipeline estiver a referir outro recurso, como um conjunto de dados ou fluxo de dados, pode passar o valor global do parâmetro através dos parâmetros desse recurso. Os parâmetros globais são referenciados como `pipeline().globalParameters.<parameterName>` .

![Usando parâmetros globais](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Parâmetros globais em CI/CD

Existem duas formas de integrar parâmetros globais na sua solução contínua de integração e implantação:

* Incluir parâmetros globais no modelo ARM
* Implementar parâmetros globais através de um script PowerShell

Para a maioria dos casos de utilização, recomenda-se incluir parâmetros globais no modelo ARM. Isto integrar-se-á de forma nativa com a solução delineada [no doc CI/CD](continuous-integration-deployment.md). Os parâmetros globais serão adicionados como um parâmetro do modelo ARM por padrão, uma vez que muitas vezes mudam de ambiente para ambiente. Pode ativar a inclusão de parâmetros globais no modelo ARM a partir do hub **Manage.**

> [!NOTE]
> A configuração **do modelo Incluir no ARM** só está disponível em "Modo Git". Atualmente está desativado no modo "modo vivo" ou no modo "Data Factory".

![Incluir no modelo ARM](media/author-global-parameters/include-arm-template.png)

A adição de parâmetros globais ao modelo ARM adiciona uma definição de nível de fábrica que irá sobrepor-se a outras definições de nível de fábrica, tais como uma chave gerida pelo cliente ou uma configuração de git em outros ambientes. Se tiver estas definições ativadas num ambiente elevado como UAT ou PROD, é melhor implementar parâmetros globais através de um script PowerShell nos passos abaixo realçados.

### <a name="deploying-using-powershell"></a>Implantação utilizando PowerShell

Os passos a seguir descrevem como implementar parâmetros globais através do PowerShell. Isto é útil quando a sua fábrica alvo tem uma definição de nível de fábrica, como a chave gerida pelo cliente.

Ao publicar uma fábrica ou exportar um modelo ARM com parâmetros globais, uma pasta chamada *GlobalParameters* é criada com um ficheiro chamado *your-factory-name_GlobalParameters.jsem*. Este ficheiro é um objeto JSON que contém cada tipo de parâmetro e valor global na fábrica publicada.

![Publicar parâmetros globais](media/author-global-parameters/global-parameters-adf-publish.png)

Se estiver a implementar para um novo ambiente, como TEST ou PROD, é recomendado criar uma cópia deste ficheiro de parâmetros globais e substituir os valores específicos do ambiente apropriados. Quando publicares o ficheiro de parâmetros globais originais será substituído, mas a cópia para o outro ambiente não será tocada.

Por exemplo, se tiver uma fábrica chamada 'ADF-DEV' e um parâmetro global de tipo de cadeia chamado 'ambiente' com um valor 'dev', quando publicar um ficheiro nomeado *ADF-DEV_GlobalParameters.jsserá* gerado. Se for implantado numa fábrica de ensaios denominada "ADF_TEST", crie uma cópia do ficheiro JSON (por exemplo, ADF-TEST_GlobalParameters.js) e substitua os valores dos parâmetros pelos valores específicos do ambiente. O parâmetro 'ambiente' pode agora ter um 'teste' de valor. 

![Implantação de parâmetros globais](media/author-global-parameters/powershell-task.png)

Use o script abaixo do PowerShell para promover parâmetros globais para ambientes adicionais. Adicione uma tarefa Azure PowerShell DevOps antes da implementação do modelo ARM. Na tarefa DevOps, deve especificar a localização do novo ficheiro de parâmetros, do grupo de recursos-alvo e da fábrica de dados-alvo.

> [!NOTE]
> Para implementar parâmetros globais utilizando o PowerShell, deve utilizar pelo menos a versão 4.4.0 do módulo Az.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Passos seguintes

* Conheça o [processo de integração e implementação contínua](continuous-integration-deployment.md) da Azure Data Factory
* Saiba como usar a linguagem de [expressão de fluxo de controlo](control-flow-expression-language-functions.md)
