---
title: Onde e como usar - Personalizer
description: O personalizer pode ser aplicado em qualquer situação em que a sua aplicação possa selecionar o item, ação ou produto certo para exibir - de forma a melhorar a experiência, obter melhores resultados de negócio, ou melhorar a produtividade.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: c562d7a1853736204a7a03262547e083bd85fb75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81617676"
---
# <a name="where-and-how-to-use-personalizer"></a>Onde e como usar o Personalizer

Utilize o Personalizer em qualquer situação em que a sua aplicação precise de selecionar a ação correta (conteúdo) para exibir - de forma a melhorar a experiência, obter melhores resultados de negócio ou melhorar a produtividade.

O Personalizer utiliza a aprendizagem de reforço para selecionar que ação (conteúdo) mostrar ao utilizador. A seleção pode variar drasticamente dependendo da quantidade, qualidade e distribuição dos dados enviados ao serviço.

## <a name="example-use-cases-for-personalizer"></a>Exemplo de casos de utilização para Personalizer

* **Esclarecimento de intenção & desambiguação**: ajude os seus utilizadores a ter uma melhor experiência quando a sua intenção não é clara, fornecendo uma opção personalizada.
* **Sugestões predefinidas** para menus & opções: pedir ao bot sugerir o item mais provável de forma personalizada como um primeiro passo, em vez de apresentar um menu impessoal ou lista de alternativas.
* **Os traços de bot & tom:** para bots que podem variar tom, verbosidade e estilo de escrita, considerar variar estes traços.
* **Notificação & conteúdo de alerta**: decida que texto utilizar para alertas para envolver mais os utilizadores.
* **Notificação & tempo**de alerta : tenha aprendido personalizadamente quando enviar notificações aos utilizadores para que os envolvam mais.


## <a name="expectations-required-to-use-personalizer"></a>Expectativas necessárias para usar o Personalizer

Pode aplicar o Personalizer em situações em que se reúna ou possa implementar as seguintes diretrizes.

|Diretriz|Explicação|
|--|--|
|Objetivo de negócio|Tem um objetivo de negócio ou usabilidade para a sua candidatura.|
|Conteúdo|Tem um lugar na sua aplicação onde tomar uma decisão contextual do que mostrar aos utilizadores irá melhorar esse objetivo.|
|Quantidade de conteúdo|Tem menos de 50 ações para classificar por chamada.|
|Dados agregados|A melhor escolha pode e deve ser aprendida com o comportamento do utilizador coletivo e a pontuação total da recompensa.|
|Uso ético|O uso de machine learning para personalização segue as diretrizes de [utilização responsável](ethics-responsible-use.md) e as escolhas que escolheu.
|Melhor opção individual|A decisão contextual pode ser expressa como classificando a melhor opção (ação) de um conjunto limitado de escolhas.|
|Resultado marcado|O quão bem a escolha classificada funcionou para a sua aplicação pode ser determinada medindo algum aspeto do comportamento do utilizador, e expressando-o numa _[pontuação](concept-rewards.md)_ de recompensa .|
|Timing relevante|A pontuação da recompensa não traz muitos fatores confusos ou externos. A duração da experiência é baixa o suficiente para que a pontuação da recompensa possa ser calculada enquanto ainda é relevante.|
|Características de contexto suficientes|Pode expressar o contexto para a classificação como uma lista de pelo menos 5 [funcionalidades](concepts-features.md) que pensa que ajudariam a fazer a escolha certa, e isso não inclui informações identificáveis específicas do utilizador.|
|Recursos de ação suficientes|Você tem informações sobre cada escolha de conteúdo, _ação_, como uma lista de pelo menos 5 [funcionalidades](concepts-features.md) que você acha que ajudarão personalizer a fazer a escolha certa.|
|Dados diários|Há eventos suficientes para ficar em cima da personalização ideal se o problema deriva risa ao longo do tempo (como preferências em notícias ou moda). O personalizer adaptar-se-á à mudança contínua no mundo real, mas os resultados não serão os melhores se não houver eventos e dados suficientes para aprender a descobrir e resolver novos padrões. Deve escolher um caso de uso que acontece com frequência. Considere procurar casos de uso que ocorram pelo menos 500 vezes por dia.|
|Dados históricos|A sua aplicação pode reter dados por tempo suficiente para acumular um histórico de pelo menos 100.000 interações. Isto permite ao Personalizer recolher dados suficientes para realizar avaliações offline e otimização de políticas.|

**Não use personalizer** onde o comportamento personalizado não é algo que possa ser descoberto em todos os utilizadores. Por exemplo, usar o Personalizer para sugerir uma primeira encomenda de pizza de uma lista de 20 itens de menu possíveis é útil, mas que o contacto para ligar da lista de contactos dos utilizadores ao necessitar de ajuda com cuidados infantis (como "Avó") não é algo personalizável em toda a sua base de utilizadores.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Como usar o Personalizer numa aplicação web

A adição de um ciclo de aprendizagem a uma aplicação web inclui:

* Determine qual a experiência para personalizar, que ações e funcionalidades você tem, que contexto apresenta, e que recompensa você vai definir.
* Adicione uma referência ao SDK de Personalização na sua aplicação.
* Ligue para a API de grau quando estiver pronto para personalizar.
* Guarde o eventoId. Enviauma recompensa com a API recompensa mais tarde.
1. Ligue para o evento assim que tiver a certeza de que o utilizador viu a sua página personalizada.
1. Aguarde a seleção do utilizador de conteúdos classificados.
1. Ligue para a API reward para especificar o quão bem a saída da API rank fez.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Como usar o Personalizer com um chat bot

