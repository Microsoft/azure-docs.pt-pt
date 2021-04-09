---
title: 'Estúdio ML (clássico): Implantação e consumo - Azure'
description: Você pode usar O Azure Machine Learning Studio (clássico) para implementar fluxos de trabalho de machine learning e modelos como serviços web. Estes serviços web podem então ser usados para chamar os modelos de machine learning de aplicações através da internet para fazer previsões em tempo real ou em modo de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 57c5462cd6eccab1d9c42e674e10ea34db3612b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520362"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (clássico) Web Services: Implantação e consumo

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Você pode usar O Azure Machine Learning Studio (clássico) para implementar fluxos de trabalho de machine learning e modelos como serviços web. Estes serviços web podem então ser usados para chamar os modelos de machine learning de aplicações através da Internet para fazer previsões em tempo real ou em modo de lote. Como os serviços web são RESTful, pode chamá-los de várias linguagens e plataformas de programação, tais como .NET e Java, e de aplicações, como o Excel.

As próximas secções fornecem links para passagem, código e documentação para ajudá-lo a começar.

## <a name="deploy-a-web-service"></a>Implementar serviços Web

### <a name="with-azure-machine-learning-studio-classic"></a>Com Azure Machine Learning Studio (clássico)

O portal Studio (clássico) e o portal Microsoft Azure Machine Learning Web Services ajudam-no a implementar e gerir um serviço web sem código de escrita.

Os seguintes links fornecem informações gerais sobre como implementar um novo serviço web:

* Para uma visão geral sobre como implementar um novo serviço web baseado no Azure Resource Manager, consulte [implementar um novo serviço web](deploy-a-machine-learning-web-service.md).
* Para uma passagem sobre como implementar um serviço web, consulte [implementar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Para uma análise completa sobre como criar e implementar um serviço web, comece com [Tutorial 1: Predict credit risk](tutorial-part1-credit-risk.md).
* Para exemplos específicos que implementem um serviço web, consulte:

  * [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implementar um serviço web em várias regiões](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com apis fornecedores de serviços web (APIs gestor de recursos Azure)

O fornecedor de recursos Azure Machine Learning Studio (clássico) para serviços web permite a implementação e gestão de serviços web utilizando chamadas REST API. Para obter mais informações, consulte a referência [do Serviço Web de Aprendizagem Automática (REST).](/rest/api/machinelearning/index)

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Com cmdlets PowerShell

O fornecedor de recursos Azure Machine Learning Studio (clássico) para serviços web permite a implementação e gestão de serviços web utilizando cmdlets PowerShell.

Para utilizar os cmdlets, tem primeiro de iniciar súm na sua conta Azure a partir do ambiente PowerShell utilizando o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Se não estiver familiarizado com a forma de ligar para comandos PowerShell baseados no Gestor de Recursos, consulte [a Utilização do PowerShell com o Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Para exportar a sua experiência preditiva, utilize [este código de amostra.](https://github.com/ritwik20/AzureML-WebServices) Depois de criar o ficheiro .exe a partir do código, pode escrever:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Executar a aplicação cria um modelo JSON de serviço web. Para utilizar o modelo para implementar um serviço web, deve adicionar as seguintes informações:

* Nome e chave da conta de armazenamento

    Pode obter o nome da conta de armazenamento e a chave do [portal Azure.](https://portal.azure.com/)
* ID do plano de compromisso

    Você pode obter o ID do plano a partir do portal [Azure Machine Learning Web Services,](https://services.azureml.net) insinando-se e clicando num nome de plano.

Adicione-os ao modelo JSON como crianças do nó *Propriedades* ao mesmo nível do nó *MachineLearningWorkspace.*

Eis um exemplo:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Consulte os seguintes artigos e código de amostra para mais detalhes:

* [Azure Machine Learning Studio (clássico) Referência de Cmdlets](/powershell/module/az.machinelearning) na MSDN

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>A partir do Azure Machine Learning Web Services UI (Teste)

Pode testar o seu serviço web a partir do portal Azure Machine Learning Web Services. Isto inclui testar as interfaces de serviço Request-Response (RRS) e serviço de execução de lotes (BES).

* [Implementar um serviço Web novo](deploy-a-machine-learning-web-service.md)
* [Implementar um serviço Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>A partir do Excel

Você pode baixar um modelo Excel que consome o serviço web:

* [Consumir um serviço web Azure Machine Learning do Excel](consuming-from-excel.md)
* [Excel add-in para Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente com sede em REST

Os Serviços Web de Aprendizagem automática Azure são APIs RESTful. Você pode consumir estes APIs de várias plataformas, tais como .NET, Python, R, Java, etc. A página **Consumir** para o seu serviço web no [portal Microsoft Azure Machine Learning Web Services](https://services.azureml.net) tem um código de amostra que pode ajudá-lo a começar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).