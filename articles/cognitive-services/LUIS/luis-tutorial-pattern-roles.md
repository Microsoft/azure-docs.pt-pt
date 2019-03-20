---
title: Funções padrão
titleSuffix: Azure Cognitive Services
description: Utilize um padrão para extrair dados de uma expressão de modelo bem formatada. A expressão de modelo utiliza uma entidade simples e funções para extrair dados relacionados, como a localização de origem e a localização de destino.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: dc1be0d1d00ae64f38690f019580119b03debedf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106598"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>Tutorial: Extrair contextualmente relacionados com padrões de utilização de funções

Neste tutorial, utilize um padrão para extrair dados de uma expressão de modelo bem formatada. A expressão de modelo utiliza uma entidade simples e funções para extrair dados relacionados, como a localização de origem e a localização de destino.  Ao utilizar padrões, são necessárias menos expressões de exemplo para a intenção.


**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Aplicação de exemplo de importação
> * Criar novas entidades
> * Criar nova intenção
> * Preparar
> * Publicar
> * Obter as intenções e as entidades do ponto final
> * Criar padrão com funções
> * Criar lista de expressões de Cidades
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>Utilização de funções em padrões

A finalidade das funções é extrair entidades relacionadas contextualmente numa expressão. Na expressão, `Move new employee Robert Williams from Sacramento and San Francisco`, os valores da cidade de origem e da cidade de destino estão relacionados entre si e utilizam uma linguagem comum para indicar cada localização. 


O nome do novo colaborador, Billy Patterson, ainda não faz parte da entidade de lista **Colaborador**. O nome do novo colaborador é extraído em primeiro lugar, para enviar o nome para um sistema externo, de modo a criar as credenciais da empresa. Depois de as credenciais da empresa serem criadas, as credenciais do colaborador são adicionadas à entidade de lista **Colaborador**.

O novo colaborador e a família têm de ser movidos da cidade atual para a cidade onde está localizada a empresa fictícia. Tendo em conta que um novo colaborador pode vir de qualquer cidade, as localizações têm de ser detetadas. Uma lista de conjunto, como uma entidade de lista, não funcionaria porque apenas as cidades na lista seriam extraídas.

Os nomes das funções associadas às cidades de origem e de destino têm de ser exclusivos em todas as entidades. Uma forma fácil de certificar-se que as funções são exclusivas é associá-las à entidade contentora através de uma estratégia de nomenclatura. O **NewEmployeeRelocation** entidade é uma entidade com duas funções: **NewEmployeeReloOrigin** e **NewEmployeeReloDestination**. Relo é a abreviatura de relocation (transferência).

Uma vez que a expressão de exemplo `Move new employee Robert Williams from Sacramento and San Francisco` tem apenas entidades de aprendizagem automática, é importante fornecer expressões de exemplo suficientes à intenção, para que as entidades sejam detetadas.  

**Embora os padrões permitam que forneça menos expressões de exemplo, se as entidades não forem detetadas, o padrão não corresponde.**

Se tiver dificuldade na detecção de uma entidade simples porque consiste num nome como uma cidade, considere adicionar uma lista de expressões com valores similares. Isto facilita a deteção do nome da cidade, ao fornecer ao LUIS uma indicação adicional sobre esse tipo de palavra ou expressão. As listas de expressões só ajudam o padrão ao ajudar na deteção de entidades, que é necessária para o padrão corresponder. 

## <a name="import-example-app"></a>Aplicação de exemplo de importação
Continue com a aplicação criada no último tutorial, com o nome **RecursosHumanos**. 

Utilize os passos seguintes:

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `roles`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="create-new-entities"></a>Criar novas entidades

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Entidades** no painel de navegação esquerdo. 

3. Selecione **Criar nova entidade**.

4. Na janela de pop-up, introduza `NewEmployee` como uma entidade **Simples**.

5. Selecione **Criar nova entidade**.

6. Na janela de pop-up, introduza `NewEmployeeRelocation` como uma entidade **Simples**.

7. Selecione **NewEmployeeRelocation** na lista de entidades. 

8. Introduza a primeira função como `NewEmployeeReloOrigin` e selecione Enter.

9. Introduza a segunda função como `NewEmployeeReloDestination` e selecione Enter.

## <a name="create-new-intent"></a>Criar nova intenção
A identificação das entidades nestes passos poderá ser mais fácil se a entidade keyPhrase pré-criada for removida antes de começar e for adicionada novamente depois de concluir os passos desta secção. 

1. Selecione **Intenções** no painel de navegação esquerdo.

2. Selecione **Create new intent** (Criar nova intenção). 

3. Introduza `NewEmployeeRelocationProcess` como o nome da intenção na caixa de diálogo de pop-up.