Neste exemplo, você verá como usar personalização para fazer uma sugestão padrão em vez de enviar o utilizador para uma série de menus ou escolhas cada vez.

* Pegue o [código](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) para esta amostra.
* Configura risa a solução de bot. Certifique-se de publicar a sua aplicação LUIS.
* Gerir chamadas de Rank e Reward API para bot.
    * Adicione código para gerir o processamento de intenções luis. Se o **None** for devolvido como a principal intenção ou a pontuação da intenção superior estiver abaixo do limiar da sua lógica de negócio, envie a lista de intenções para personalizar para classificar as intenções.
    * Mostre a lista de intenções ao utilizador como links selecionáveis, sendo a primeira intenção a intenção mais bem classificada da resposta Rank API.
    * Capture a seleção do utilizador e envie-a na chamada Reward API.

### <a name="recommended-bot-patterns"></a>Padrões de bot recomendados

* Faça chamadas de API de Classificação Personalizada sempre que é necessária uma desambiguação, em oposição aos resultados de cache para cada utilizador. O resultado da intenção desambiguante pode mudar ao longo do tempo para uma pessoa, e permitir que a API de rank explore variações irá acelerar a aprendizagem geral.
* Escolha uma interação que seja comum com muitos utilizadores para que tenha dados suficientes para personalizar. Por exemplo, as questões introdutórias podem ser melhores do que pequenos esclarecimentos no fundo do gráfico de conversação que apenas alguns utilizadores podem chegar.
* Use chamadas API de Rank para ativar conversas "a primeira sugestão está certa", onde o utilizador é questionado "Gostaria de X?" ou "Quis dizer X?" e o utilizador pode apenas confirmar; em oposição a dar opções ao utilizador onde deve escolher a partir de um menu. Por exemplo, Utilizador: "Gostaria de pedir um café" Bot: "Gostaria de um expresso duplo?". Desta forma, o sinal de recompensa também é forte, uma vez que diz respeito diretamente a uma sugestão.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Como utilizar o Personalizer com uma solução de recomendação

Muitas empresas usam motores de recomendação, ferramentas de marketing e campanha, segmentação de audiências e clustering, filtragem colaborativa, e outros meios para recomendar produtos de um grande catálogo para clientes.

O [repositório Microsoft Recommenders GitHub](https://github.com/Microsoft/Recommenders) fornece exemplos e boas práticas para a construção de sistemas de recomendação, fornecidos como cadernos Jupyter. Fornece exemplos de trabalho para a elaboração de dados, modelos de construção, avaliação, afinação e operacionalização dos motores de recomendação, para muitas abordagens comuns, incluindo xDeepFM, SAR, ALS, RBM, DKN.

O personalizer pode trabalhar com um motor de recomendação quando está presente.

* Os motores de recomendação tomam grandes quantidades de itens (por exemplo, 500.000) e recomendam um subconjunto (como o top 20) de centenas ou milhares de opções.
* O Personalizer toma um pequeno número de ações com muita informação sobre eles e classifica-as em tempo real para um determinado contexto rico, enquanto a maioria dos motores de recomendação usam apenas alguns atributos sobre utilizadores, produtos e suas interações.
* O Personalizer é projetado para explorar de forma autónoma as preferências dos utilizadores a toda a hora, o que produzirá melhores resultados onde o conteúdo está a mudar rapidamente, como notícias, eventos ao vivo, conteúdo comunitário ao vivo, conteúdo com atualizações diárias ou conteúdo sazonal.

Um uso comum é pegar na saída de um motor de recomendação (por exemplo, os 20 melhores produtos para um determinado cliente) e usá-lo como as ações de entrada para personalizar.

## <a name="adding-content-safeguards-to-your-application"></a>Adicionar salvaguardas de conteúdo à sua aplicação

Se a sua aplicação permitir grandes variações de conteúdo mostradoaos utilizadores, e alguns desses conteúdos podem ser inseguros ou inadequados para alguns utilizadores, deve planear com antecedência para garantir que as salvaguardas certas estão em vigor para evitar que os seus utilizadores vejam conteúdos inaceitáveis. O melhor padrão para implementar salvaguardas é:
    * Obtenha a lista de ações para classificar.
    * Filtrar aqueles que não são viáveis para o público.
    * Apenas classificar estas ações viáveis.
    * Mostrar a ação mais bem classificada para o utilizador.

Em algumas arquiteturas, a sequência acima pode ser difícil de implementar. Nesse caso, existe uma abordagem alternativa para a aplicação das salvaguardas após o ranking, mas é necessário prever que as ações que se avizinham fora da salvaguarda não sejam utilizadas para treinar o modelo Personalizer.

* Obtenha a lista de ações a classificar, com a aprendizagem desativada.
* Ações de classificação.
* Verifique se a ação de topo é viável.
    * Se a ação de topo for viável, ative a aprendizagem para este posto e mostre-a ao utilizador.
    * Se a ação de topo não for viável, não ative a aprendizagem para este ranking e decida através da sua própria lógica ou abordagens alternativas o que mostrar ao utilizador. Mesmo que utilize a segunda opção mais bem classificada, não ative a aprendizagem para este ranking.


## <a name="next-steps"></a>Passos seguintes

[Ética & utilização responsável.](ethics-responsible-use.md)
