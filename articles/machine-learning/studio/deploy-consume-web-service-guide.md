---
title: Implantação e consumo
titleSuffix: ML Studio (classic) - Azure
description: Você pode usar Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos do Machine Learning como serviços Web. Esses serviços Web podem ser usados para chamar os modelos de aprendizado de máquina de aplicativos pela Internet para fazer previsões em tempo real ou em modo de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 560158dce3c70049ad3faa11a5ffb9a1858aa445
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837676"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Serviços Web Azure Machine Learning Studio (clássico): implantação e consumo

Você pode usar Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos do Machine Learning como serviços Web. Esses serviços Web podem ser usados para chamar os modelos de aprendizado de máquina de aplicativos pela Internet para fazer previsões em tempo real ou em modo de lote. Como os serviços Web são RESTful, você pode chamá-los de várias linguagens de programação e plataformas, como .NET e Java, e de aplicativos, como o Excel.

As seções a seguir fornecem links para orientações, código e documentação para ajudar você a começar.

## <a name="deploy-a-web-service"></a>Implementar serviços Web

### <a name="with-azure-machine-learning-studio-classic"></a>Com Azure Machine Learning Studio (clássico)

O portal Studio (clássico) e o Microsoft Azure Machine Learning Portal de serviços Web ajudam você a implantar e gerenciar um serviço Web sem escrever código.

Os links a seguir fornecem informações gerais sobre como implantar um novo serviço Web:

* Para obter uma visão geral sobre como implantar um novo serviço Web baseado em Azure Resource Manager, consulte [implantar um novo serviço Web](deploy-a-machine-learning-web-service.md).
* Para obter instruções sobre como implantar um serviço Web, consulte [implantar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Para obter uma explicação completa sobre como criar e implantar um serviço Web, comece com o [tutorial 1: prever o risco de crédito](tutorial-part1-credit-risk.md).
* Para obter exemplos específicos que implantam um serviço Web, consulte:

  * [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implantar um serviço Web em várias regiões](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com APIs de provedor de recursos de serviços Web (APIs de Azure Resource Manager)

A versão clássica do provedor de recursos Azure Machine Learning Studio para serviços Web permite a implantação e o gerenciamento de serviços Web usando chamadas à API REST. Para obter mais informações, consulte a referência do [serviço Web do Machine Learning (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

A versão clássica do provedor de recursos Azure Machine Learning Studio para serviços Web permite a implantação e o gerenciamento de serviços Web usando cmdlets do PowerShell.

Para usar os cmdlets, primeiro você deve entrar em sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Se você não estiver familiarizado com como chamar comandos do PowerShell baseados no Resource Manager, consulte [usando Azure PowerShell com Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Para exportar seu experimento de previsão, use [este código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o arquivo. exe do código, você pode digitar:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

A execução do aplicativo cria um modelo JSON de serviço Web. Para usar o modelo para implantar um serviço Web, você deve adicionar as seguintes informações:

* Nome e chave da conta de armazenamento

    Você pode obter o nome da conta de armazenamento e a chave do [portal do Azure](https://portal.azure.com/).
* ID do plano de compromisso

    Você pode obter a ID do plano do [Azure Machine Learning Portal de serviços Web](https://services.azureml.net) entrando e clicando em um nome de plano.

Adicione-os ao modelo JSON como filhos do nó *Propriedades* no mesmo nível que o nó *MachineLearningWorkspace* .

Segue-se um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os seguintes artigos e código de exemplo para obter detalhes adicionais:

* Referência de [cmdlets do Azure Machine Learning Studio (clássico)](https://docs.microsoft.com/powershell/module/az.machinelearning) no MSDN
* Exemplo de [explicação](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no github

## <a name="consume-the-web-services"></a>Consumir os serviços Web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Na interface do usuário de serviços Web do Azure Machine Learning (teste)

Você pode testar seu serviço Web do Azure Machine Learning Portal de serviços Web. Isso inclui o teste das interfaces RRS (serviço de solicitação-resposta) e BES (serviço de execução em lote).

* [Implementar um serviço Web novo](deploy-a-machine-learning-web-service.md)
* [Implantar um serviço Web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Do Excel

Você pode baixar um modelo do Excel que consome o serviço Web:

* [Consumindo um serviço Web Azure Machine Learning do Excel](consuming-from-excel.md)
* [Suplemento do Excel para serviços Web do Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST

Azure Machine Learning serviços Web são APIs RESTful. Você pode consumir essas APIs de várias plataformas, como .NET, Python, R, Java, etc. A página **consumir** do serviço Web no portal de [Serviços Web Microsoft Azure Machine Learning](https://services.azureml.net) tem um código de exemplo que pode ajudá-lo a começar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).
