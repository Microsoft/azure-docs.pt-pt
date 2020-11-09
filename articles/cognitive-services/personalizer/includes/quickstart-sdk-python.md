---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: 791e46e73ad7292528f0197f8424d44486ea9795
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371962"
---
[Documentação de referência](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)  |  [Pacote (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title=" crie um recurso Personalizer no "  target="_blank"> portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API Personalizada. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install azure-cognitiveservices-personalizer
```

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python e crie variáveis para a chave final e subscrição do seu recurso.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
from azure.cognitiveservices.personalizer import PersonalizerClient
from azure.cognitiveservices.personalizer.models import RankableAction, RewardRequest, RankRequest
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time, uuid

key = "<paste-your-personalizer-key-here>"
endpoint = "<paste-your-personalizer-endpoint-here>"
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)em seguida, passe-o ao cliente. Método de classificação. O método Rank devolve um RankResponse.

Para enviar uma pontuação de recompensa para Personaler, desempece o ID do evento e a pontuação de recompensa (valor) para enviar para o método [Recompensa](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) na classe EventOperations.

Determinar a recompensa, neste arranque rápido é trivial. Num sistema de produção, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e o quanto pode ser um processo complexo, que poderá decidir mudar com o tempo. Esta deve ser uma das principais decisões de design na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Personalizer para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Classificação API](#request-the-best-action)
* [Recompensa API](#send-a-reward)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiu o `PersonalizerClient` com o seu e que criou mais `key` `endpoint` cedo.

```python
# Instantiate a Personalizer client
client = PersonalizerClient(endpoint, CognitiveServicesCredentials(key))
```

## <a name="get-content-choices-represented-as-actions"></a>Obtenha escolhas de conteúdo representadas como ações

As ações representam as escolhas de conteúdo a partir das quais deseja que o Personaler selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e as suas características.

```python
def get_actions():
    action1 = RankableAction(id='pasta', features=[{"taste":"salty", "spice_level":"medium"},{"nutrition_level":5,"cuisine":"italian"}])
    action2 = RankableAction(id='ice cream', features=[{"taste":"sweet", "spice_level":"none"}, { "nutritional_level": 2 }])
    action3 = RankableAction(id='juice', features=[{"taste":"sweet", 'spice_level':'none'}, {'nutritional_level': 5}, {'drink':True}])
    action4 = RankableAction(id='salad', features=[{'taste':'salty', 'spice_level':'none'},{'nutritional_level': 2}])
    return [action1, action2, action3, action4]
```

```python
def get_user_timeofday():
    res={}
    time_features = ["morning", "afternoon", "evening", "night"]
    time = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        ptime = int(time)
        if(ptime<=0 or ptime>len(time_features)):
            raise IndexError
        res['time_of_day'] = time_features[ptime-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        res['time_of_day'] = time_features[0]
    return res
```

```python
def get_user_preference():
    res = {}
    taste_features = ['salty','sweet']
    pref = input("What type of food would you prefer? Enter number 1.salty 2.sweet\n")
    
    try:
        ppref = int(pref)
        if(ppref<=0 or ppref>len(taste_features)):
            raise IndexError
        res['taste_preference'] = taste_features[ppref-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", taste_features[0]+ ".")
        res['taste_preference'] = taste_features[0]
    return res
```

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [Rank](#request-the-best-action) and [Reward.](#send-a-reward) Neste arranque rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida de uma chamada de recompensa para dizer ao Personaler o quão bem o serviço foi executado.

O código que se segue passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personaler para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma recompensa ao Personaler sinalizando o quão bem o serviço se saiu na sua seleção.

```python
keep_going = True
while keep_going:

    eventid = str(uuid.uuid4())

    context = [get_user_preference(), get_user_timeofday()]
    actions = get_actions()

    rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
    response = client.rank(rank_request=rank_request)
    
    print("Personalizer service ranked the actions with the probabilities listed below:")
    
    rankedList = response.ranking
    for ranked in rankedList:
        print(ranked.id, ':',ranked.probability)

    print("Personalizer thinks you would like to have", response.reward_action_id+".")
    answer = input("Is this correct?(y/n)\n")[0]

    reward_val = "0.0"
    if(answer.lower()=='y'):
        reward_val = "1.0"
    elif(answer.lower()=='n'):
        reward_val = "0.0"
    else:
        print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

    client.events.reward(event_id=eventid, value=reward_val)

    br = input("Press Q to exit, any other key to continue: ")
    if(br.lower()=='q'):
        keep_going = False
```

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-content-choices-represented-as-actions)antes de executar o ficheiro de código:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido rank, o programa pede às preferências do utilizador para criar uma `currentContent` das escolhas de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions` . O pedido rank precisa das ações e suas funcionalidades, funcionalidades atualescontexto, exclusões Deacções, e um ID de evento único, para receber a resposta.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e características](../concepts-features.md) pode ser uma questão não trivial.

```python
rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
response = client.rank(rank_request=rank_request)
```

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID do evento único e a pontuação da recompensa como o valor numérico para a API Reward.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Decompras](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

```python
reward_val = "0.0"
if(answer.lower()=='y'):
    reward_val = "1.0"
elif(answer.lower()=='n'):
    reward_val = "0.0"
else:
    print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

client.events.reward(event_id=eventid, value=reward_val)
```

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação com a pitão do seu diretório de candidaturas.

```console
python sample.py
```

![O programa quickstart faz algumas perguntas para recolher as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)