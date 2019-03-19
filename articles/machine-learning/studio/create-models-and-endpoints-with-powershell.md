---
title: Criar vários pontos de extremidade para um modelo
titleSuffix: Azure Machine Learning Studio
description: Utilize o PowerShell para criar vários modelos de Machine Learning e web pontos finais de serviço com o mesmo algoritmo, mas treinamento diferentes conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: a191a7adc2c43337b663fc44a8ef40df9d8ffef4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848930"
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>Utilize o PowerShell para criar modelos de Studio e pontos finais de serviço da web a partir de uma experiência

Este é um problema de aprendizado de máquina comum: Quer crie vários modelos que tenham o mesmo fluxo de trabalho de treinamento e usam o mesmo algoritmo. Mas deseja que eles tenham conjuntos de dados de treinamento diferente como entrada. Este artigo mostra-lhe como fazê-lo em escala no Azure Machine Learning Studio usando apenas uma única experiência.

Por exemplo, imaginemos que é proprietário de uma empresa de franquia de aluguer de bicicletas global. Qual quer criar um modelo de regressão para prever a procura de aluguel com base no histórico de dados. Tem 1.000 localizações de aluguel em todo o mundo e Reunimos um conjunto de dados para cada localização. Eles incluem recursos importantes, como a data, hora, meteorologia e tráfego, que são específicos para cada localização.

Pode preparar o seu modelo uma vez usando uma versão unida de todos os conjuntos de dados em todas as localizações. No entanto, cada uma das suas localizações possui um ambiente exclusivo. Portanto, uma abordagem melhor seria de preparar o seu modelo de regressão separadamente com o conjunto de dados para cada localização. Dessa forma, cada modelo preparado poderia levar em conta os tamanhos de armazenamento diferentes, volume, geografia, população, ambiente de tráfego de bicicletas amigável e muito mais.

Isso pode ser a melhor abordagem, mas não quiser criar 1.000 treinamento experimentações no Azure Machine Learning Studio com cada um representando um local exclusivo. Além de ser uma tarefa árdua, também parece ineficiente, pois cada experimentação teria todos os mesmos componentes, exceto para o conjunto de dados de treinamento.

Felizmente, isso pode ser feito utilizando o [reparametrização API do Azure Machine Learning Studio](/azure/machine-learning/studio/retrain-machine-learning-model) e automatizar as tarefas com [PowerShell do Azure Machine Learning Studio](powershell-module.md).

