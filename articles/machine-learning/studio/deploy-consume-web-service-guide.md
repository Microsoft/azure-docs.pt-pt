---
title: Implantação e consumo
titleSuffix: ML Studio (classic) - Azure
description: Pode utilizar o Azure Machine Learning Studio (clássico) para implementar fluxos de trabalho e modelos de aprendizagem automática como serviços web. Estes serviços web podem então ser usados para chamar os modelos de machine learning a partir de aplicações através da internet para fazer previsões em tempo real ou em modo de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 9104470a2346052ed17c670ccc39215ff77ef51f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118418"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (clássico) Web Services: Implantação e consumo

Pode utilizar o Azure Machine Learning Studio (clássico) para implementar fluxos de trabalho e modelos de aprendizagem automática como serviços web. Estes serviços web podem então ser usados para chamar os modelos de machine learning a partir de aplicações através da Internet para fazer previsões em tempo real ou em modo de lote. Como os serviços web são RESTful, você pode chamá-los de vários idiomas e plataformas de programação, tais como .NET e Java, e de aplicações, como o Excel.

As secções seguintes fornecem links para walkthroughs, código e documentação para ajudá-lo a começar.

## <a name="deploy-a-web-service"></a>Implementar serviços Web

### <a name="with-azure-machine-learning-studio-classic"></a>Com o Azure Machine Learning Studio (clássico)

O portal Studio (clássico) e o portal Microsoft Azure Machine Learning Web Services ajudam-no a implementar e gerir um serviço web sem escrever código.

Os seguintes links fornecem informações gerais sobre como implementar um novo serviço web:

* Para uma visão geral sobre como implementar um novo serviço web baseado no Azure Resource Manager, consulte [a Implementação de um novo serviço web.](deploy-a-machine-learning-web-service.md)
* Para uma caminhada sobre como implementar um serviço web, consulte [Implementar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Para uma caminhada completa sobre como criar e implementar um serviço web, comece com [tutorial 1: Prever](tutorial-part1-credit-risk.md)o risco de crédito .
* Para exemplos específicos que implementam um serviço web, consulte:

  * [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implementar um serviço web para várias regiões](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o fornecedor de recursos de serviços web APIs (Azure Resource Manager APIs)

O Azure Machine Learning Studio (clássico) fornecedor de recursos para serviços web permite a implementação e gestão de serviços web utilizando chamadas REST API. Para mais informações, consulte a referência do [Serviço Web de Aprendizagem Automática (REST).](/rest/api/machinelearning/index)

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Com cmdlets PowerShell

O fornecedor de recursos Azure Machine Learning Studio (clássico) para serviços web permite a implementação e gestão de serviços web utilizando cmdlets PowerShell.

Para utilizar os cmdlets, tem primeiro de iniciar sessão na sua conta Azure a partir do ambiente PowerShell utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Se não está familiarizado com a forma de ligar para comandos PowerShell baseados no Gestor de Recursos, consulte a Utilização de [PowerShell Azure com o Gestor](../../azure-resource-manager/management/manage-resources-powershell.md)de Recursos Azure .

Para exportar a sua experiência preditiva, use [este código de amostra](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o ficheiro .exe a partir do código, pode escrever:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar a aplicação cria um modelo JSON de serviço web. Para utilizar o modelo para implementar um serviço web, deve adicionar as seguintes informações:

* Nome e chave da conta de armazenamento

    Pode obter o nome da conta de armazenamento e a chave do [portal Azure.](https://portal.azure.com/)
* ID do plano de compromisso

    Pode obter o ID do plano do portal [Azure Machine Learning Web Services,](https://services.azureml.net) inserindo e clicando num nome de plano.

Adicione-os ao modelo JSON como crianças do nó de *Propriedades* ao mesmo nível do nó *machineLearningWorkspace.*

Eis um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os seguintes artigos e código de amostra para obter mais detalhes:

* [Azure Machine Learning Studio (clássico) Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) referência na MSDN

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>A partir do Azure Machine Learning Web Services UI (Testing)

Pode testar o seu serviço web a partir do portal Azure Machine Learning Web Services. Isto inclui testar as interfaces do serviço de resposta a pedidos (RRS) e do serviço de execução de lotes (BES).

* [Implementar um serviço Web novo](deploy-a-machine-learning-web-service.md)
* [Implementar um serviço Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>A partir do Excel

Você pode baixar um modelo Excel que consome o serviço web:

* [Consumir um serviço web Azure Machine Learning do Excel](consuming-from-excel.md)
* [Complemento Excel para Serviços Web de Aprendizagem automática Azure](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST

Os Serviços Web de Aprendizagem Automática Azure são APIs RESTful. Pode consumir estas APIs de várias plataformas, tais como .NET, Python, R, Java, etc. A página **Consumir** para o seu serviço web no [portal microsoft Azure Machine Learning Web Services](https://services.azureml.net) tem código de amostra que pode ajudá-lo a começar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).
