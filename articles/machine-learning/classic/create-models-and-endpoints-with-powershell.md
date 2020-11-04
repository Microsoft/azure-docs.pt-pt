---
title: 'Estúdio ML (clássico): Criar vários pontos finais & modelo - Azure'
description: Use o PowerShell para criar vários modelos de Machine Learning e pontos finais de serviço web com o mesmo algoritmo, mas diferentes conjuntos de dados de treino.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: ef9ea055f437b53313dc9ee11b0b91f095664f5e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322850"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Crie vários pontos finais de serviço web a partir de uma experiência com ML Studio (clássico) e PowerShell

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Aqui está um problema comum de aprendizagem automática: Você quer criar muitos modelos que têm o mesmo fluxo de trabalho de treino e usar o mesmo algoritmo. Mas quer que tenham diferentes conjuntos de dados de treino como entrada. Este artigo mostra-lhe como fazê-lo em escala no Azure Machine Learning Studio (clássico) usando apenas uma experiência.

Por exemplo, digamos que é dono de um negócio global de franquia de aluguer de bicicletas. Você quer construir um modelo de regressão para prever a procura de aluguel com base em dados históricos. Você tem 1.000 locais de aluguer em todo o mundo e você recolheu um conjunto de dados para cada local. Incluem características importantes como data, hora, tempo e tráfego específicos de cada local.

Você pode treinar o seu modelo uma vez usando uma versão fundida de todos os conjuntos de dados em todos os locais. Mas cada uma das suas localizações tem um ambiente único. Assim, uma melhor abordagem seria treinar o seu modelo de regressão separadamente usando o conjunto de dados para cada local. Desta forma, cada modelo treinado poderia ter em conta os diferentes tamanhos das lojas, volume, geografia, população, ambiente de tráfego amigo das bicicletas, e muito mais.

Essa pode ser a melhor abordagem, mas não quer criar 1.000 experiências de formação no Azure Machine Learning Studio (clássico) com cada uma representando uma localização única. Além de ser uma tarefa esmagadora, também parece ineficiente, uma vez que cada experiência teria todos os mesmos componentes, exceto o conjunto de dados de treino.

Felizmente, você pode fazê-lo usando o [Azure Machine Learning Studio (clássico) retraining API](./retrain-machine-learning-model.md) e automatizando a tarefa com [Azure Machine Learning Studio (clássico) PowerShell](powershell-module.md).

