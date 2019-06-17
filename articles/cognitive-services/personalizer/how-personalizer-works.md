---
title: Como funciona o Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer utiliza machine learning para descobrir qual a ação a utilizar um contexto. Cada loop learning tem um modelo que está preparado exclusivamente nos dados que enviou a ele por meio de classificação e chama de recompensa. Cada loop de aprendizado é completamente independente umas das outras.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: d0a0a6101d876493188426d19f2fa845d20ee274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055486"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

Personalizer utiliza machine learning para descobrir qual a ação a utilizar um contexto. Cada loop de aprendizado tem um modelo que está preparado exclusivamente nos dados que enviou a ele via **classificação** e **recompensa** chamadas. Cada loop de aprendizado é completamente independente umas das outras. Crie um loop de aprendizagem para cada parte ou o comportamento da sua aplicação que pretende personalizar.

Para cada loop **chamar a API de classificação com** com base no contexto atual, com:

* Lista de ações possíveis: itens a partir do qual pode selecionar a ação de principal de conteúdo.
* Lista de [funcionalidades do contexto](concepts-features.md): dados relevantes contextualmente como usuário, o conteúdo e o contexto.

O **classificação** decide a utilização de uma API:

* _Explorar_: O modelo atual para decidir a melhor ação com base nos últimos dados.
* _Explore_: Selecione outra ação em vez da ação superior.

O **recompensa** API:

* Recolhe dados para preparar o modelo gravando as funcionalidades e as pontuações de recompensa de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base nas definições especificadas na _Learning política_.

## <a name="architecture"></a>Arquitetura

A imagem seguinte mostra o fluxo de arquitetura de chamar as chamadas de classificação e a recompensa:

![texto alternativo](./media/how-personalizer-works/personalization-how-it-works.png "como funciona a personalização")

1. Personalizer utiliza um modelo de IA interno para determinar a classificação da ação.
1. O serviço de decide se pretende explorar o modelo atual ou explorar novas escolhas para o modelo.  
1. O resultado de classificação é enviado para EventHub.
1. Quando Personalizer recebe a recompensa, a recompensa é enviada para EventHub. 
1. A classificação e a recompensa são correlacionados.
1. É atualizar o modelo de IA com base nos resultados de correlação.
1. O mecanismo de inferência de tipos é atualizado com o novo modelo. 

## <a name="research-behind-personalizer"></a>Pesquisa por trás Personalizer

Personalizer baseia-se a ciência de vanguarda e investigação na área de [aprendizagem por reforço](concepts-reinforcement-learning.md) incluindo documentos, atividades de pesquisa e as áreas em curso de exploração na Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Ciclo de aprendizagem**: Pode criar um loop de aprendizagem para todas as partes do aplicativo que pode se beneficiar da personalização. Se tiver mais de uma experiência para personalizar, crie um loop para cada um. 

* **Ações**: As ações são os itens de conteúdo, por exemplo, produtos ou promoções, à sua escolha. Personalizer escolhe a ação para mostrar aos seus utilizadores, conhecidos como superior a _recompensar ação_, através da API de classificação. Cada ação pode ter submetidos com o pedido de classificação de recursos.

* **Contexto**: Para fornecer uma classificação mais precisa, fornecem informações sobre seu contexto, por exemplo:
    * O utilizador.
    * O dispositivo estão no. 
    * A hora atual.
    * Outros dados sobre a situação atual.
    * Dados históricos sobre o utilizador ou o contexto.

    Seu aplicativo específico pode ter informações de contexto diferente. 

* **[Funcionalidades](concepts-features.md)** : Uma unidade de informações sobre um item de conteúdo ou a um contexto de usuário.

* **Recompensa**: Uma medida de como o usuário respondesse para a API de classificação devolveu ação, como uma classificação de entre 0 e 1. O valor de 0 a 1 é definido por sua lógica de negócio, com base em como a opção ajudou a atingir suas metas de negócios de personalização. 

* **Exploração**: O serviço de Personalizer está explorando quando, em vez de retornar a melhor ação, ela escolhe uma ação diferente para o utilizador. O serviço de Personalizer evita descompassos, estagnação e pode adaptar ao comportamento do utilizador em curso ao explorar. 

* **Duração experimental**: A quantidade de tempo que o serviço de Personalizer aguarda uma recompensa, a partir do momento em que a chamada de classificação aconteceu para esse evento.

* **Eventos Inativos**: Um evento inativo é um em que é chamado de classificação, mas não tiver a certeza do que usuário nunca verá o resultado, devido a decisões de aplicação de cliente. Eventos Inativos permitem-lhe criar e armazenar os resultados de personalização, em seguida, optar por eliminá-las mais tarde sem afetar o modelo de machine learning.

