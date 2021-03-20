---
title: Onde e como usar - Personalizante
description: O personalização pode ser aplicado em qualquer situação em que a sua aplicação possa selecionar o item, ação ou produto certo para exibir - de forma a melhorar a experiência, alcançar melhores resultados de negócio ou melhorar a produtividade.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777229"
---
# <a name="where-and-how-to-use-personalizer"></a>Onde e como usar o Personalizar

Use o Personalizar em qualquer situação em que a sua aplicação precise de selecionar a ação correta (conteúdo) para exibir - de forma a melhorar a experiência, alcançar melhores resultados de negócio ou melhorar a produtividade.

O personalização utiliza a aprendizagem de reforço para selecionar que ação (conteúdo) para mostrar ao utilizador. A seleção pode variar drasticamente dependendo da quantidade, qualidade e distribuição dos dados enviados para o serviço.

## <a name="example-use-cases-for-personalizer"></a>Exemplo usar casos para Personalizar

* **Esclarecimento intenção & desambiguação**: ajude os seus utilizadores a ter uma melhor experiência quando a sua intenção não é clara, fornecendo uma opção personalizada.
* **Sugestões padrão** para menus & opções: ter o bot sugerir o item mais provável de uma forma personalizada como um primeiro passo, em vez de apresentar um menu impessoal ou lista de alternativas.
* **Traços bot & tom**: para bots que podem variar tom, verbosidade e estilo de escrita, considere variar estes traços.
* **Notificação & conteúdo de alerta:** decida qual o texto a utilizar para alertas para envolver mais os utilizadores.
* **Notificação & tempo** de alerta: tenha uma aprendizagem personalizada de quando enviar notificações aos utilizadores para os envolver mais.


## <a name="expectations-required-to-use-personalizer"></a>Expectativas necessárias para usar o Personalizador

Pode aplicar Personalizer em situações em que se encontre ou possa implementar as seguintes diretrizes.

|Diretriz|Explicação|
|--|--|
|Objetivo de negócio|Tem um objetivo de negócio ou usabilidade para a sua aplicação.|
|Conteúdo|Você tem um lugar na sua aplicação onde tomar uma decisão contextual do que mostrar aos utilizadores vai melhorar esse objetivo.|
|Quantidade de conteúdo|Tem menos de 50 ações para classificar por chamada.|
|Dados agregados|A melhor escolha pode e deve ser aprendida com o comportamento coletivo do utilizador e a pontuação total da recompensa.|
|Uso ético|O uso de machine learning para personalização segue [as diretrizes](ethics-responsible-use.md) e escolhas de uso responsável que escolheu.
|Melhor opção única|A decisão contextual pode ser expressa como classificando a melhor opção (ação) de um conjunto limitado de escolhas.|
|Resultado pontuado|O quão bem a escolha classificada foi trabalhada para a sua aplicação pode ser determinada medindo algum aspeto do comportamento do utilizador, e expressando-a numa _[pontuação de recompensa.](concept-rewards.md)_|
|Tempo relevante|A pontuação da recompensa não traz muitos fatores confusos ou externos. A duração da experiência é baixa o suficiente para que a pontuação da recompensa possa ser calculada enquanto ainda é relevante.|
|Características de contexto suficientes|Pode expressar o contexto para o ranking como uma lista de pelo menos 5 [funcionalidades](concepts-features.md) que você acha que ajudaria a fazer a escolha certa, e isso não inclui informações identificáveis específicas do utilizador.|
|Características de ação suficientes|Você tem informações sobre cada escolha de conteúdo, _ação,_ como uma lista de pelo menos 5 [funcionalidades](concepts-features.md) que você acha que ajudarão Personalizer a fazer a escolha certa.|
|Dados diários|Há eventos suficientes para se manter no topo da personalização ideal se o problema se desvie ao longo do tempo (como preferências em notícias ou moda). O personalização irá adaptar-se à mudança contínua no mundo real, mas os resultados não serão ótimos se não houver eventos e dados suficientes para aprender a descobrir e resolver novos padrões. Deve escolher um caso de uso que acontece com frequência. Considere procurar casos de uso que acontecem pelo menos 500 vezes por dia.|
|Dados históricos|A sua aplicação pode reter dados durante tempo suficiente para acumular um histórico de pelo menos 100.000 interações. Isto permite ao Personaler recolher dados suficientes para realizar avaliações offline e otimização de políticas.|

**Não use o Personalizer** onde o comportamento personalizado não é algo que possa ser descoberto em todos os utilizadores. Por exemplo, usar o Personalizer para sugerir uma primeira encomenda de pizza a partir de uma lista de 20 itens de menu possíveis é útil, mas qual o contacto a ligar da lista de contactos dos utilizadores quando se necessita de ajuda com cuidados infantis (como "Avó") não é algo que seja personalizável em toda a sua base de utilizadores.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Como usar o Personalizar numa aplicação web

A adição de um ciclo de aprendizagem a uma aplicação web inclui:

* Determine qual a experiência para personalizar, que ações e funcionalidades tem, que contexto usar e qual a recompensa que irá definir.
* Adicione uma referência ao SDK de Personalização na sua aplicação.
* Ligue para a API de classificação quando estiver pronto para personalizar.
* Guarde o eventoId. Mais tarde, envia-se uma recompensa com a API reward.
1. Ligue para Ativar o evento assim que tiver a certeza de que o utilizador viu a sua página personalizada.
1. Aguarde a seleção do utilizador dos conteúdos classificados.
1. Ligue para a API reward para especificar o quão bem a saída da API rank fez.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Como usar personalizer com um chat bot

