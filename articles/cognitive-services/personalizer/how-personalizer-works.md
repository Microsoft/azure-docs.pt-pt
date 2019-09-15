---
title: Como o personalizador funciona – personalizador
titleSuffix: Azure Cognitive Services
description: O personalizador usa o aprendizado de máquina para descobrir a ação a ser usada em um contexto. Cada loop de aprendizagem tem um modelo que é treinado exclusivamente em dados que você enviou a ele por meio de chamadas de classificação e recompensa. Cada loop de aprendizado é completamente independente um do outro.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.openlocfilehash: 7c163dacae24749dbe309bca33bac016a3be7aa5
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002893"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O personalizador usa o aprendizado de máquina para descobrir a ação a ser usada em um contexto. Cada loop de aprendizagem tem um modelo que é treinado exclusivamente em dados que você enviou a ele por meio de chamadas de **classificação** e **recompensa** . Cada loop de aprendizado é completamente independente um do outro. Crie um loop de aprendizagem para cada parte ou comportamento do seu aplicativo que você deseja personalizar.

Para cada loop, **chame a API de classificação com** base no contexto atual, com:

* Lista de ações possíveis: itens de conteúdo dos quais selecionar a ação superior.
* Lista de [recursos de contexto](concepts-features.md): dados contextualmente relevantes, como usuário, conteúdo e contexto.

A API de **classificação** decide usar:

* _Exploração_: O modelo atual para decidir a melhor ação com base nos dados anteriores.
* _Explorar_: Selecione uma ação diferente em vez da ação superior.

A API de **recompensa** :

* Coleta dados para treinar o modelo registrando os recursos e recompensando pontuações de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base nas configurações especificadas na _política de aprendizado_.

## <a name="architecture"></a>Arquitetura

A imagem a seguir mostra o fluxo arquitetônico de chamar as chamadas Rank e recompensa:

