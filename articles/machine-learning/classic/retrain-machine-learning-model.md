---
title: 'ML Studio (clássico): retreine um serviço web - Azure'
description: Saiba como atualizar um serviço web para utilizar um modelo de aprendizagem automática recentemente treinado no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 2f115313b17ed159973d2545b947e2ff031508eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362338"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Retreine e implemente um modelo de aprendizagem automática

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../compare-azure-ml-to-studio-classic.md)  


A reconversão é uma forma de garantir que os modelos de machine learning se mantenham precisos e com base nos dados mais relevantes disponíveis. Este artigo mostra como retreinar e implementar um modelo de machine learning como um novo serviço web em Studio (clássico). Se procura retreinar um serviço web clássico, [veja este artigo de como fazer.](retrain-classic-web-service.md)

Este artigo pressupõe que já tem um serviço web preditivo implantado. Se ainda não tem um serviço web preditivo, [aprenda a implementar um serviço web Studio (clássico) aqui.](deploy-a-machine-learning-web-service.md)

Seguirá estes passos para retreinar e implementar um novo serviço web de aprendizagem automática:

1. Implementar um **serviço web de reconversão**
1. Treine um novo modelo utilizando o seu **serviço web de reciclagem**
1. Atualize a sua **experiência preditiva** existente para usar o novo modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Implementar o serviço web de reconversão

Um serviço web de reconversão permite-lhe reconverter o seu modelo com um novo conjunto de parâmetros, como novos dados, e guardá-lo para mais tarde. Quando liga uma **saída de Serviço Web**  a um Modelo de **Comboio,** a experiência de treino produz um novo modelo para utilizar.

Utilize os seguintes passos para implementar um serviço web de reconversão:

