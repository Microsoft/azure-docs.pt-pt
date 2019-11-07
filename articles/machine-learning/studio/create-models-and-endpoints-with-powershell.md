---
title: Criar vários pontos de extremidade para um modelo
titleSuffix: ML Studio (classic) Azure
description: Use o PowerShell para criar vários modelos de Machine Learning e pontos de extremidade de serviço Web com o mesmo algoritmo, mas com diferentes conjuntos de DataSets de treinamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 337b90e68d0c88f6d6c0325431e963a3276dc42e
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619597"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Usar o PowerShell para criar modelos de estúdio (clássico) e pontos de extremidade de serviço Web de um experimento

Aqui está um problema comum de aprendizado de máquina: você deseja criar muitos modelos que têm o mesmo fluxo de trabalho de treinamento e usam o mesmo algoritmo. Mas você deseja que eles tenham conjuntos de dados de treinamento diferentes como entrada. Este artigo mostra como fazer isso em escala no Azure Machine Learning Studio (clássico) usando apenas um único experimento.

Por exemplo, digamos que você tenha um negócio de franquia de aluguel de bicicletas global. Você deseja criar um modelo de regressão para prever a demanda de aluguel com base em dados históricos. Você tem 1.000 locais de aluguel em todo o mundo e coletou um conjunto de um DataSet para cada local. Eles incluem recursos importantes, como data, hora, clima e tráfego específicos de cada local.

Você pode treinar seu modelo uma vez usando uma versão mesclada de todos os conjuntos de todos os locais. Mas, cada um de seus locais tem um ambiente exclusivo. Portanto, uma abordagem melhor seria treinar o modelo de regressão separadamente usando o conjunto de um para cada local. Dessa forma, cada modelo treinado poderia levar em conta os diferentes tamanhos de armazenamento, volume, geografia, população, ambiente de tráfego amigável de bicicletas e muito mais.

Essa pode ser a melhor abordagem, mas você não quer criar experimentos de treinamento 1.000 na versão clássica do Azure Machine Learning Studio com cada um representando um local exclusivo. Além de ser uma tarefa árdua, também parece ineficiente, pois cada experimento teria todos os mesmos componentes, exceto o conjunto de dado de treinamento.

Felizmente, você pode fazer isso usando a API de readaptação [Azure Machine Learning Studio (clássica)](/azure/machine-learning/studio/retrain-machine-learning-model) e automatizando a tarefa com o [PowerShell Azure Machine Learning Studio (clássico)](powershell-module.md).