* **modelo**: Um modelo de Personalizer captura todos os dados, ficou a conhecer o comportamento dos utilizadores, a obtenção de dados de treinamento da combinação dos enviar para chamadas de classificação e a recompensa e com um comportamento de treinamento, determinado pela política de aprendizagem de argumentos. 

## <a name="example-use-cases-for-personalizer"></a>Casos de utilização de exemplo para Personalizer

* Esclarecimento intenção & desambiguação: ajuda os utilizadores têm uma melhor experiência quando o seu objetivo é não desmarque ao fornecer uma opção que é personalizada para cada utilizador.
* Sugestões para menus e as opções de predefinido: tem o bot sugerir o item mais provável de forma personalizada como primeiro passo, em vez de apresentar um impersonal menu ou uma lista de alternativas.
* As características de bot & tom: para bots que pode variar tom verbosidade e estilo de escrita, considere variados estas características de uma formas personalizadas.
* Conteúdo do & alerta de notificação: decidir o texto a utilizar para alertas para interagir com os utilizadores mais.
* & Tempo de alerta de notificação: ter personalizadas de aprendizagem de quando enviar notificações aos utilizadores para contactá-lo mais.

## <a name="checklist-for-applying-personalizer"></a>Lista de verificação para a aplicação de Personalizer

Pode aplicar Personalizer em situações em que:

