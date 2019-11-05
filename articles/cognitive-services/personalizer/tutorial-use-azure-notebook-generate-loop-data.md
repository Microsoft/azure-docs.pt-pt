---
title: 'Tutorial: Azure Notebook-personalizador'
titleSuffix: Azure Cognitive Services
description: Este tutorial simula um loop personalizado _system em um bloco de anotações do Azure, que sugere o tipo de café que um cliente deve solicitar. Os usuários e suas preferências são armazenados em um conjunto de um usuário. As informações sobre o café também estão disponíveis e armazenadas em um conjunto de dados de café.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 6bc306551d158d4b996002de0bb5ab991a0bcbd9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467238"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Tutorial: usar o personalizador no bloco de anotações do Azure

Este tutorial executa um loop personalizador em um bloco de anotações do Azure, demonstrando o ciclo de vida de ponta a ponta de um loop personalizador. 

O loop sugere o tipo de café que um cliente deve solicitar. Os usuários e suas preferências são armazenados em um conjunto de um usuário. As informações sobre o café são armazenadas em um conjunto de dados de café.

## <a name="users-and-coffee"></a>Usuários e café

O notebook seleciona um usuário aleatório, a hora do dia e o tipo de clima do conjunto de um. Um resumo das informações do usuário é:

|Clientes-recursos de contexto|Horas do dia|Tipos de clima|
|--|--|--|
|Maria<br>José<br>Cathy<br>Dave|Manhã<br>Tarde<br>À|Ensolarado<br>Rainy<br>Nevado| 

Para ajudar a personalizar o aprendizado, ao longo do tempo, a seleção de café correta para cada pessoa, o _sistema_ também conhece os detalhes sobre o café.

|Recursos de ação de café|Tipos de temperatura|Locais de origem|Tipos de assado|Sistemático|
|--|--|--|--|--|
|Cappacino|Acesso Frequente|Quénia|Escuro|Sistemático|
|Brew frio|Frio|Brasil|Claro|Sistemático|
|Iced Mocha|Frio|Etiópia|Claro|Não orgânica|
|Expresso|Acesso Frequente|Brasil|Escuro|Não orgânica|


A **finalidade** do loop do personalizador é encontrar a melhor correspondência entre os usuários e o café o máximo possível. 

