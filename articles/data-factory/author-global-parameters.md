---
title: Parâmetros globais
description: Desconfiem de parâmetros globais para cada um dos seus ambientes de Fábrica de Dados Azure
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854355"
---
# <a name="global-parameters-in-azure-data-factory"></a>Parâmetros globais na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os parâmetros globais são constantes em toda uma fábrica de dados que pode ser consumida por um oleoduto em qualquer expressão. São úteis quando se tem vários oleodutos com nomes e valores de parâmetros idênticos. Ao promover uma fábrica de dados utilizando o processo de integração e implantação contínua (CI/CD), pode sobrepor-se a estes parâmetros em cada ambiente. 

## <a name="creating-global-parameters"></a>Criação de parâmetros globais

Para criar um parâmetro global, aceda ao separador *parâmetros Global* na secção *Gerir.* Selecione **Novo** para abrir a criação side-nav.

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-1.png)

Na navegação lateral, introduza um nome, selecione um tipo de dados e especifique o valor do seu parâmetro.

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-2.png)

Depois de criado um parâmetro global, pode editá-lo clicando no nome do parâmetro. Para alterar vários parâmetros de uma só vez, **selecione Editar todos**.

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Usando parâmetros globais em um oleoduto

Os parâmetros globais podem ser utilizados em qualquer [expressão de gasoduto](control-flow-expression-language-functions.md). Se um pipeline estiver a referir outro recurso, como um conjunto de dados ou fluxo de dados, pode passar o valor global do parâmetro através dos parâmetros desse recurso. Os parâmetros globais são referenciados como `pipeline().globalParameters.<parameterName>` .

![Usando parâmetros globais](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>Parâmetros globais em CI/CD

Os parâmetros globais têm um processo ci/CD único em relação a outras entidades na Azure Data Factory. Ao publicar uma fábrica ou exportar um modelo ARM com parâmetros globais, uma pasta chamada *GlobalParameters* é criada com um ficheiro chamado *your-factory-name_GlobalParameters.jsem*. Este ficheiro é um objeto JSON que contém cada tipo de parâmetro e valor global na fábrica publicada.

![Publicar parâmetros globais](media/author-global-parameters/global-parameters-adf-publish.png)

Se estiver a implantar para um novo ambiente, como TEST ou PROD, recomenda-se criar uma cópia deste ficheiro de parâmetros globais e substituir os valores específicos do ambiente adequados. Quando publicares o ficheiro de parâmetros globais originais será substituído, mas a cópia para o outro ambiente não será tocada.

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