Neste exemplo, verá como usar a Personalização para fazer uma sugestão padrão em vez de enviar o utilizador para uma série de menus ou escolhas de cada vez.

* Pegue o [código](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) para esta amostra.
* Estabeleça a sua solução bot. Certifique-se de publicar a sua aplicação LUIS.
* Gerir Rank e Reward API chama para bot.
    * Adicione código para gerir o processamento de intenções LUIS. Se o **None** for devolvido como a intenção superior ou a pontuação da intenção superior estiver abaixo do limiar da sua lógica de negócio, envie a lista de intenções para Personalizar para Classificar as intenções.
    * Mostrar lista de intenções para o utilizador como links selecionáveis com a primeira intenção de ser a intenção mais bem classificada da resposta da Rank API.
    * Capture a seleção do utilizador e envie-a na chamada Reward API.

### <a name="recommended-bot-patterns"></a>Padrões de bot recomendados

* Faça chamadas de API de ranking personalizador sempre que for necessária uma desambiguação, em oposição aos resultados de caching para cada utilizador. O resultado da intenção desambiguar pode mudar ao longo do tempo para uma pessoa, e permitir que a API rank explore variações acelerará a aprendizagem geral.
* Escolha uma interação que seja comum com muitos utilizadores para que tenha dados suficientes para personalizar. Por exemplo, as questões introdutórias podem ser mais adequadas do que pequenos esclarecimentos profundos no gráfico de conversação que apenas alguns utilizadores podem chegar.
* Use chamadas API rank para ativar conversas "a primeira sugestão é certa", onde o utilizador é questionado "Você gostaria de X?" ou "Quis dizer X?" e o utilizador pode apenas confirmar; em oposição a dar opções ao utilizador onde deve escolher entre um menu. Por exemplo, Utilizador: "Gostaria de pedir um café" Bot:"Gostaria de um expresso duplo?". Desta forma, o sinal de recompensa também é forte, uma vez que diz respeito diretamente a uma sugestão.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Como utilizar o Personalizar com uma solução de recomendação

Muitas empresas usam motores de recomendação, ferramentas de marketing e campanha, segmentação de audiências e clustering, filtragem colaborativa, e outros meios para recomendar produtos de um grande catálogo para os clientes.

O [repositório GitHub da Microsoft Recommenders](https://github.com/Microsoft/Recommenders) fornece exemplos e boas práticas para a construção de sistemas de recomendação, fornecidos como cadernos Jupyter. Fornece exemplos de trabalho para a preparação de dados, modelos de construção, avaliação, afinação e operacionalização dos motores de recomendação, para muitas abordagens comuns, incluindo xDeepFM, SAR, ALS, RBM, DKN.

O personalização pode trabalhar com um motor de recomendação quando está presente.

* Os motores de recomendação tomam grandes quantidades de itens (por exemplo, 500.000) e recomendam um subconjunto (como o top 20) de centenas ou milhares de opções.
* O personalizador pega num pequeno número de ações com muita informação sobre elas e classifica-as em tempo real para um determinado contexto rico, enquanto a maioria dos motores de recomendação usam apenas alguns atributos sobre utilizadores, produtos e suas interações.
* O personalização é projetado para explorar autonomamente as preferências dos utilizadores a toda a hora, o que dará melhores resultados onde os conteúdos estão a mudar rapidamente, como notícias, eventos ao vivo, conteúdo sonoro, conteúdo com atualizações diárias ou conteúdo sazonal.

Um uso comum é pegar na saída de um motor de recomendação (por exemplo, os 20 melhores produtos para um determinado cliente) e usá-lo como ações de entrada para Personaler.

## <a name="adding-content-safeguards-to-your-application"></a>Adicionar salvaguardas de conteúdo à sua aplicação

Se a sua aplicação permitir grandes variações nos conteúdos mostrados aos utilizadores, e alguns desses conteúdos podem ser inseguros ou inadequados para alguns utilizadores, deve planear com antecedência que as salvaguardas certas estão em vigor para evitar que os seus utilizadores vejam conteúdo inaceitável. O melhor padrão para implementar salvaguardas é:
    * Obtenha a lista de ações a classificar.
    * Filtre os que não são viáveis para o público.
    * Apenas classificar estas ações viáveis.
    * Exiba a ação mais bem classificada para o utilizador.

Em algumas arquiteturas, a sequência acima pode ser difícil de implementar. Nesse caso, existe uma abordagem alternativa para implementar salvaguardas após o ranking, mas é necessário prever que as ações que se encontram fora da salvaguarda não sejam utilizadas para formar o modelo Personaler.

* Obtenha a lista de ações a classificar, com a aprendizagem desativada.
* Ações de classificação.
* Verifique se a ação superior é viável.
    * Se a ação de topo for viável, ative a aprendizagem para este posto e, em seguida, mostrá-la ao utilizador.
    * Se a ação de topo não for viável, não ative a aprendizagem para este ranking, e decida através da sua própria lógica ou abordagens alternativas o que mostrar ao utilizador. Mesmo que utilize a segunda opção mais bem classificada, não ative a aprendizagem para este ranking.


## <a name="next-steps"></a>Passos seguintes

[Ética & uso responsável](ethics-responsible-use.md).