* Tem uma empresa ou o objetivo de Facilidade de utilização para a sua aplicação.
* Tem um local na sua aplicação em que a tomar uma decisão contextual do que mostrar para os utilizadores irão melhorar esse objetivo.
* A melhor opção pode e deve ser aprendida com a pontuação de recompensa de comportamento e total de utilizador coletiva.
* Segue-se a utilização do machine learning para personalização [diretrizes de utilização responsável](ethics-responsible-use.md) e opções para a sua equipa.
* A decisão pode ser expresso como a classificação a melhor opção ([ação](concepts-features.md#actions-represent-a-list-of-options) de um conjunto limitado de opções.
* Bem, essa opção trabalhada pode ser computada por sua lógica de negócio, por medir algum aspecto do comportamento do usuário e expressá-lo num número entre -1 e 1.
* A pontuação de recompensa não leva em muitos fatores externos ou confusão, especificamente a duração de experimentação é baixa o suficiente para que a pontuação de recompensa pode ser computada, enquanto ainda é relevante.
* Pode expressar o contexto para a classificação como um dicionário de recursos, pelo menos, 5, que acredita que ajudaria a escolha certa e que não inclui informações de identificação pessoal.
* Tem informações sobre cada ação, como um dicionário de atributos, pelo menos, 5 ou recursos que acredita que ajudará a Personalizer escolha certa.
* Pode manter os dados de longa duração suficiente para acumular um histórico de interações, pelo menos, 100 000.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>As considerações para aplicar Personalizer de aprendizagem automática

Personalizer baseia-se por reforço learning, uma abordagem ao machine learning que obtém ensinado por comentários dá-lhe. 

Personalizer aprenderá melhor em situações em que:
* Não existe suficiente eventos para ficar por cima de personalização ideal, se o problema drifts ao longo do tempo (por exemplo, as preferências em notícias ou em forma de mensagens em fila). Personalizer irá adaptar-se a mudança contínua no mundo real, mas os resultados não serão ideais se não houver suficiente eventos e os dados de aprender com a descoberta e se concentrar em novos padrões. Deve escolher um caso de utilização que ocorre com frequência suficiente. Considere busca de casos de utilização de isso acontecer, pelo menos, 500 vezes por dia.
* Contexto e ações tem suficiente recursos para facilitar a aprendizagem.
* Há menos de 50 ações para classificação por chamada.
* As definições de retenção de dados permitem Personalizer recolher dados suficientes para realizar avaliações offline e a otimização de política. Isto é, normalmente, os pontos de dados, pelo menos, 50 000.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Como utilizar Personalizer num aplicativo web

Adicionar um loop para uma aplicação web inclui:

* Determine que experiência para personalizar, quais ações e os recursos têm, quais recursos de contexto para utilizar, e que recompensa definirá.
* Adicione uma referência para o SDK de personalização em seu aplicativo.
* Quando estiver pronto para personalizar, chame a API de classificação.
* Store o eventId. Enviar uma recompensa com a API de recompensa mais tarde.
1. Chame ativar para o evento assim que tiver a certeza do que utilizador teve sua página personalizada.
1. Aguarde pela seleção de utilizador de conteúdo classificada. 
1. Chame a API de recompensa para especificar a eficiência com que a saída da API de classificação fez.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Como utilizar Personalizer com um bot de bate-papo

Neste exemplo, verá como utilizar personalização para fazer uma sugestão de predefinição em vez de enviar o utilizador para baixo de uma série de menus ou opções de cada vez.

* Obter o [código](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) para este exemplo.
* Configure a solução de bot. Certifique-se publicar a sua aplicação LUIS. 
* Gerir a classificação e chamadas à API de recompensa para bot.
    * Adicione código para gerir o processamento de intenção de LUIS. Se o **None** é retornada como a intenção de principal ou de classificação a intenção superior é abaixo o limiar de lógica de negócios, enviar as intenções listam para Personalizer a classificação dos objetivos.
    * Mostra a lista de intenção ao utilizador como ligações selecionáveis com a intenção de primeiro a ser a intenção de classificação alta de mensagens em fila de resposta da API de classificação.
    * Capturar a seleção do usuário e enviar isso na chamada de API de recompensa. 

### <a name="recommended-bot-patterns"></a>Padrões de bot recomendada

* Efetuar chamadas de API de classificação de Personalizer sempre que for necessário uma desambiguação, em vez de colocação em cache os resultados para cada utilizador. O resultado de eliminar a ambiguidade intenção podem ser alterados ao longo do tempo para uma pessoa, e permitindo que a API de classificação explorar as variações irá acelerar a aprendizagem geral.
* Escolha uma interação que é comum com muitos usuários, para que tenha dados suficientes para personalizar. Por exemplo, perguntas introdutórias podem ser melhor ajusta-se que o menores esclarecimentos no gráfico de conversação que apenas a alguns utilizadores podem obter a.
* Utilize chamadas à API de classificação para ativar a "a primeira sugestão é o direito" conversas, onde obtém pedido ao utilizador "Gostaria que X?" ou "quis dizer X?" e o utilizador pode confirmar; em vez de dar opções ao usuário em que têm de escolher um menu. Por exemplo, utilizador: "Eu gostaria de solicitar um café" Bot: "Gostaria que uma dupla espresso?". Dessa forma o sinal de recompensa também é forte porque ela pertence diretamente para uma sugestão.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Como utilizar Personalizer com uma solução de recomendação

Use seu mecanismo de recomendação para filtrar um catálogo de grandes dimensões para alguns itens que, em seguida, pode ser apresentado como 30 ações possíveis enviadas para a API de classificação.

Pode usar os motores de recomendações com Personalizer:

* Configurar o [solução recomendação](https://github.com/Microsoft/Recommenders/). 
* Quando visualizar uma página, invoca o modelo de recomendação para obter uma lista de recomendações.
* Chame a personalização para classificar a saída da solução de recomendação.
* Envie comentários sobre a ação do utilizador com a chamada da API de recompensa.


## <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

* Não utilize Personalizer onde o comportamento personalizado não é algo que possam ser detetados em todos os utilizadores, mas em vez disso, algo que deve ser memorizada para utilizadores específicos, ou é proveniente de uma lista de utilizadores específicos de alternativas. Por exemplo, usando Personalizer sugerir uma ordem de pizza primeiro de uma lista de 20 itens de menu possíveis é útil, mas a lista de contactos que contacto a chamar a partir dos utilizadores, quando precisar de ajuda com childcare (por exemplo, "Vovó") não é algo que é personalizáveis em sua base de usuários.


## <a name="adding-content-safeguards-to-your-application"></a>Adição de garantias de conteúdo à sua aplicação

Se a aplicação permite que as variações de grandes dimensões no conteúdo apresentado aos utilizadores e alguns dos que conteúdo pode ser confiável ou inadequada para alguns usuários, deve planeje com antecedência para se certificar de que as salvaguardas certas estão em vigor para impedir que os utilizadores visualizem inaceitáveis conteúdo. O padrão recomendado para implementar as proteções é: O padrão recomendado para implementar as proteções é:
    * Obter a lista de ações para classificação.
    * Filtre aqueles que não são viáveis para o público-alvo.
    * Classificar apenas essas ações viáveis.
    * Mostrar parte superior com a classificação de ação para o utilizador.

Algumas arquiteturas, poderá ser difíceis de implementar a sequência de acima. Nesse caso, há uma abordagem alternativa para implementar as proteções após a classificação, mas um aprovisionamento tem de ser feitas para que as ações que está fora do salvaguardar não são utilizadas para preparar o modelo de Personalizer.

* Obter a lista de ações para classificação, com aprendizagem desativada.
* Ações de classificação.
* Verificar se a ação superior é viável.
    * Se a ação superior for viável, ativar learning para esta classificação, em seguida, mostrá-lo ao usuário.
    * Se a ação superior não for viável, não ative learning para esta classificação e decidir, por meio de sua própria lógica ou abordagens alternativas, o que mostrar ao usuário. Mesmo que utilize a opção de classificações de segundo melhor, não ative learning para esta classificação.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Verificar a eficácia adequada de Personalizer

Pode monitorizar a eficácia da Personalizer periodicamente executando [avaliações offline](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Passos Seguintes

Compreender [onde pode utilizar Personalizer](where-can-you-use-personalizer.md).