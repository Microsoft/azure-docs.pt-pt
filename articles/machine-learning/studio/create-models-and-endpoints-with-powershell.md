---
title: Criar vários pontos finais para um modelo
titleSuffix: ML Studio (classic) - Azure
description: Utilize o PowerShell para criar vários modelos de Machine Learning e web pontos finais de serviço com o mesmo algoritmo, mas treinamento diferentes conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218178"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Use powerShell para criar modelos studio (clássicos) e pontos finais de serviço web a partir de uma experiência

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este é um problema de aprendizado de máquina comum: pretende criar muitos modelos que tenham o mesmo fluxo de trabalho de treinamento e usam o mesmo algoritmo. Mas deseja que eles tenham conjuntos de dados de treinamento diferente como entrada. Este artigo mostra-lhe como fazê-lo em escala no Azure Machine Learning Studio (clássico) usando apenas uma experiência.

Por exemplo, imaginemos que é proprietário de uma empresa de franquia de aluguer de bicicletas global. Qual quer criar um modelo de regressão para prever a procura de aluguel com base no histórico de dados. Tem 1.000 localizações de aluguel em todo o mundo e Reunimos um conjunto de dados para cada localização. Eles incluem recursos importantes, como a data, hora, meteorologia e tráfego, que são específicos para cada localização.

Pode preparar o seu modelo uma vez usando uma versão unida de todos os conjuntos de dados em todas as localizações. No entanto, cada uma das suas localizações possui um ambiente exclusivo. Portanto, uma abordagem melhor seria de preparar o seu modelo de regressão separadamente com o conjunto de dados para cada localização. Dessa forma, cada modelo preparado poderia levar em conta os tamanhos de armazenamento diferentes, volume, geografia, população, ambiente de tráfego de bicicletas amigável e muito mais.

Essa pode ser a melhor abordagem, mas não quer criar 1.000 experiências de formação no Azure Machine Learning Studio (clássico) com cada uma representando uma localização única. Além de ser uma tarefa árdua, também parece ineficiente, pois cada experimentação teria todos os mesmos componentes, exceto para o conjunto de dados de treinamento.

Felizmente, você pode fazê-lo usando o [Azure Machine Learning Studio (clássico) retreinando a API](/azure/machine-learning/studio/retrain-machine-learning-model) e automatizando a tarefa com [o Azure Machine Learning Studio (clássico) PowerShell](powershell-module.md).