> [!NOTE]
> Para fazer com que seu exemplo seja executado mais rapidamente, reduza o número de locais de 1.000 para 10. Mas os mesmos princípios e procedimentos se aplicam a locais 1.000. No entanto, se você quiser treinar a partir de 1.000 conjuntos de data, talvez queira executar os seguintes scripts do PowerShell em paralelo. Como fazer isso está além do escopo deste artigo, mas você pode encontrar exemplos de multithreading do PowerShell na Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurar o teste de treinamento
Use o [teste de treinamento](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) de exemplo que está no [Cortana Intelligence Gallery](https://gallery.azure.ai). Abra este experimento no seu espaço de trabalho [Azure Machine Learning Studio (clássico)](https://studio.azureml.net) .

> [!NOTE]
> Para acompanhar este exemplo, talvez você queira usar um espaço de trabalho padrão em vez de um espaço de trabalho gratuito. Você cria um ponto de extremidade para cada cliente – para um total de 10 pontos de extremidade, e isso requer um espaço de trabalho padrão, já que um espaço de trabalho gratuito é limitado a 3 pontos de extremidade.
> 
> 

O experimento usa um módulo de **importação de dados** para importar o DataSet de treinamento *customer001. csv* de uma conta de armazenamento do Azure. Vamos supor que você coletou conjuntos de coleta de os em todos os locais de aluguel de bicicletas e os armazenou no mesmo local de armazenamento de blob com nomes de arquivo que vão de *rentalloc001. csv* para *rentalloc10. csv*.

![O módulo leitor importa dados de um blob do Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Observe que um módulo de **saída do serviço Web** foi adicionado ao módulo **modelo de treinamento** .
Quando esse experimento é implantado como um serviço Web, o ponto de extremidade associado a essa saída retorna o modelo treinado no formato de um arquivo. ilearner.

Observe também que você configura um parâmetro de serviço Web que define a URL usada pelo módulo **importar dados** . Isso permite que você use o parâmetro para especificar conjuntos de itens de treinamento individuais para treinar o modelo para cada local.
Há outras maneiras de fazer isso. Você pode usar uma consulta SQL com um parâmetro de serviço Web para obter dados de um banco de dado SQL Azure. Ou você pode usar um módulo de **entrada de serviço Web** para passar um conjunto de dados para o serviço Web.

![Um módulo de modelo treinado gera saídas para um módulo de saída do serviço Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esse teste de treinamento usando o valor padrão *rental001. csv* como o conjunto de testes de treinamento. Se você exibir a saída do módulo **avaliar** (clique na saída e selecionar **Visualizar**), poderá ver que obtém um desempenho razoável de *auc* = 0,91. Neste ponto, você está pronto para implantar um serviço Web fora deste experimento de treinamento.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implantar os serviços Web de treinamento e Pontuação
Para implantar o serviço Web de treinamento, clique no botão **configurar serviço Web** abaixo da tela do experimento e selecione **implantar serviço Web**. Chame este serviço Web "treinamento de aluguel de bicicletas".

Agora você precisa implantar o serviço Web de pontuação.
Para fazer isso, clique em **configurar serviço Web** abaixo da tela e selecione **serviço Web de previsão**. Isso cria um experimento de pontuação.
Você precisa fazer alguns ajustes secundários para fazê-lo funcionar como um serviço Web. Remova a coluna de rótulo "CNT" dos dados de entrada e limite a saída somente para a ID da instância e o valor previsto correspondente.

Para economizar esse trabalho, você pode abrir o [experimento de previsão](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) na galeria que já foi preparada.

Para implantar o serviço Web, execute o teste de previsão e clique no botão **implantar serviço Web** abaixo da tela. Nomeie o serviço Web de Pontuação "Pontuação de aluguel de bicicletas".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Criar 10 pontos de extremidade de serviço Web idênticos com o PowerShell
Esse serviço Web vem com um ponto de extremidade padrão. Mas você não está interessado no ponto de extremidade padrão, pois ele não pode ser atualizado. O que você precisa fazer é criar 10 pontos de extremidade adicionais, um para cada local. Você pode fazer isso com o PowerShell.

Primeiro, você configura o ambiente do PowerShell:

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

Agora você criou 10 pontos de extremidade e todos eles contêm o mesmo modelo treinado treinado em *customer001. csv*. Você pode exibi-los no portal do Azure.

![Exibir a lista de modelos treinados no portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualizar os pontos de extremidade para usar conjuntos de valores de treinamento separados usando o PowerShell
A próxima etapa é atualizar os pontos de extremidade com modelos treinados exclusivamente nos dados individuais de cada cliente. Mas primeiro você precisa produzir esses modelos do serviço Web de **treinamento de aluguel de bicicletas** . Vamos voltar ao serviço Web de **treinamento de aluguel de bicicletas** . Você precisa chamar seu ponto de extremidade BES 10 vezes com 10 conjuntos de DataSets de treinamento diferentes para produzir 10 modelos diferentes. Use o cmdlet do PowerShell **InovkeAmlWebServiceBESEndpoint** para fazer isso.

Você também precisará fornecer credenciais para sua conta de armazenamento de BLOBs em `$configContent`. Isto é, nos campos `AccountName`, `AccountKey`e `RelativeLocation`. O `AccountName` pode ser um de seus nomes de conta, como visto na **portal do Azure** (guia*armazenamento* ). Depois de clicar em uma conta de armazenamento, sua `AccountKey` pode ser encontrada pressionando o botão **gerenciar chaves de acesso** na parte inferior e copiando a *chave de acesso primária*. O `RelativeLocation` é o caminho relativo ao armazenamento em que um novo modelo será armazenado. Por exemplo, o caminho `hai/retrain/bike_rental/` no script a seguir aponta para um contêiner chamado `hai`e `/retrain/bike_rental/` são subpastas. No momento, não é possível criar subpastas por meio da interface do usuário do portal, mas há [vários gerenciadores de armazenamento do Azure](../../storage/common/storage-explorers.md) que permitem fazer isso. É recomendável que você crie um novo contêiner no armazenamento para armazenar os novos modelos treinados (arquivos. iLearner) da seguinte maneira: na sua página de armazenamento, clique no botão **Adicionar** na parte inferior e nomeie-o `retrain`. Em resumo, as alterações necessárias ao script a seguir pertencem a `AccountName`, `AccountKey`e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> O ponto de extremidade BES é o único modo com suporte para esta operação. RRS não podem ser usados para produzir modelos treinados.
> 
> 

Como você pode ver acima, em vez de construir 10 arquivos JSON de configuração de trabalho BES diferentes, você cria dinamicamente a cadeia de caracteres de configuração em vez disso. Em seguida, o feed para o parâmetro *jobConfigString* do cmdlet **InvokeAmlWebServceBESEndpoint** . Realmente não há necessidade de manter uma cópia no disco.

Se tudo correr bem, após um tempo, você deverá ver 10 arquivos. iLearner, de *model001. iLearner* para *model010. iLearner*, em sua conta de armazenamento do Azure. Agora você está pronto para atualizar os 10 pontos de extremidade de serviço Web de pontuação com esses modelos usando o cmdlet **patch-AmlWebServiceEndpoint** do PowerShell. Lembre-se novamente de que você só pode corrigir os pontos de extremidade não padrão criados por programação anteriormente.

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

Isso deve ser executado com bastante rapidez. Quando a execução for concluída, você terá criado 10 pontos de extremidade de serviço Web de previsão com êxito. Cada uma delas conterá um modelo treinado exclusivamente treinado no conjunto de informações específico para um local de aluguel, tudo a partir de um único experimento de treinamento. Para verificar isso, você pode tentar chamar esses pontos de extremidade usando o cmdlet **InvokeAmlWebServiceRRSEndpoint** , fornecendo-os com os mesmos dados de entrada. Você deve esperar ver resultados de previsão diferentes, pois os modelos são treinados com diferentes conjuntos de treinamento.

## <a name="full-powershell-script"></a>Script completo do PowerShell
Aqui está a lista do código-fonte completo:

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