1. Conecte um módulo **de entrada de serviço web** à entrada de dados. Normalmente, pretende garantir que os seus dados de entrada são tratados da mesma forma que os seus dados de treino originais.
1. Ligue um módulo **de saída de serviço web** à saída do seu Modelo de **Comboio.**
1. Se tiver um módulo **De Modelo de Avaliação,** pode ligar um módulo de saída de **Serviço Web** para obter os resultados da avaliação
1. Faz a tua experiência.

    Após executar a sua experiência, o fluxo de trabalho resultante deve ser semelhante à seguinte imagem:

    ![Fluxo de trabalho resultante](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Agora, você implementa a experiência de treino como um serviço web de reconversão que produz um modelo treinado e resultados de avaliação de modelos.

1. Na parte inferior da tela de experiência, clique em **Configurar O Serviço Web**
1. Selecione **Implementar o Serviço Web [Novo]**. O portal Azure Machine Learning Web Services abre para a página **de Serviço Web Implementar.**
1. Digite um nome para o seu serviço web e escolha um plano de pagamento.
1. Selecione **Implementar**.

## <a name="retrain-the-model"></a>Retreinar o modelo

Para este exemplo, estamos a usar o C# para criar a aplicação de reciclagem. Também pode utilizar o código de amostra Python ou R para realizar esta tarefa.

Utilize os seguintes passos para chamar as APIs de reconversão:

1. Criar uma aplicação de consola C# em Visual Studio: **Novo**  >  **Project**  >  **Visual C#** Windows Classic  >  **Desktop**Console  >  **App (.NET Framework)**.
1. Inscreva-se no portal de Serviços Web machine learning.
1. Clique no serviço web com o qual está a trabalhar.
1. Clique **em Consumir**.
1. Na parte inferior da página **Consumir,** na secção **Código amostra,** clique em **Lote**.
1. Copie o código da amostra C# para a execução do lote e cole-o no ficheiro Program.cs. Certifique-se de que o espaço de nome permanece intacto.

Adicione o pacote NuGet Microsoft.AspNet.WebApi.Client, conforme especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, poderá ser necessário instalar a biblioteca do [cliente para serviços de Armazenamento Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A imagem que se segue mostra a página **Consumir** no portal Azure Machine Learning Web Services.

![Página consumir](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração apikey

Localize a declaração **apikey:**

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

Na secção **de informações** de consumo básico da página **Consumir,** localize a chave primária e copie-a para a declaração **de apikey.**

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de Armazenamento Azure

O código de amostra do BES envia um ficheiro de uma unidade local (por exemplo, "C:\temp\CensusInput.csv") para o Azure Storage, processa-o e escreve os resultados de volta para o Azure Storage.

1. Iniciar sessão no portal do Azure
1. Na coluna de navegação à esquerda, clique em **Mais serviços,** procure **contas de Armazenamento**e selecione-a.
1. A partir da lista de contas de armazenamento, selecione uma para armazenar o modelo retreinado.
1. Na coluna de navegação à esquerda, clique nas **teclas de acesso**.
1. Copie e guarde a **chave de acesso primário.**
1. Na coluna de navegação à esquerda, clique em **Blobs**.
1. Selecione um recipiente existente, ou crie um novo e guarde o nome.

Localize as declarações *de StorageAccountName*, *StorageAccountKey*e *StorageContainerName* e atualize os valores que guardou do portal.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

Também deve certificar-se de que o ficheiro de entrada está disponível no local que especifica no código.

### <a name="specify-the-output-location"></a>Especificar a localização da saída

Quando especificar a localização da saída na Carga De pagamento do pedido, a extensão do ficheiro especificada na *RelativaLocalização* deve ser especificada como `ilearner` .

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Aqui está um exemplo de saída de reconversão:

![Saída de reciclagem](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Avaliar os resultados da reconversão

Quando executa a aplicação, a saída inclui o URL e o token de assinaturas de acesso partilhado que são necessárias para aceder aos resultados da avaliação.

Pode ver os resultados de desempenho do modelo retreinado combinando o *BaseLocation*, *RelativeLocation*e *SasBlobToken* a partir dos resultados de saída para *a saída2* e colando o URL completo na barra de endereços do navegador.

Examine os resultados para determinar se o modelo recém-treinado tem um desempenho melhor do que o existente.

Salve a *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados da saída.

## <a name="update-the-predictive-experiment"></a>Atualizar a experiência preditiva

### <a name="sign-in-to-azure-resource-manager"></a>Inscreva-se no Azure Resource Manager

Em primeiro lugar, inscreva-se na sua conta Azure a partir do ambiente PowerShell utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="get-the-web-service-definition-object"></a>Obtenha o objeto de definição de serviço web

Em seguida, obtenha o objeto de Definição de Serviço Web chamando o cmdlet [Get-AzMlWebService.](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice)

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Para determinar o nome do grupo de recursos de um serviço web existente, executar o Get-AzMlWebService cmdlet sem quaisquer parâmetros para exibir os serviços web na sua subscrição. Localize o serviço web e, em seguida, olhe para o seu ID de serviço web. O nome do grupo de recursos é o quarto elemento no ID, logo após o elemento *grupos de recursos.* No exemplo seguinte, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Em alternativa, para determinar o nome do grupo de recursos de um serviço web existente, inscreva-se no portal Azure Machine Learning Web Services. Selecione o serviço web. O nome do grupo de recursos é o quinto elemento do URL do serviço web, logo após o elemento *grupos de recursos.* No exemplo seguinte, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de Definição de Serviço Web como JSON

Para modificar a definição do modelo treinado para utilizar o modelo recém-treinado, tem primeiro de utilizar o cmdlet [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) para exportá-lo para um ficheiro em formato JSON.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>Atualize a referência à bolha ilearner

Nos ativos, localizar o [modelo treinado], atualizar o valor *uri* no nó *localizaçãoInfo* com o URI da bolha ilearner. O URI é gerado combinando a *BaseLocation* e a *RelativaLocation* a partir da saída da chamada de reconversão do BES.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importe o JSON num objeto de definição de serviço web

Utilize o cmdlet [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) para converter o ficheiro JSON modificado de volta num objeto de Definição de Serviço Web que pode utilizar para atualizar a experiência de pretensão.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Atualizar o serviço web

Por fim, utilize o [cmdlet Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) para atualizar a experiência preditiva.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerir serviços web ou acompanhar várias experiências, consulte os seguintes artigos:

* [Explore o portal de Serviços Web](manage-new-webservice.md)
* [Gerir iterações das experimentações](manage-experiment-iterations.md)