> [!NOTE]
> Para que a sua amostra corra mais rapidamente, reduza o número de locais de 1.000 para 10. Mas os mesmos princípios e procedimentos aplicam-se a 1.000 locais. No entanto, se quiser treinar a partir de 1.000 conjuntos de dados, talvez queira executar os seguintes scripts PowerShell em paralelo. Como fazê-lo está fora do âmbito deste artigo, mas você pode encontrar exemplos de powerShell multi-threading na Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurar a experiência de treino
Use o [exemplo de experiência de treino](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) que está na Galeria de Inteligência [Cortana.](https://gallery.azure.ai) Abra esta experiência no seu espaço de trabalho [Azure Machine Learning Studio (clássico).](https://studio.azureml.net)

> [!NOTE]
> Para acompanhar este exemplo, pode querer utilizar um espaço de trabalho padrão em vez de um espaço de trabalho gratuito. Você cria um ponto final para cada cliente - para um total de 10 pontos finais - e isso requer um espaço de trabalho padrão uma vez que um espaço de trabalho gratuito é limitado a 3 pontos finais.
> 
> 

A experiência utiliza um módulo **de Dados de Importação** para importar o conjunto de dados de formação *customer001.csv* de uma conta de armazenamento Azure. Vamos supor que recolheu conjuntos de dados de treino de todos os locais de aluguer de bicicletas e os armazenou no mesmo local de armazenamento de bolhas com nomes de ficheiros que vão de *rentalloc001.csv* a *rentalloc10.csv.*

![Módulo de leitor importa dados de uma bolha de Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Note que um módulo **de saída de serviço web** foi adicionado ao módulo Modelo de **Comboio.**
Quando esta experiência é implementada como um serviço web, o ponto final associado a essa saída devolve o modelo treinado no formato de um ficheiro .ilearner.

Note também que configura um parâmetro de serviço web que define o URL que o módulo **de Dados de Importação** utiliza. Isto permite-lhe utilizar o parâmetro para especificar conjuntos de dados de treino individuais para treinar o modelo para cada local.
Há outras formas de ter feito isto. Pode utilizar uma consulta SQL com um parâmetro de serviço web para obter dados de uma base de dados na Base de Dados Azure SQL. Ou pode usar um módulo  **de entrada de serviço web** para passar num conjunto de dados para o serviço web.

![Um módulo de modelo treinado produz saídas para um módulo de saída de serviço Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esta experiência de treino usando o valor padrão *rental001.csv* como o conjunto de dados de treino. Se visualizar a saída do módulo **Avaliar** (clique na saída e selecione **Visualize),** pode ver que obtém um desempenho decente de *AUC* = 0,91. Neste momento, estás pronto para implantar um serviço web a partir desta experiência de treino.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implementar os serviços web de formação e pontuação
Para implementar o serviço web de formação, clique no botão **De Serviço Web Configurar** abaixo da tela de experiência e selecione **Implementar o Serviço Web**. Ligue para este serviço web "Bike Rental Training".

Agora tens de implementar o serviço web de pontuação.
Para isso, clique em **Configurar o Serviço Web** abaixo da tela e selecione **o Serviço Web Preditivo**. Isto cria uma experiência de pontuação.
É preciso fazer alguns pequenos ajustes para que funcione como um serviço web. Remova a coluna de etiqueta "cnt" dos dados de entrada e limite a saída apenas ao id de instância e ao valor previsto correspondente.

Para se salvar esse trabalho, pode abrir a [experiência preditiva](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) na Galeria que já foi preparada.

Para implementar o serviço web, executar a experiência preditiva e, em seguida, clicar no botão Implementar o **Serviço Web** por baixo da tela. Nomeie o serviço web de pontuação "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Criar 10 pontos finais de serviço web idênticos com PowerShell
Este serviço web vem com um ponto final padrão. Mas não está tão interessado no ponto final padrão, uma vez que não pode ser atualizado. O que precisa fazer é criar 10 pontos finais adicionais, um para cada local. Podes fazer isto com o PowerShell.

Primeiro, cria-se o ambiente PowerShell:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Em seguida, executar o seguinte comando PowerShell:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

Agora criaste 10 pontos finais e todos eles contêm o mesmo modelo treinado treinado em *customer001.csv*. Pode vê-los no portal Azure.

![Veja a lista de modelos treinados no portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualize os pontos finais para utilizar conjuntos de dados de formação separados utilizando o PowerShell
O próximo passo é atualizar os pontos finais com modelos treinados exclusivamente nos dados individuais de cada cliente. Mas primeiro você precisa produzir estes modelos a partir do serviço web **Bike Rental Training.** Vamos voltar ao serviço web **de Bike Rental Training.** É necessário ligar para o seu ponto final do BES 10 vezes com 10 conjuntos de dados de treino diferentes para produzir 10 modelos diferentes. Utilize o **cmdlet InovkeAmlWebServiceBESEndpoint** powerShell para o fazer.

Também terá de fornecer credenciais para a sua conta de armazenamento de bolhas em `$configContent` . Nomeadamente, nos `AccountName` `AccountKey` campos, `RelativeLocation` e. `AccountName`Pode ser um dos nomes da sua conta, como visto no portal **Azure** *(separador de armazenamento).* Uma vez clicada numa conta de armazenamento, `AccountKey` pode encontrar-se premindo o botão **'Gerir chaves'** na parte inferior e copiar a *tecla de acesso principal*. Este `RelativeLocation` é o caminho relativo ao seu armazenamento onde um novo modelo será armazenado. Por exemplo, o caminho `hai/retrain/bike_rental/` do seguinte script aponta para um recipiente chamado , e são `hai` `/retrain/bike_rental/` sub-dobradeiras. Atualmente, não é possível criar sub-13 através do portal UI, mas existem [vários Exploradores de Armazenamento Azure](../../storage/common/storage-explorers.md) que lhe permitem fazê-lo. Recomenda-se que crie um novo recipiente no seu armazém para armazenar os novos modelos treinados (.iLearner files) da seguinte forma: a partir da sua página de armazenamento, clique no botão **Adicionar** na parte inferior e nomeie-o `retrain` . Em resumo, as alterações necessárias ao seguinte guião dizem respeito a `AccountName` `AccountKey` , e `RelativeLocation` (: `"retrain/model' + $seq + '.ilearner"` ).

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> O ponto final do BES é o único modo suportado para esta operação. O RRS não pode ser utilizado para a produção de modelos treinados.
> 
> 

Como pode ver acima, em vez de construir 10 diferentes ficheiros json de configuração de trabalho do BES, cria dinamicamente a cadeia config. Em seguida, alimente-o para o *trabalhoConfigString* parâmetro do **cmdlet InvokeAmlWebServceBESEndpoint.** Não há necessidade de manter uma cópia no disco.

Se tudo correr bem, depois de um tempo deverá ver 10 ficheiros .iLearner, desde *model001.ilearner* a *model010.ilearner,* na sua conta de armazenamento Azure. Agora está pronto para atualizar os 10 pontos finais do serviço web com estes modelos utilizando o **cmdlet De Patch-AmlWebServiceEndpoint** PowerShell. Lembre-se mais uma vez que só pode corrigir os pontos finais não predefinidos que criou programaticamente anteriormente.

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

Isto deve correr rapidamente. Quando a execução terminar, terá criado com sucesso 10 pontos finais de serviço web preditivos. Cada um deles conterá um modelo treinado exclusivamente no conjunto de dados específico para um local de aluguer, tudo a partir de uma única experiência de treino. Para verificar isto, pode tentar ligar para estes pontos finais utilizando o **cmdlet InvokeAmlWebServiceRRSEndpoint,** fornecendo-lhes os mesmos dados de entrada. Deve esperar ver diferentes resultados de previsão uma vez que os modelos são treinados com diferentes conjuntos de treino.

## <a name="full-powershell-script"></a>Script Full PowerShell
Aqui está a listagem do código fonte completo:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```