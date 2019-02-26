---
title: Implementação e consumo
titleSuffix: Azure Machine Learning Studio
description: Pode utilizar o Azure Machine Learning Studio para implementar fluxos de trabalho do machine learning e modelos como serviços web. Esses serviços da web, em seguida, podem ser utilizados para chamar os modelos de machine learning a partir de aplicações através da internet para fazer previsões em tempo real ou no modo de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: b4658d6321b9c37a2f769ab3961268af8af0773d
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823931"
---
# <a name="azure-machine-learning-studio-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio serviços da Web: Implementação e consumo

Pode utilizar o Azure Machine Learning Studio para implementar fluxos de trabalho do machine learning e modelos como serviços web. Esses serviços da web, em seguida, podem ser utilizados para chamar os modelos de machine learning a partir de aplicações através da Internet para fazer previsões em tempo real ou no modo de lote. Porque os serviços web RESTful, pode chamá-los a partir de várias linguagens de programação e plataformas, como o .NET e Java e de aplicativos, como o Excel.

As secções seguintes fornecem ligações para instruções passo a passo, código e documentação para ajudar a começar.

## <a name="deploy-a-web-service"></a>Implementar serviços Web

### <a name="with-azure-machine-learning-studio"></a>Com o Azure Machine Learning Studio

Portal do Studio e o portal de serviços Web do Microsoft Azure Machine Learning ajudam a implementar e gerir um serviço web sem escrever código.

As ligações seguintes fornecem informações gerais sobre como implementar um novo serviço web:

* Para uma descrição geral sobre como implementar um novo serviço web baseado no Azure Resource Manager, consulte [implementar um novo serviço web](publish-a-machine-learning-web-service.md).
* Para obter instruções sobre como implementar um serviço da web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Para obter instruções completas sobre como criar e implementar um serviço web, inicie com [Tutorial 1: Prever o risco de crédito](tutorial-part1-credit-risk.md).
* Para obter exemplos específicos que implementa um serviço web, consulte:

  * [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implementar um serviço web em várias regiões](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o fornecedor de recursos de serviços de web APIs (APIs do Azure Resource Manager)

O fornecedor de recursos do Azure Machine Learning Studio para serviços da web permite a implementação e gestão de serviços da web através de chamadas à REST API. Para obter mais informações, consulte a [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) referência.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

O fornecedor de recursos do Azure Machine Learning Studio para serviços da web permite a implementação e gestão de serviços da web utilizando cmdlets do PowerShell.

Para utilizar os cmdlets, tem primeiro de iniciar sessão sua conta do Azure a partir de ambiente do PowerShell, utilizando o [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet. Se não estiver familiarizado com como chamar comandos do PowerShell que são baseados no Resource Manager, veja [utilizar o Azure PowerShell com o Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

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

* [Cmdlets Studio do Azure Machine Learning](https://docs.microsoft.com/powershell/module/azurerm.machinelearning) referência no MSDN
* Exemplo [passo a passo](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no GitHub

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Os Azure Machine Learning serviços da Web da interface do Usuário (teste)

Pode testar o seu serviço web a partir do portal do Azure Machine Learning Web Services. Isto inclui o teste do serviço de solicitação-resposta (RRS) e interfaces de serviço de execução de lotes (BES).

* [Implementar um serviço Web novo](publish-a-machine-learning-web-service.md)
* [Implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>A partir do Excel

Pode transferir um modelo do Excel que consome o serviço web:

* [Consumir um serviço web do Azure Machine Learning a partir do Excel](consuming-from-excel.md)
* [Suplemento do Excel para serviços da Web do Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST

Serviços Web do Azure Machine Learning são RESTful APIs. Pode consumir essas APIs de várias plataformas, como .NET, Python, R, Java, etc. O **Consume** página do web Service a [portal do Microsoft Azure Machine Learning Web Services](https://services.azureml.net) tem o código de exemplo que pode ajudá-lo a começar a utilizar. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).
