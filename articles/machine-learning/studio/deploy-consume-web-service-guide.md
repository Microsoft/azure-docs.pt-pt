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
ms.openlocfilehash: 1c97fd4f4d5646b6654f5261abd99372c521c389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228243"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Serviços Web Azure Machine Learning Studio (clássico): implantação e consumo

Você pode usar Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos do Machine Learning como serviços Web. Esses serviços da web, em seguida, podem ser utilizados para chamar os modelos de machine learning a partir de aplicações através da Internet para fazer previsões em tempo real ou no modo de lote. Porque os serviços web RESTful, pode chamá-los a partir de várias linguagens de programação e plataformas, como o .NET e Java e de aplicativos, como o Excel.

As secções seguintes fornecem ligações para instruções passo a passo, código e documentação para ajudar a começar.

## <a name="deploy-a-web-service"></a>Implementar serviços Web

### <a name="with-azure-machine-learning-studio-classic"></a>Com Azure Machine Learning Studio (clássico)

O portal Studio (clássico) e o Microsoft Azure Machine Learning Portal de serviços Web ajudam você a implantar e gerenciar um serviço Web sem escrever código.

As ligações seguintes fornecem informações gerais sobre como implementar um novo serviço web:

* Para obter uma visão geral sobre como implantar um novo serviço Web baseado em Azure Resource Manager, consulte [implantar um novo serviço Web](deploy-a-machine-learning-web-service.md).
* Para obter instruções sobre como implantar um serviço Web, consulte [implantar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Para obter uma explicação completa sobre como criar e implantar um serviço Web, comece com o [tutorial 1: prever o risco de crédito](tutorial-part1-credit-risk.md).
* Para obter exemplos específicos que implementa um serviço web, consulte:

  * [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implantar um serviço Web em várias regiões](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o fornecedor de recursos de serviços de web APIs (APIs do Azure Resource Manager)

A versão clássica do provedor de recursos Azure Machine Learning Studio para serviços Web permite a implantação e o gerenciamento de serviços Web usando chamadas à API REST. Para obter mais informações, consulte a referência do [serviço Web do Machine Learning (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

A versão clássica do provedor de recursos Azure Machine Learning Studio para serviços Web permite a implantação e o gerenciamento de serviços Web usando cmdlets do PowerShell.

Para usar os cmdlets, primeiro você deve entrar em sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Se você não estiver familiarizado com como chamar comandos do PowerShell baseados no Resource Manager, consulte [usando Azure PowerShell com Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Para exportar seu experimento de previsão, use [este código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o ficheiro de .exe do código, pode digitar:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar a aplicação cria um modelo de JSON do serviço web. Para utilizar o modelo para implementar um serviço da web, tem de adicionar as seguintes informações:

* Nome da conta de armazenamento e a chave

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

Consulte os seguintes artigos e o código de exemplo para obter detalhes adicionais:

* Referência de [cmdlets do Azure Machine Learning Studio (clássico)](https://docs.microsoft.com/powershell/module/az.machinelearning) no MSDN
* Exemplo de [explicação](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no github

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Os Azure Machine Learning serviços da Web da interface do Usuário (teste)

Pode testar o seu serviço web a partir do portal do Azure Machine Learning Web Services. Isto inclui o teste do serviço de solicitação-resposta (RRS) e interfaces de serviço de execução de lotes (BES).

* [Implementar um serviço Web novo](deploy-a-machine-learning-web-service.md)
* [Implantar um serviço Web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>A partir do Excel

Pode transferir um modelo do Excel que consome o serviço web:

* [Consumindo um serviço Web Azure Machine Learning do Excel](consuming-from-excel.md)
* [Suplemento do Excel para serviços Web do Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST

Serviços Web do Azure Machine Learning são RESTful APIs. Você pode consumir essas APIs de várias plataformas, como .NET, Python, R, Java, etc. A página **consumir** do serviço Web no portal de [Serviços Web Microsoft Azure Machine Learning](https://services.azureml.net) tem um código de exemplo que pode ajudá-lo a começar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).
