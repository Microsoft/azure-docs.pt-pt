---
title: Implantação e consumo
titleSuffix: ML Studio (classic) - Azure
description: Você pode usar Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos do Machine Learning como serviços Web. Esses serviços da web, em seguida, podem ser utilizados para chamar os modelos de machine learning a partir de aplicações através da internet para fazer previsões em tempo real ou no modo de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 7216d2f97a52798d2609073761eb8f4a2ce9024d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976118"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Serviços Web Azure Machine Learning Studio (clássico): implantação e consumo

Você pode usar Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos do Machine Learning como serviços Web. Esses serviços da web, em seguida, podem ser utilizados para chamar os modelos de machine learning a partir de aplicações através da Internet para fazer previsões em tempo real ou no modo de lote. Porque os serviços web RESTful, pode chamá-los a partir de várias linguagens de programação e plataformas, como o .NET e Java e de aplicativos, como o Excel.

As secções seguintes fornecem ligações para instruções passo a passo, código e documentação para ajudar a começar.

## <a name="deploy-a-web-service"></a>Implementar um serviço Web

### <a name="with-azure-machine-learning-studio-classic"></a>Com Azure Machine Learning Studio (clássico)

O portal Studio (clássico) e o Microsoft Azure Machine Learning Portal de serviços Web ajudam você a implantar e gerenciar um serviço Web sem escrever código.

As ligações seguintes fornecem informações gerais sobre como implementar um novo serviço web:

* Para uma descrição geral sobre como implementar um novo serviço web baseado no Azure Resource Manager, consulte [implementar um novo serviço web](deploy-a-machine-learning-web-service.md).
* Para obter instruções sobre como implementar um serviço da web, consulte [implementar um serviço web do Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Para obter uma explicação completa sobre como criar e implantar um serviço Web, comece com o [tutorial 1: prever o risco de crédito](tutorial-part1-credit-risk.md).
* Para obter exemplos específicos que implementa um serviço web, consulte:

  * [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implementar um serviço web em várias regiões](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o fornecedor de recursos de serviços de web APIs (APIs do Azure Resource Manager)

O provedor de recursos Azure Machine Learning Studio (clássico) para serviços Web permite a implantação e o gerenciamento de serviços Web usando chamadas à API REST. Para obter mais informações, consulte a referência do [serviço Web do Machine Learning (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

O provedor de recursos Azure Machine Learning Studio (clássico) para serviços Web permite a implantação e o gerenciamento de serviços Web usando cmdlets do PowerShell.

Para usar os cmdlets, primeiro você deve entrar em sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Se não estiver familiarizado com como chamar comandos do PowerShell que são baseados no Resource Manager, veja [utilizar o Azure PowerShell com o Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Para exportar a sua experimentação preditiva, utilize [este código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o ficheiro de .exe do código, pode digitar:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar a aplicação cria um modelo de JSON do serviço web. Para utilizar o modelo para implementar um serviço da web, tem de adicionar as seguintes informações:

* Nome da conta de armazenamento e a chave

    Pode obter o nome de conta de armazenamento e da chave do [portal do Azure](https://portal.azure.com/).
* ID do plano de compromisso

    Pode obter o ID do plano do [serviços da Web do Azure Machine Learning](https://services.azureml.net) portal a iniciar sessão e clicando num nome de plano.

Adicioná-los para o modelo JSON como filhos do *propriedades* nó de mesmo nível, como o *MachineLearningWorkspace* nó.

Segue-se um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os seguintes artigos e o código de exemplo para obter detalhes adicionais:

* Referência de [cmdlets do Azure Machine Learning Studio (clássico)](https://docs.microsoft.com/powershell/module/az.machinelearning) no MSDN
* Exemplo [passo a passo](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no GitHub

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Os Azure Machine Learning serviços da Web da interface do Usuário (teste)

Pode testar o seu serviço web a partir do portal do Azure Machine Learning Web Services. Isto inclui o teste do serviço de solicitação-resposta (RRS) e interfaces de serviço de execução de lotes (BES).

* [Implementar um serviço Web novo](deploy-a-machine-learning-web-service.md)
* [Implementar um serviço web do Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>A partir do Excel

Pode transferir um modelo do Excel que consome o serviço web:

* [Consumir um serviço web do Azure Machine Learning a partir do Excel](consuming-from-excel.md)
* [Suplemento do Excel para serviços da Web do Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST

Serviços Web do Azure Machine Learning são RESTful APIs. Você pode consumir essas APIs de várias plataformas, como .NET, Python, R, Java, etc. A página **consumir** do serviço Web no portal de [Serviços Web Microsoft Azure Machine Learning](https://services.azureml.net) tem um código de exemplo que pode ajudá-lo a começar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).