O código para este tutorial está disponível no [repositório GitHub de exemplos do personalizador](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Como a simulação funciona

No início do sistema em execução, as sugestões do personalizador são bem-sucedidas apenas entre 20% e 30% (indicado pela pontuação de recompensa de 1). Após algumas solicitações, o sistema melhora.

Após as solicitações iniciais 10.000, execute uma avaliação offline. Isso permite que o personalizador examine os dados e sugira uma melhor política de aprendizado. Aplique a nova política de aprendizado e execute o bloco de anotações novamente com 2.000 solicitações. O loop terá um desempenho melhor.

## <a name="rank-and-reward-calls"></a>Classificar e recompensar chamadas

Para cada uma das primeiras mil chamadas para o serviço personalizador, o bloco de anotações do Azure envia a solicitação de **classificação** para a API REST:

* Uma ID exclusiva para o evento de classificação/solicitação
* Contexto-uma opção aleatória do usuário, clima e hora do dia – simulando um usuário em um site ou dispositivo móvel
* Recursos – _todos_ os dados de café – dos quais o personalizador faz uma sugestão

O sistema recebe a classificação das opções de café e, em seguida, compara essa previsão com a opção conhecida do usuário para a mesma hora do dia e clima. Se a opção conhecida for a mesma que a escolha prevista, o **prêmio** de 1 será enviado de volta para o personalizador. Caso contrário, o prêmio será 0. 

> [!Note]
> Essa é uma simulação para que o algoritmo para a recompensa seja simples. Em um cenário do mundo real, o algoritmo deve usar a lógica de negócios, possivelmente com pesos para vários aspectos da experiência do cliente, para determinar a pontuação de recompensa. 


## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [do bloco de anotações do Azure](https://notebooks.azure.com/) . 
* Um [recurso personalizador do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). 
    * Se você já tiver usado o recurso personalizado, certifique-se de [limpar os dados](how-to-settings.md#clear-data-for-your-learning-loop) no portal do Azure para o recurso. 
* Carregue todos os arquivos [deste exemplo](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) em um projeto de bloco de anotações do Azure. 

Descrições de arquivo:

* O [personalizador. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) é o Jupyter Notebook para este tutorial.
* O [conjunto de usuários do usuário](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) é armazenado em um objeto JSON.
* O conjunto de objetos de [café](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) é armazenado em um objeto JSON. 
* [Exemplo de solicitação JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) é o formato esperado para uma solicitação post para a API de classificação.

## <a name="configure-personalizer-resource"></a>Configurar recurso personalizador

Na portal do Azure, configure o [recurso personalizado](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) com a frequência do **modelo de atualização** definido como 15 segundos e um **tempo de espera de recompensa** de 15 segundos. Esses valores são encontrados na página de **[configuração](how-to-settings.md#configure-service-settings-in-the-azure-portal)** . 

|Definição|Valor|
|--|--|
|atualizar frequência do modelo|15 segundos|
|tempo de espera de recompensa|15 segundos|

Esses valores têm uma duração muito curta para mostrar as alterações neste tutorial. Esses valores não devem ser usados em um cenário de produção sem validá-los para atingir seu objetivo com o loop personalizador. 

## <a name="set-up-the-azure-notebook"></a>Configurar o bloco de anotações do Azure

1. Altere o kernel para `Python 3.6`. 
1. Abra o ficheiro `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Executar células do bloco de anotações

Execute cada célula executável e aguarde que ela seja retornada. Você sabe que isso é feito quando os colchetes ao lado da célula exibem um número em vez de um `*`. As seções a seguir explicam o que cada célula faz programaticamente e o que esperar para a saída. 

### <a name="include-the-python-modules"></a>Incluir os módulos do Python

Inclua os módulos do python necessários. A célula não tem nenhuma saída.

```python
import json
import matplotlib.pyplot as plt
import random 
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Definir o nome e a chave de recurso do personalizador

Na portal do Azure, localize sua chave e ponto de extremidade na página **início rápido** do recurso personalizado. Altere o valor de `<your-resource-name>` para o nome do seu recurso personalizador. Altere o valor de `<your-resource-key>` para a chave do personalizador. 

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Imprimir data e hora atuais
Use essa função para anotar as horas de início e de término da função iterativa, iterações.

Essas células não têm nenhuma saída. A função faz a saída da data e hora atual quando chamada.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Obter a hora da última atualização do modelo

Quando a função, `get_last_updated`, é chamada, a função imprime a data da última modificação e a hora em que o modelo foi atualizado. 

Essas células não têm nenhuma saída. A função faz a saída da última data de treinamento do modelo quando chamada.

A função usa uma API REST GET para [obter propriedades de modelo](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties). 

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):
    
    print('-----checking model')
    
    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)
    
    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])
    
    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Obter a configuração de política e serviço

Valide o estado do serviço com essas duas chamadas REST.

Essas células não têm nenhuma saída. A função faz a saída dos valores de serviço quando chamado.

