---
title: Utilizar um modelo Azure Resource Manager para criar uma área de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar um modelo Azure Resource Manager para criar uma área de trabalho de serviço do Azure Machine Learning nova.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 4e0af3b395ec640fd037a1e76365408c10613340
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477005"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Utilizar um modelo Azure Resource Manager para criar uma área de trabalho para o serviço Azure Machine Learning

Neste artigo, irá aprender as várias formas de criar uma área de trabalho de serviço do Azure Machine Learning utilizando modelos Azure Resource Manager. Um modelo do Resource Manager torna fácil criar recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos que são necessários para uma implementação. Ele também pode especificar parâmetros de implementação. Parâmetros são usados para fornecer valores de entrada ao utilizar o modelo.

Para obter mais informações, consulte [implementar uma aplicação com o modelo Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se não tiver um, experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree).

* Para utilizar um modelo a partir de uma CLI, é necessário um [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O modelo do Resource Manager seguinte pode ser utilizado para criar uma área de trabalho do serviço do Azure Machine Learning e os recursos do Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Este modelo cria os seguintes serviços do Azure:

* Grupo de Recursos do Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Registo de Contentores do Azure
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contentor que retém os serviços. Os serviços são necessárias para a área de trabalho do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* O **localização** onde serão criados o grupo de recursos e serviços.

    O modelo irá utilizar a localização que selecionou para a maioria dos recursos. A exceção é o serviço do Application Insights, que não está disponível em todas as localizações que são os outros serviços. Se selecionar uma localização para onde ele não está disponível, o serviço será criado na localização Centro-Sul.

* O **nome da área de trabalho**, que é o nome amigável da área de trabalho do Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

Para obter mais informações sobre modelos, consulte os artigos seguintes:

* [Criar modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Implementar uma aplicação com modelos Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipos de recursos de Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Siga os passos em [implementar recursos de modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando deparar-se com o __Editar modelo__ ecrã, cole o modelo a partir deste documento.
1. Selecione __guardar__ para utilizar o modelo. Forneça as seguintes informações e os Concordo com os listados termos e condições:

   * Subscrição: Selecione a subscrição do Azure a utilizar para estes recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome da área de trabalho: O nome a utilizar para a área de trabalho do Azure Machine Learning que vai ser criada. O nome de área de trabalho tem de ter entre 3 e 33 carateres. Pode conter apenas carateres alfanuméricos e "-".
   * Localização: Selecione a localização onde os recursos serão criados.

     ![Os parâmetros de modelo no portal do Azure](media/how-to-create-workspace-template/template-parameters.png)

Para obter mais informações, consulte [implementar recursos de modelo personalizado](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Este exemplo assume que guardou o modelo num arquivo chamado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implementar privado modelo do Resource Manager com o SAS token e o Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Este exemplo assume que guardou o modelo num arquivo chamado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [implementar privado modelo do Resource Manager com CLI do Azure e o SAS token](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso do Cofre de chaves do Azure e os modelos Azure Resource Manager

Quando utiliza um modelo Azure Resource Manager para criar a área de trabalho e os recursos associados (incluindo o Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implementação e integração contínua.

A maioria das operações de criação de recursos através de modelos são idempotent, mas o Key Vault limpa as políticas de acesso sempre que o modelo é utilizado. Limpar o acesso políticas quebras o acesso ao Key Vault para qualquer área de trabalho existente que está a utilizar. Por exemplo, poderão falhar parar/criar funcionalidades de VM de blocos de notas do Azure.  

Para evitar este problema, recomendamos uma das abordagens seguintes:

*  Não implemente o modelo mais do que uma vez para os mesmos parâmetros. Ou eliminar os recursos existentes antes de utilizar o modelo de recriá-las.
  
* Examine as políticas de acesso do Cofre de chaves e, em seguida, utilizar estas políticas para definir a propriedade accessPolicies do modelo.
* Verifique se o recurso do Key Vault já existe. Se assim for, não recriá-lo através do modelo. Por exemplo, adicione um parâmetro que permite que desabilite a criação do recurso do Key Vault, se já existir.

## <a name="next-steps"></a>Passos Seguintes

* [Implementar recursos com modelos do Resource Manager e API de REST do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criar e implementar grupos de recursos do Azure através do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