![texto alt](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a personalização")

1. O personalizador usa um modelo interno de ia para determinar a classificação da ação.
1. O serviço decide se deseja explorar o modelo atual ou explorar novas opções para o modelo.  
1. O resultado da classificação é enviado para o EventHub.
1. Quando o personalizador recebe a recompensa, o prêmio é enviado para o EventHub. 
1. A classificação e a recompensa estão correlacionadas.
1. O modelo de ia é atualizado com base nos resultados de correlação.
1. O mecanismo de inferência é atualizado com o novo modelo. 

## <a name="research-behind-personalizer"></a>Pesquisa por trás do personalizador

O personalizador é baseado em ciência de ponta e pesquisa na área de [aprendizado de reforço](concepts-reinforcement-learning.md) , incluindo documentos, atividades de pesquisa e áreas contínuas de exploração no Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Loop de aprendizagem**: Você pode criar um loop de aprendizagem para cada parte do seu aplicativo que pode se beneficiar da personalização. Se você tiver mais de uma experiência para personalizar, crie um loop para cada uma delas. 

* **Ações**: As ações são itens de conteúdo, como produtos ou promoções, para escolher. O personalizador escolhe a ação principal a ser mostrada aos usuários, conhecida como a _ação de recompensa_, por meio da API de classificação. Cada ação pode ter recursos enviados com a solicitação de classificação.

* **Contexto**: Para fornecer uma classificação mais precisa, forneça informações sobre seu contexto, por exemplo:
    * Seu usuário.
    * O dispositivo no qual estão. 
    * A hora atual.
    * Outros dados sobre a situação atual.
    * Dados históricos sobre o usuário ou contexto.

    Seu aplicativo específico pode ter informações de contexto diferentes. 

* **[Recursos](concepts-features.md)** do: Uma unidade de informações sobre um item de conteúdo ou um contexto de usuário.

* **Recompensa**: Uma medida de como o usuário respondeu à API Rank retornou a ação, como uma pontuação entre 0 e 1. O valor de 0 a 1 é definido pela lógica de negócios, com base em como a escolha ajudou a atingir suas metas de negócios de personalização. 

* **Exploração**: O serviço personalizado está explorando quando, em vez de retornar a melhor ação, ele escolhe uma ação diferente para o usuário. O serviço personalizado evita descompasso, estagnação e pode se adaptar ao comportamento contínuo do usuário explorando. 

* **Duração do experimento**: A quantidade de tempo que o serviço personalizado aguarda para um prêmio, começando do momento em que a chamada de classificação ocorreu para esse evento.

* **Eventos**inativos: Um evento inativo é aquele em que você chamou Rank, mas não tem certeza de que o usuário verá o resultado, devido às decisões do aplicativo cliente. Os eventos inativos permitem que você crie e armazene os resultados da personalização e, em seguida, decida descartá-los mais tarde sem afetar o modelo de aprendizado de máquina.

* **Modelo**: Um modelo personalizado captura todos os dados aprendidos sobre o comportamento do usuário, obtendo dados de treinamento da combinação dos argumentos que você envia para classificar e recompensar chamadas e com um comportamento de treinamento determinado pela política de aprendizado. 

* **Política de aprendizagem**: O modo como o personalizado treinará um modelo em cada evento será determinado por alguns metaparâmetros que afetam o funcionamento dos algoritmos de aprendizado de máquina. Novos loops do personalizador começarão com uma política de aprendizado padrão, que pode produzir um desempenho moderado. Ao executar [avaliações](concepts-offline-evaluation.md), o personalizador pode criar novas políticas de aprendizado especificamente otimizadas para os casos de uso do loop. O personalizador terá um desempenho significativamente melhor com políticas otimizadas para cada loop específico, gerado durante a avaliação.

## <a name="example-use-cases-for-personalizer"></a>Casos de uso de exemplo para personalizador

* Esclarecimento da intenção & Desambigüidade: ajuda os usuários a ter uma experiência melhor quando sua intenção não está clara, fornecendo uma opção que é personalizada para cada usuário.
* Sugestões padrão para menus & opções: faça com que o bot sugira o item mais provável de uma maneira personalizada como uma primeira etapa, em vez de apresentar um menu ou uma lista de alternativas pessoais.
* As características do bot & Tom: para bots que podem variar o estilo de Tom, detalhamento e escrita, considere a possibilidade de diversificar essas características de maneiras personalizadas.
* Notificação & conteúdo do alerta: decida qual texto deve ser usado para alertas a fim de envolver mais usuários.
* Tempo de alerta de & de notificação: tenha aprendizado personalizado de quando enviar notificações aos usuários para que eles se envolvam mais.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Como usar o personalizador em um aplicativo Web

Adicionar um loop a um aplicativo Web inclui:

* Determine qual experiência deve ser individualizada, quais ações e recursos você tem, quais recursos de contexto usar e qual recompensa você definirá.
* Adicione uma referência ao SDK de personalização em seu aplicativo.
* Chame a API de classificação quando estiver pronto para personalizar.
* Armazene o eventId. Você envia uma recompensa com a API de recompensa mais tarde.
1. Chame ativar para o evento quando tiver certeza de que o usuário viu sua página personalizada.
1. Aguarde a seleção de conteúdo classificada pelo usuário. 
1. Chame a API de recompensa para especificar o quão bem a saída da API de classificação.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Como usar o personalizador com um bot de chat

Neste exemplo, você verá como usar a personalização para fazer uma sugestão padrão em vez de enviar o usuário uma série de menus ou opções a cada vez.

* Obtenha o [código](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) para este exemplo.
* Configure sua solução de bot. Certifique-se de publicar seu aplicativo LUIS. 
* Gerenciar classificação e recompensar chamadas de API para bot.
    * Adicione código para gerenciar o processamento de intenção LUIS. Se **nenhum** for retornado como a principal intenção ou a pontuação da intenção principal estiver abaixo do limite da lógica de negócios, envie a lista de tentativas para personalizar para classificar as intenções.
    * Mostrar a lista de intenções ao usuário como links selecionáveis com a primeira intenção sendo a intenção de classificação mais alta da resposta da API de classificação.
    * Capture a seleção do usuário e envie-a na chamada à API de recompensa. 

### <a name="recommended-bot-patterns"></a>Padrões de bot recomendados

* Faça chamadas da API de classificação do personalizado sempre que uma desambiguidade for necessária, em vez de armazenar em cache os resultados de cada usuário. O resultado da intenção de ambiguidade pode mudar ao longo do tempo para uma pessoa, e permitir que a API de classificação Explore as variações acelerará o aprendizado geral.
* Escolha uma interação que seja comum com muitos usuários para que você tenha dados suficientes para personalizar. Por exemplo, as perguntas introdutórias podem ser mais bem ajustadas do que os esclarecimentos mais amplos no grafo de conversa que apenas alguns usuários podem chegar.
* Use chamadas à API de classificação para habilitar as conversas "primeira sugestão é certa", em que o usuário recebe uma pergunta "você gostaria de X?" ou "você quis dizer X?" e o usuário pode apenas confirmar; em vez de fornecer opções para o usuário onde eles devem escolher um menu. Por exemplo, usuário: "gostaria de pedir um" bot "de café:" você gostaria de um expresso duplo? ". Dessa forma, o sinal de recompensa também é forte, pois ele se refere diretamente a uma sugestão.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Como usar o personalizador com uma solução de recomendação

Use seu mecanismo de recomendação para filtrar um catálogo grande em alguns itens que podem ser apresentados como 30 ações possíveis enviadas para a API de classificação.

Você pode usar os mecanismos de recomendação com o personalizador:

* Configure a [solução de recomendação](https://github.com/Microsoft/Recommenders/). 
* Ao exibir uma página, invoque o modelo de recomendação para obter uma lista curta de recomendações.
* Chame a personalização para classificar a saída da solução de recomendação.
* Envie comentários sobre a ação do usuário com a chamada à API de recompensa.


## <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

* Não use o personalizador onde o comportamento personalizado não é algo que possa ser descoberto em todos os usuários, mas sim algo que deve ser lembrado para usuários específicos ou proveniente de uma lista de alternativas específicas do usuário. Por exemplo, usar o personalizador para sugerir uma primeira ordem de pizza de uma lista de 20 itens de menu possíveis é útil, mas qual contato chamar da lista de contatos dos usuários ao exigir ajuda com Childcare (como "avó") não é algo que seja personalizável em sua base de usuários.


## <a name="adding-content-safeguards-to-your-application"></a>Adicionando proteções de conteúdo ao seu aplicativo

Se o seu aplicativo permitir grandes variações no conteúdo mostrado aos usuários, e algum desses conteúdos pode não ser seguro ou inadequado para alguns usuários, você deve planejar com antecedência para garantir que as proteções certas estejam em vigor para impedir que os usuários vejam inaceitáveis disputa. O melhor padrão para implementar proteções é:
    * Obtenha a lista de ações a serem classificadas.
    * Filtre aqueles que não são viáveis para o público.
    * Classifique somente essas ações viáveis.
    * Exibe a ação mais classificada para o usuário.

Em algumas arquiteturas, a sequência acima pode ser difícil de implementar. Nesse caso, há uma abordagem alternativa para implementar proteções após a classificação, mas um provisionamento precisa ser feito para que as ações que ficarem fora da proteção não sejam usadas para treinar o modelo personalizado.

* Obter a lista de ações a serem classificadas, com o aprendizado desativado.
* Ações de classificação.
* Verifique se a ação principal é viável.
    * Se a ação principal for viável, ative o Learning para essa classificação e, em seguida, mostre-a para o usuário.
    * Se a ação principal não for viável, não ative o aprendizado para essa classificação e decida por sua própria lógica ou abordagens alternativas o que mostrar ao usuário. Mesmo que você use a segunda opção com a melhor classificação, não ative o aprendizado para essa classificação.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Verificando a eficácia adequada do personalizador

Você pode monitorar a eficácia do Personalizar periodicamente executando [avaliações offline](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Passos seguintes

Entenda [onde você pode usar](where-can-you-use-personalizer.md)o personalizador.
Executar [avaliações offline](how-to-offline-evaluation.md)