> [!NOTE]
> Para tornar o seu exemplo são executadas mais depressa, reduza o número de localizações de 1.000 para 10. Mas os mesmos princípios e procedimentos aplicam-se a 1.000 localizações. No entanto, se quiser dar formação de 1.000 conjuntos de dados poderá querer executar scripts do PowerShell abaixo em paralelo. Como fazer isso está além do escopo deste artigo, mas pode encontrar exemplos do PowerShell multithreading na Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurar a experimentação de preparação
Utilize o exemplo [experimentação de preparação](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) que está no [galeria do Cortana Intelligence](https://gallery.azure.ai). Abrir esta experimentação na sua [Azure Machine Learning Studio](https://studio.azureml.net) área de trabalho.

> [!NOTE]
> Para acompanhar este exemplo, pode querer utilizar uma área de trabalho padrão, em vez de uma área de trabalho gratuita. Criar um ponto final de cada cliente - para um total de 10 pontos finais - e que requer uma área de trabalho padrão, uma vez que uma área de trabalho gratuita está limitada a 3 pontos de extremidade. Se tiver apenas uma área de trabalho gratuita, basta altere os scripts para permitir apenas as localizações de th.
> 
> 

A experimentação utiliza um **importar dados** módulo para importar o conjunto de dados de treinamento *customer001.csv* de uma conta de armazenamento do Azure. Vamos supor que ter recolhido conjuntos de dados de treinamento de todos os locais de aluguer de bicicletas e os armazenado na mesma localização de armazenamento de Blobs com nomes de ficheiro que vão desde *rentalloc001.csv* ao *rentalloc10.csv*.

![Módulo leitor importa dados a partir de um blob do Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Tenha em atenção que uma **saída de serviço Web** módulo foi adicionado para o **Train Model** módulo.
Quando esta experiência é implementada como um serviço web, o ponto final associado que saída retorna o modelo preparado no formato de um arquivo de .ilearner.

Observe também que defina um parâmetro de serviço web que define o URL que o **importar dados** módulo utiliza. Isto permite-lhe utilizar o parâmetro para especificar os conjuntos de dados de treinamento individual para preparar o modelo para cada localização.
Há outras maneiras que pode fazer isso. Pode utilizar uma consulta SQL com um parâmetro de serviço da web para obter dados de uma base de dados do SQL Azure. Ou pode utilizar um **entrada de serviço Web** módulo para passar num conjunto de dados para o serviço web.

![Um módulo do modelo treinado produz a um módulo de saída do serviço Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esta experimentação de preparação com o valor predefinido *rental001.csv* como o conjunto de dados de treinamento. Se visualizar a saída do **Evaluate** módulo (clique em de saída e selecione **Visualize**), pode ver que obter um desempenho razoável de *AUC* = 0.91. Neste momento, está pronto para implementar um serviço web fora desta experiência de treinamento.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implementar a preparação e classificação de serviços da web
Para implementar o serviço da web de treinamento, clique a **segurança de serviço Web** abaixo a tela da experimentação e selecione **implementar serviço Web**. Chame esse web service "Treinamento de aluguer de bicicletas".

Agora precisa implementar o serviço web de pontuação.
Para tal, clique em **segurança de serviço Web** abaixo de tela e selecione **serviço da Web preditiva**. Esta ação cria uma experimentação de classificação.
Precisa fazer alguns ajustes secundários fazê-lo funcionar como um serviço web. Remover a coluna de etiqueta "cnt" os dados de entrada e limitar a saída para apenas o id de instância e o valor previsto correspondente.

Para guardar esse trabalho, pode abrir o [experimentação preditiva](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) na galeria que já tenha sido preparada.

Para implementar o serviço web, execute a experimentação preditiva, em seguida, clique nas **implementar serviço Web** botão abaixo da tela. Nome do serviço web de pontuação "Aluguer de bicicletas classificação".

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

Agora que criou 10 pontos finais e todos eles contenham o mesmo preparado o modelo preparado na *customer001.csv*. Pode visualizá-las no portal do Azure.

![Ver a lista de modelos de formação no portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualizar os pontos finais para utilizar conjuntos de dados de treinamento separados com o PowerShell
O passo seguinte é atualizar os pontos finais com modelos de forma exclusiva com base nos dados de cada cliente individual. Mas primeiro tem de produzir esses modelos a partir da **treinamento de aluguer de bicicletas** serviço web. Vamos voltar para o **treinamento de aluguer de bicicletas** serviço web. Precisa chamar o seu ponto de extremidade BES 10 vezes com 10 treinamento diferentes conjuntos de dados para produzir 10 modelos diferentes. Utilize o **InovkeAmlWebServiceBESEndpoint** cmdlet do PowerShell para fazer isso.

Também terá de fornecer credenciais para a sua conta de armazenamento de BLOBs em `$configContent`. Ou seja, em campos `AccountName`, `AccountKey`, e `RelativeLocation`. O `AccountName` pode ser um dos seus nomes de contas, como mostra a **portal do Azure** (*armazenamento* separador). Depois de clicar numa conta de armazenamento, seus `AccountKey` podem ser encontrados ao premir o **gerir chaves de acesso** botão na parte inferior e copiar o *chave de acesso primária*. O `RelativeLocation` é o caminho relativo ao seu armazenamento em que um novo modelo será armazenado. Por exemplo, o caminho `hai/retrain/bike_rental/` nos seguintes pontos de script para um contentor com o nome `hai`, e `/retrain/bike_rental/` estão subpastas. Atualmente, não é possível criar subpastas através do portal da interface do Usuário, mas há [vários exploradores de armazenamento do Azure](../../storage/common/storage-explorers.md) que permitem que faça isso. Recomenda-se que crie um novo contentor no seu armazenamento para armazenar os novos modelos treinados (ficheiros .iLearner) da seguinte forma: a partir da sua página de armazenamento, clique nas **Add** botão na parte inferior e nomeie- `retrain`. Em resumo, as alterações necessárias para o script a seguir dizem respeito a `AccountName`, `AccountKey`, e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Como pode ver acima, em vez de construir 10 diferentes BES tarefa json arquivos de configuração, cria dinamicamente a cadeia de configuração em vez disso. Em seguida, inseri-la para o *jobConfigString* parâmetro do **InvokeAmlWebServceBESEndpoint** cmdlet. Não é realmente necessário para manter uma cópia em disco.

Se tudo correr bem, depois de um tempo verá 10 .iLearner ficheiros, partir *model001.ilearner* ao *model010.ilearner*, na sua conta de armazenamento do Azure. Agora, está pronto para atualizar o 10 pontos finais de serviço web de classificação com estes modelos com o **Patch-AmlWebServiceEndpoint** cmdlet do PowerShell. Lembre-se novamente que só é possível corrigir os pontos de extremidade não-padrão por meio de programação criado anteriormente.

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

Isso deve ser executada rapidamente. Quando a execução estiver concluída, criou com êxito 10 pontos finais do serviço web preditivo. Cada um irá conter um modelo preparado exclusivamente treinado no conjunto de dados específico para uma localização de aluguel, tudo a partir de uma experimentação de preparação único. Para verificar isto, pode tentar chamar esses pontos finais utilizando o **InvokeAmlWebServiceRRSEndpoint** cmdlet, fornecendo-os com os mesmos dados de entrada. Deve esperar para ver os resultados da predição diferentes, uma vez que os modelos são treinados com conjuntos de formação diferentes.

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