4. Introduza as seguintes expressões de exemplo, que identificam as novas entidades. Os valores da entidade e da função estão em negrito. Não se esqueça de mudar para a **Vista de Tokens**, se facilitar a identificação do texto. 

    Não especifique a função da entidade ao identificar a intenção. Faça isso mais tarde quando criar o padrão. 

    |Expressão|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Mover **Bob Jones** de **Seattle** para **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Mover **Dave C. Cooper** de **Redmond** para **New York City**|Dave C. Cooper|Redmond, New York City|
    |Mover **Jim Paul Smith** de **Toronto** para **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Mover **J. Benson** de **Boston** para **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Mover **Travis “Trav” Hinton** de **Castelo Branco** para **Orlando**|Travis “Trav” Hinton|Castelo Branco, Orlando|
    |Mover **Trevor Nottington III** de **Aranda de Duero** para **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Mover **Dr. Greg Williams** de **Orlando** para **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Mover **Robert “Bobby” Gregson** de **Kansas City** para **San Juan Capistrano**|Robert “Bobby” Gregson|Kansas City, San Juan Capistrano|
    |Mover **Patti Owens** de **Bellevue** para **Rockford**|Patti Owens|Bellevue, Rockford|
    |Mover **Janet Bartlet** de **Tuscan** para **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    O nome do colaborador tem uma variedade de prefixo, contagem de palavras, sintaxe e sufixo. É importante para o LUIS compreender as variações de nome de um novo funcionário. Os nomes das cidades também têm uma variedade de contagem de palavras e sintaxe. Esta variedade é importante para ensinar ao LUIS como estas entidades podem aparecer na expressão de um utilizador. 
    
    Se uma entidade tivesse a mesma contagem de palavras e nenhumas outras variações, iria ensinar ao LUIS que essa entidade tem apenas essa contagem de palavras e nenhumas outras variações. O LUIS não conseguiria prever corretamente um conjunto mais amplo de variações porque não lhe foram apresentadas nenhumas variações. 

    Se tiver removido a entidade keyPhrase, adicione-a agora novamente à aplicação.

## <a name="train"></a>Preparar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter as intenções e as entidades do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Vá para o final do URL no endereço e introduza `Move Wayne Berry from Miami to Mount Vernon`. O último parâmetro querystring é `q`, a expressão **query**. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

A pontuação de predição de intenção é apenas cerca de 50%. Se a aplicação cliente precisar de um número mais elevado, isto tem de ser corrigido. As entidades também não foram previstas.

Uma das localizações foi extraída, mas a outra localização não o foi. 

Os padrões irão ajudar na pontuação de predição, no entanto, as entidades têm de ser previstas corretamente antes de o padrão corresponder a expressão. 

## <a name="pattern-with-roles"></a>Padrão com funções

1. Selecione **Compilar** no painel de navegação superior.

2. Selecione **Padrões** no painel de navegação esquerdo.

3. Selecione **NewEmployeeRelocationProcess** na lista pendente **Selecionar uma intenção**. 

4. Introduza o seguinte padrão: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Se preparar, publicar e consultar o ponto final, poderá ficar desapontado ao ver que as entidades não foram encontradas, o que significa que o padrão não correspondeu e, por conseguinte, a predição não melhorou. Esta é uma consequência de não existirem expressões de exemplo suficientes com entidades identificadas. Em vez de adicionar mais exemplos, adicione uma lista de expressões para corrigir este problema.

## <a name="cities-phrase-list"></a>Lista de expressões de cidades
Tal como os nomes das pessoas, as cidades também são complicadas pois podem ser uma combinação de palavras e pontuação. As cidades da região e do mundo são conhecidas, portanto, o LUIS precisa de uma lista de expressões de cidades para começar a aprender. 

1. Selecione **Lista de expressões** na secção **Melhorar o desempenho da aplicação** do menu à esquerda. 

2. Dê o nome `Cities` à lista e adicione os seguintes `values` à lista:

    |Valores da lista de expressões|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Não adicione todas as cidades do mundo, nem sequer todas as cidades da região. O LUIS tem de conseguir generalizar o que é uma cidade a partir da lista. Certifique-se de que mantém **Estes valores são intercambiáveis** selecionado. Esta definição significa que as palavras na lista são tratadas como sinónimos. 

3. Prepare e publique a aplicação.

## <a name="get-intent-and-entities-from-endpoint"></a>Obter as intenções e as entidades do ponto final

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vá para o final do URL no endereço e introduza `Move wayne berry from miami to mount vernon`. O último parâmetro querystring é `q`, a expressão **query**. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
   }
    ```

A pontuação da intenção é agora muito superior e os nomes das funções fazem parte da resposta da entidade.

## <a name="hierarchical-entities-versus-roles"></a>Entidades hierárquicas versus funções

No [tutorial hierárquico](luis-quickstart-intent-and-hier-entity.md), a intenção **MoveEmployee** detetou quando mover um colaborador existente de um edifício e escritório para outro. As expressões de exemplo tinham as localizações de origem e destino, mas não utilizaram funções. Em vez disso, a origem e o destino eram subordinados da entidade hierárquica. 

Neste tutorial, a aplicação Recursos Humanos deteta expressões sobre a transferência de novos colaboradores de uma cidade para outra. Estes dois tipos de expressões são os mesmos, mas foram resolvidos com diferentes capacidades do LUIS.

|Tutorial|Expressão de exemplo|Localizações de origem e destino|
|--|--|--|
|[Hierárquica (sem funções)](luis-quickstart-intent-and-hier-entity.md)|transf Jill Jones de **a-2349** para **b-1298**|a-2349, b-1298|
|Este tutorial (com funções)|Mover Billy Patterson de **Yuma** para **Denver**.|Yuma, Denver|

Para obter mais informações, veja [Funções versus entidades hierárquicas](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial adicionou uma entidade com funções e uma intenção com expressões de exemplo. A primeira predição de ponto final que utilizou a entidade corretamente previu a intenção, mas com uma pontuação de confiança baixa. Apenas uma das duas entidades foi detetada. Em seguida, o tutorial adicionou um padrão que utilizou as funções de entidade e uma lista de expressões para aumentar o valor dos nomes de cidades nas expressões. A segunda predição de ponto final devolveu uma pontuação de alta confiança e encontrou as duas funções de entidade. 

> [!div class="nextstepaction"]
> [Aprenda as melhores práticas para aplicações LUIS](luis-concept-best-practices.md)