```python
def get_service_settings():
    
    print('-----checking service settings')
    
    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
    
    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Construir URLs e ler arquivos de dados JSON

Esta célula 

* compila as URLs usadas em chamadas REST 
* define o cabeçalho de segurança usando a chave de recurso do personalizador 
* define a semente aleatória para a ID do evento de classificação
* leituras nos arquivos de dados JSON
* chama `get_last_updated` método – a política de aprendizado foi removida na saída de exemplo
* chama `get_service_settings` método

A célula tem saída da chamada para funções de `get_last_updated` e `get_service_settings`.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None 
rankactionsjsonobj = None 
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())
    
with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())  
    
get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Verifique se os `rewardWaitTime` e os `modelExportFrequency` da saída estão definidos como 15 segundos. 
    
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Solucionando problemas da primeira chamada REST

Esta célula anterior é a primeira célula que chama o personalizador. Verifique se o código de status REST na saída é `<Response [200]>`. Se você receber um erro, como 404, mas tiver certeza de que a chave de recurso e o nome estão corretos, recarregue o bloco de anotações.

Verifique se a contagem de café e usuários é 4. Se você receber um erro, verifique se carregou todos os 3 arquivos JSON. 

### <a name="set-up-metric-chart-in-azure-portal"></a>Configurar o gráfico de métricas no portal do Azure

Posteriormente neste tutorial, o processo de execução demorada de 10.000 solicitações é visível no navegador com uma caixa de texto de atualização. Pode ser mais fácil ver em um gráfico ou como uma soma total, quando o processo de execução longa termina. Para exibir essas informações, use as métricas fornecidas com o recurso. Você pode criar o gráfico agora que concluiu uma solicitação para o serviço e, em seguida, atualizando o gráfico periodicamente enquanto o processo de execução longa está indo.

1. No portal do Azure, selecione o recurso personalizador.
1. Na navegação de recursos, selecione **métricas** sob monitoramento. 
1. No gráfico, selecione **Adicionar métrica**.
1. O namespace de recurso e métrica já está definido. Você só precisa selecionar a métrica de **chamadas bem-sucedidas** e a agregação de **sum**.
1. Altere o filtro de tempo para as últimas 4 horas.

    ![Configure o gráfico de métricas em portal do Azure, adicionando métrica para chamadas bem-sucedidas para as últimas 4 horas.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Você deve ver três chamadas bem-sucedidas no gráfico. 

### <a name="generate-a-unique-event-id"></a>Gerar uma ID de evento exclusiva

Essa função gera uma ID exclusiva para cada chamada de classificação. A ID é usada para identificar a classificação e recompensar as informações de chamada. Esse valor pode vir de um processo comercial, como uma ID de exibição da Web ou uma ID de transação.

A célula não tem nenhuma saída. A função faz a saída da ID exclusiva quando chamada.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Obter usuário aleatório, clima e hora do dia

Essa função seleciona um usuário exclusivo, clima e hora do dia e, em seguida, adiciona esses itens ao objeto JSON para enviar para a solicitação de classificação.

A célula não tem nenhuma saída. Quando a função é chamada, retorna o nome do usuário aleatório, o clima aleatório e a hora aleatória do dia.

A lista de 4 usuários e suas preferências-somente algumas preferências são mostradas para fins de brevidade: 

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):   
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Adicionar todos os dados de café

Essa função adiciona a lista completa de café ao objeto JSON para enviar para a solicitação de classificação. 

A célula não tem nenhuma saída. A função altera o `rankjsonobj` quando chamado.


O exemplo de recursos de um único café é: 

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"
        
    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Comparar previsão com a preferência do usuário conhecida

Essa função é chamada depois que a API de classificação é chamada, para cada iteração.

Essa função compara a preferência do usuário para café, com base no clima e na hora do dia, com a sugestão do personalizador para o usuário para esses filtros. Se a sugestão corresponder, uma pontuação de 1 será retornada, caso contrário, a pontuação será 0. A célula não tem nenhuma saída. A função faz a saída da Pontuação quando chamada.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1 
    return 0
``` 

### <a name="loop-through-calls-to-rank-and-reward"></a>Loop por meio de chamadas para classificação e recompensa

A próxima célula é o trabalho _principal_ do notebook, obtendo um usuário aleatório, obtendo a lista de café, enviando ambos para a API de classificação. Comparar a previsão com as preferências conhecidas do usuário e, em seguida, enviar a recompensa de volta para o serviço personalizador. 

O loop é executado por `num_requests` vezes. O personalizador precisa de algumas mil chamadas para classificar e recompensar a criação de um modelo. 

Segue um exemplo do JSON enviado para a API de classificação. A lista de café não está completa, por concisão. Você pode ver o JSON inteiro para café em `coffee.json`.

JSON enviado para a API de classificação:

```json
{ 
   'contextFeatures':[ 
      { 
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[ 
      { 
         'id':'Cappucino',
         'features':[ 
            { 
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[ 

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Resposta JSON da API de classificação:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ], 
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2', 
    'rewardActionId': 'Latte'
}
```

Por fim, cada loop mostra a seleção aleatória de usuário, clima, hora do dia e a recompensa determinada. O prêmio de 1 indica que o recurso personalizador selecionou o tipo de café correto para determinado usuário, clima e hora do dia.

```console
1 Alice Rainy Morning Latte 1
```

A função usa:

* Classificação: uma API de POSTAgem REST para [obter classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank). 
* Recompensa: uma API de POSTAgem REST para [relatar recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1
    
    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']
    
    
    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat) 

        # show JSON to send to Rank
        print('To: ', jsonFormat)    

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction 
        print ('From: ',response.json())    

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service 
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified) 

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1
        
    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Executar para iterações de 10.000