> [!NOTE]
> Para tornar o seu exemplo são executadas mais depressa, reduza o número de localizações de 1.000 para 10. Mas os mesmos princípios e procedimentos aplicam-se a 1.000 localizações. No entanto, se quiser dar formação de 1.000 conjuntos de dados poderá querer executar scripts do PowerShell abaixo em paralelo. Como fazer isso está além do escopo deste artigo, mas pode encontrar exemplos do PowerShell multithreading na Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurar a experimentação de preparação
Use a experiência de [treino](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) de exemplo que está na Galeria de [Inteligência cortana.](https://gallery.azure.ai) Abra esta experiência no seu espaço de trabalho [Azure Machine Learning Studio (clássico).](https://studio.azureml.net)

> [!NOTE]
> Para acompanhar este exemplo, pode querer utilizar uma área de trabalho padrão, em vez de uma área de trabalho gratuita. Criar um ponto final de cada cliente - para um total de 10 pontos finais - e que requer uma área de trabalho padrão, uma vez que uma área de trabalho gratuita está limitada a 3 pontos de extremidade.
> 
> 

A experiência utiliza um módulo **de Dados de Importação** para importar o conjunto de dados de formação *cliente001.csv* de uma conta de armazenamento Azure. Vamos supor que você recolheu conjuntos de dados de treino de todos os locais de aluguer de bicicletas e guardou-os no mesmo local de armazenamento blob com nomes de arquivos que vão de *rentalloc001.csv* a *rentalloc10.csv*.

![Módulo de leitor importa dados de uma bolha Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Note que um módulo de saída de **serviço Web** foi adicionado ao módulo Modelo **de Comboio.**
Quando esta experiência é implementada como um serviço web, o ponto final associado que saída retorna o modelo preparado no formato de um arquivo de .ilearner.

Note também que criou um parâmetro de serviço web que define o URL que o módulo **de Dados de Importação** utiliza. Isto permite-lhe utilizar o parâmetro para especificar os conjuntos de dados de treinamento individual para preparar o modelo para cada localização.
Há outras maneiras que pode fazer isso. Pode utilizar uma consulta SQL com um parâmetro de serviço da web para obter dados de uma base de dados do SQL Azure. Ou pode utilizar um módulo de entrada de **serviço web** para passar num conjunto de dados para o serviço web.

![Um módulo de modelo treinado saídas para um módulo de saída de serviço Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esta experiência de treino usando o valor padrão *rental001.csv* como o conjunto de dados de treino. Se visualizar a saída do módulo **Avaliar** (clique na saída e selecione **Visualize),** pode ver que obtém um desempenho decente de *AUC* = 0,91. Neste momento, está pronto para implementar um serviço web fora desta experiência de treinamento.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implementar a preparação e classificação de serviços da web
Para implementar o serviço web de treino, clique no botão **'Configurar** o Serviço Web' abaixo da tela de experimentação e selecionar **o Serviço Web de implantação**. Chame esse web service "Treinamento de aluguer de bicicletas".

Agora precisa implementar o serviço web de pontuação.
Para isso, clique em **Configurar** o Web Service abaixo da tela e selecione **Predictive Web Service**. Esta ação cria uma experimentação de classificação.
Precisa fazer alguns ajustes secundários fazê-lo funcionar como um serviço web. Remover a coluna de etiqueta "cnt" os dados de entrada e limitar a saída para apenas o id de instância e o valor previsto correspondente.

Para poupar esse trabalho, pode abrir a [experiência preditiva](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) na Galeria que já foi preparada.

Para implementar o serviço web, execute a experiência preditiva e, em seguida, clique no botão Implementar o **Serviço Web** abaixo da tela. Nome do serviço web de pontuação "Aluguer de bicicletas classificação".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Criar pontos finais de serviço web idênticos 10 com o PowerShell
Esse web service é fornecido com um ponto final predefinido. Mas não tão interessados no ponto de extremidade padrão, uma vez que ele não pode ser atualizado. O que precisa fazer é criar 10 pontos finais adicionais, uma para cada local. Pode fazê-lo com o PowerShell.

Em primeiro lugar, pode configurar o ambiente do PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Em seguida, execute o seguinte comando do PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Agora criaste 10 pontos finais e todos eles contêm o mesmo modelo treinado em *customer001.csv*. Pode visualizá-las no portal do Azure.

![Veja a lista de modelos treinados no portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualizar os pontos finais para utilizar conjuntos de dados de treinamento separados com o PowerShell
O passo seguinte é atualizar os pontos finais com modelos de forma exclusiva com base nos dados de cada cliente individual. Mas primeiro precisa produzir estes modelos a partir do serviço web Bike **Rental Training.** Vamos voltar ao serviço web de treino de aluguer de **bicicletas.** Precisa chamar o seu ponto de extremidade BES 10 vezes com 10 treinamento diferentes conjuntos de dados para produzir 10 modelos diferentes. Utilize o **cmdlet InovkeAmlWebServiceBESEndpoint** PowerShell para o fazer.

Também terá de fornecer credenciais para a sua conta de armazenamento blob em `$configContent`. Ou seja, nos campos `AccountName`, `AccountKey`e `RelativeLocation`. O `AccountName` pode ser um dos nomes da sua conta, como se pode ver no **portal Azure** (separador*de armazenamento).* Uma vez clicado numa conta de armazenamento, o seu `AccountKey` pode ser encontrado premindo o botão **'Gerir chaves de acesso'** na parte inferior e copiando a Chave de *Acesso Primário*. O `RelativeLocation` é o caminho relativo ao seu armazenamento onde um novo modelo será armazenado. Por exemplo, o caminho `hai/retrain/bike_rental/` no script seguinte aponta para um recipiente chamado `hai`, e `/retrain/bike_rental/` são subpastas. Atualmente, não é possível criar subpastas através do portal UI, mas existem [vários Exploradores](../../storage/common/storage-explorers.md) de Armazenamento Azure que lhe permitem fazê-lo. Recomenda-se que crie um novo recipiente no seu armazenamento para armazenar os novos modelos treinados (.iLearner) da seguinte forma: a partir da sua página de armazenamento, clique no botão **Adicionar** na parte inferior e nomeie-o `retrain`. Em resumo, as alterações necessárias ao seguinte guião dizem respeito a `AccountName`, `AccountKey`e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

> [!NOTE]
> O ponto de extremidade BES é o único modo suportado para esta operação. RRS não pode ser utilizado para a produção de modelos de formação.
> 
> 

Como pode ver acima, em vez de construir 10 diferentes BES tarefa json arquivos de configuração, cria dinamicamente a cadeia de configuração em vez disso. Em seguida, alimente-o para o parâmetro *de trabalhoConfigString* do **cmdlet InvokeAmlWebServceBESEndpoint.** Não é realmente necessário para manter uma cópia em disco.

Se tudo correr bem, passado algum tempo deverá ver 10 ficheiros .iLearner, desde *model001.ilearner* até *model010.ilearner,* na sua conta de armazenamento Azure. Agora está pronto para atualizar os 10 pontos finais do serviço web com estes modelos utilizando o cmdlet **PowerShell patch-AmlWebServiceEndpoint.** Lembre-se novamente que só é possível corrigir os pontos de extremidade não-padrão por meio de programação criado anteriormente.

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

Isso deve ser executada rapidamente. Quando a execução estiver concluída, criou com êxito 10 pontos finais do serviço web preditivo. Cada um irá conter um modelo preparado exclusivamente treinado no conjunto de dados específico para uma localização de aluguel, tudo a partir de uma experimentação de preparação único. Para verificar isto, pode tentar ligar para estes pontos finais utilizando o cmdlet **InvokeAmlWebServiceRRSEndpoint,** fornecendo-lhes os mesmos dados de entrada. Deve esperar para ver os resultados da predição diferentes, uma vez que os modelos são treinados com conjuntos de formação diferentes.

## <a name="full-powershell-script"></a>Script de PowerShell completo
Esta é a listagem de código-fonte completo:

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
