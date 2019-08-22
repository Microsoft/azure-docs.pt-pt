---
title: Usar um modelo de Azure Resource Manager para criar um espaço de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como usar um modelo de Azure Resource Manager para criar um novo espaço de trabalho de serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 89c24512e50dedbf10d145088ec77c2e6e303d1e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873158"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Usar um modelo de Azure Resource Manager para criar um espaço de trabalho para o serviço Azure Machine Learning

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho de serviço do Azure Machine Learning usando modelos de Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma única operação coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Ele também pode especificar parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para obter mais informações, consulte [implantar um aplicativo com Azure Resource Manager modelo](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se você não tiver uma, experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo de uma CLI, você precisa de [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O seguinte modelo do Resource Manager pode ser usado para criar um espaço de trabalho de serviço Azure Machine Learning e recursos do Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Este modelo cria os seguintes serviços do Azure:

* Grupo de Recursos do Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contêiner que mantém os serviços. Os vários serviços são exigidos pelo espaço de trabalho Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* O **local** onde o grupo de recursos e os serviços serão criados.

    O modelo usará o local que você selecionar para a maioria dos recursos. A exceção é o serviço Application Insights, que não está disponível em todos os locais em que os outros serviços estão. Se você selecionar um local onde ele não está disponível, o serviço será criado no local do EUA Central do Sul.

* O **nome do espaço de trabalho**, que é o nome amigável do espaço de trabalho Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

Para obter mais informações sobre modelos, consulte os seguintes artigos:

* [Criar modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Implantar um aplicativo com modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipos de recurso Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Siga as etapas em [implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando você chegar à tela __Editar modelo__ , Cole o modelo deste documento.
1. Selecione __salvar__ para usar o modelo. Forneça as seguintes informações e concorde com os termos e condições listados:

   * Subscrição: Selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do espaço de trabalho: O nome a ser usado para o espaço de trabalho Azure Machine Learning que será criado. O nome do espaço de trabalho deve ter entre 3 e 33 caracteres. Ele pode conter apenas caracteres alfanuméricos e '-'.
   * Local Selecione o local onde os recursos serão criados.

Para obter mais informações, consulte [implantar recursos do modelo personalizado](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Este exemplo pressupõe que você salvou o modelo em um arquivo chamado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implantar o modelo do Resource Manager privado com o token SAS e Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Este exemplo pressupõe que você salvou o modelo em um arquivo chamado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar o modelo do Resource Manager privado com o token SAS e CLI do Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso de Azure Key Vault e modelos de Azure Resource Manager

Quando você usa um modelo de Azure Resource Manager para criar o espaço de trabalho e os recursos associados (incluindo Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer espaço de trabalho existente que o esteja usando. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM pode falhar.  

Para evitar esse problema, recomendamos uma das seguintes abordagens:

*  Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
  
* Examine as políticas de acesso do Key Vault e use essas políticas para definir a propriedade accessPolicies do modelo.
* Verifique se o recurso de Key Vault já existe. Se tiver, não a recrie por meio do modelo. Por exemplo, adicione um parâmetro que permita desabilitar a criação do recurso de Key Vault se ele já existir.

## <a name="next-steps"></a>Passos seguintes

* [Implante recursos com modelos do Resource Manager e a API REST do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