Execute o loop do personalizador para iterações de 10.000. Esse é um evento de longa execução. Não feche o navegador que está executando o bloco de anotações. Atualize o gráfico de métricas no portal do Azure periodicamente para ver o total de chamadas para o serviço. Quando você tem cerca de 20.000 chamadas, uma chamada de classificação e recompensa para cada iteração do loop, as iterações são feitas. 

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Resultados do gráfico para ver a melhoria 

Crie um gráfico do `count` e `rewards`.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Executar o gráfico para solicitações de classificação de 10.000

Execute a função `createChart`.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Lendo o gráfico

Este gráfico mostra o sucesso do modelo para a política de aprendizado padrão atual. 

![Este gráfico mostra o sucesso da política de aprendizado atual durante o teste.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


O destino ideal que, ao final do teste, o loop é a média de uma taxa de sucesso que está perto de 100% menos a exploração. O valor padrão de exploração é de 20%. 

`100-20=80`

Esse valor de exploração é encontrado no portal do Azure, para o recurso personalizado, na página **configuração** . 

Para encontrar uma melhor política de aprendizado, com base em seus dados para a API de classificação, execute uma [avaliação offline](how-to-offline-evaluation.md) no portal para o loop personalizador.

## <a name="run-an-offline-evaluation"></a>Executar uma avaliação offline

1. Na portal do Azure, abra a página **avaliações** do recurso personalizador.
1. Selecione **criar avaliação**.
1. Insira os dados necessários do nome de avaliação e o intervalo de datas para a avaliação do loop. O intervalo de datas deve incluir apenas os dias nos quais você está se concentrando para sua avaliação. 
    ![na portal do Azure, abra a página de avaliações do recurso personalizador. Selecione criar avaliação. Insira o nome da avaliação e o intervalo de datas.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    A finalidade de executar essa avaliação offline é determinar se há uma melhor política de aprendizado para os recursos e as ações usadas neste loop. Para descobrir essa melhor política de aprendizado, verifique se a **política de otimização** está ativada.

1. Selecione **OK** para iniciar a avaliação. 
1. Essa página de **avaliações** lista a nova avaliação e seu status atual. Dependendo da quantidade de dados que você tem, essa avaliação pode levar algum tempo. Você pode voltar para esta página depois de alguns minutos para ver os resultados. 
1. Quando a avaliação for concluída, selecione a avaliação e, em seguida, selecione **comparação de diferentes políticas de aprendizagem**. Isso mostra as políticas de aprendizado disponíveis e como elas se comportariam com os dados. 
1. Selecione a política de aprendizado mais alta na tabela e selecione **aplicar**. Isso aplica a _melhor_ política de aprendizado ao seu modelo e se retreina. 

## <a name="change-update-model-frequency-to-5-minutes"></a>Alterar a frequência do modelo de atualização para 5 minutos

1. No portal do Azure, ainda no recurso personalizador, selecione a página **configuração** . 
1. Altere a **frequência de atualização do modelo** e **recompensa o tempo de espera** para 5 minutos e selecione **salvar**.

Saiba mais sobre o [tempo de espera de recompensa](concept-rewards.md#reward-wait-time) e a [frequência de atualização do modelo](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Verifique se os `rewardWaitTime` e os `modelExportFrequency` da saída estão definidos como 5 minutos. 
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Validar nova política de aprendizado 

Retorne ao bloco de anotações do Azure e continue executando o mesmo loop, mas apenas para iterações 2.000. Atualize o gráfico de métricas no portal do Azure periodicamente para ver o total de chamadas para o serviço. Quando você tem cerca de 4.000 chamadas, uma chamada de classificação e recompensa para cada iteração do loop, as iterações são feitas. 

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Executar o gráfico para solicitações de classificação de 2.000

Execute a função `createChart`.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Examinar o segundo gráfico

O segundo gráfico deve mostrar um aumento visível nas previsões de classificação que se alinham com as preferências do usuário. 

![O segundo gráfico deve mostrar um aumento visível nas previsões de classificação que se alinham com as preferências do usuário.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende continuar a série de tutoriais, limpe os seguintes recursos:

* Exclua seu projeto de bloco de anotações do Azure. 
* Exclua seu recurso personalizador. 

## <a name="next-steps"></a>Passos seguintes

O [bloco de anotações e os arquivos de dados do Jupyter](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) usados neste exemplo estão disponíveis no repositório GitHub para personalizador. 

