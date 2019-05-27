---
title: O que é a Conversa de Personalidade do Projeto?
titlesuffix: Azure Cognitive Services
description: Este artigo é uma descrição geral do Azure Project Personality Chat, uma API com base na cloud para melhorar as capacidades de conversação do seu bot.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "62103630"
---
# <a name="what-is-project-personality-chat"></a>O que é a Conversa de Personalidade do Projeto?

O Project Personality Chat melhora as capacidades de conversação do seu bot ao lidar com conversas simples de acordo com uma personalidade distinta escolhida. O Personality Chat utiliza classificadores de intenções para identificar intenções de conversas simples comuns e gera respostas consistentes com uma personalidade. Com base na intenção e nas pontuações de confiança, o bot escolhe a melhor resposta de entre uma base editorial organizada ou gera uma resposta em tempo real através de redes neurais profundas (DNNs). Pode escolher uma de três pessoas fictícias predefinidas. O modelo de pessoa fictícia devolve as respostas mais semelhantes à personalidade escolhida.

Está disponível um conjunto editorial personalizável para consultas de conversa simples. Pode ser facilmente integrado no Microsoft Bot Framework SDK. Este SDK poupa-lhe tempo e o custo de escrevê-los de raiz.

## <a name="getting-started-with-project-personality-chat"></a>Introdução ao Project Personality Chat

Pode visitar a página de laboratórios do Project Personality Chat e conversar com a demonstração disponível, além de pedir acesso antecipado quando o serviço estiver disponível.
Hoje em dia, também pode integrar a biblioteca apenas editorial personalizável nos seus bots através do Microsoft Bot Framework SDK. <br>
[Exemplos: Integrar a personalidade bate-papo de mensagens em fila um bot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Experimentar a biblioteca do Personality Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Gerar respostas através de redes neurais

O Personality Chat utiliza modelos de aprendizagem aprofundada para aprender padrões de conversa gerais e gerar respostas. O modelo de geração de respostas é uma rede neural recorrente (RNN). Este modelo é preparado com base em milhões de conversações, onde compreende e aprende padrões de interações humanas. Com os padrões de estruturas de frases aprendidos, são formadas novas consultas e são geradas respostas. Ao gerar esta nova resposta, o modelo pesquisa um "vocabulário escrito" de palavras e escolhe as primeiras X melhores palavras para a resposta. Através da pesquisa, continua a procurar as segundas X melhores palavras para cada uma das primeiras palavras geradas. Uma resposta será considerada concluída quando o modelo escolher a palavra de "Fim de frase" (EOS). Depois de ter todas as respostas, escolhe as X melhores respostas com base na pontuação de probabilidade da resposta completa.

O modelo aprende a gerar opções contextualmente apropriadas com a "estrutura" certa. Por exemplo, uma pergunta como "Quer falar agora?" diz bastante sobre a estrutura de uma resposta plausível: provavelmente irá começar ao parafrasear com "sim" ou "não" e um pronome seria provavelmente "Eu". É mais provável que uma resposta "não" inclua uma explicação educada e assim sucessivamente.

O sistema tenta aprender um modelo de linguagem para a estrutura básica de conversa. Esta estrutura deve permitir que as respostas sejam influenciadas em parte por restrições externas como tópicos, personalidade e assim sucessivamente.  Uma vez que estas restrições são aprendidas a partir de padrões abrangentes, são adequadas (mas não limitadas) para a aplicação de conversa simples.

![Sequência do modelo para geração de respostas](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modelação da personalidade

 Com qualquer modelo conversacional condicionado por dados, existe o desafio de uma "personalidade" coerente de forma consistente. O conceito de PESSOA FICTÍCIA é definido como a personagem representada durante as interações de conversação. Pode considerar-se que uma pessoa fictícia é constituída por elementos de identidade, comportamento de linguagem e estilo de interação. Na versão atual do Personality Chat, o foco está no comportamento de linguagem e no estilo de interação.

Este modelo representa cada orador individual como vetor ou incorporação. Codifica as informações a partir do orador que influencia os conteúdos e o estilo das suas respostas. Em seguida, o modelo aprende as representações do orador com base nos conteúdos conversacionais fornecidos por diferentes oradores. Os oradores com respostas semelhantes tendem a ter incorporações semelhantes ao ocuparem as posições mais próximas no espaço de vetor. Desta forma, os dados de preparação de oradores mais próximos no espaço de vetor ajudam a aumentar a capacidade de generalização do modelo de orador. O modelo cria clusters para os oradores que têm representações semelhantes no espaço de vetor para formar um cluster de pessoa fictícia, com um "ID de pessoa fictícia".

Para as pessoas fictícias predefinidas, os atributos e as respostas organizadas são utilizadas para encontrar o cluster mais próximo do orador correspondente. Em seguida, este cluster é escolhido como ID de Pessoa Fictícia para cada uma das personalidades predefinidas. A personalização contínua pode ocorrer para qualquer tipo de personalidade através de um conjunto de respostas de bot/marca. As conversações são feitas de forma a emular com precisão a pessoa fictícia individual, como o comportamento de resposta linguística e outras características principais.

![Modelação de pessoas fictícias com clusters de oradores](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Compreensão da intenção de conversa simples

O Personality Chat tem classificadores de intenções para assegurar uma resposta para intenções de conversa simples. Estes classificadores não afetam as tarefas nem as consultas de informações. Um classificador de chat de nível geral determina se a consulta é uma intenção de conversa simples ou de conversa. Decide com base em classificadores de léxico e semântica e ao combinar as respetivas pontuações. Estas intenções são preparadas com dados conversacionais (como exemplos de intenções positivas) e consultas de pesquisa/tarefas (para exemplos de intenções negativas).

São utilizados outros classificadores de subintenções para identificar as subclasses de conversa simples para restringir os tipos de conversa simples a que o serviço responde, por exemplo: saudações, cumprimentos, opiniões e assim sucessivamente. Estes classificadores de aprendizagem aprofundada convertem todas as consultas em vetores através do Deep Structure Semantic Model (CDSSM). São preparados com dezenas de milhares de consultas organizadas para as subintenções. Em seguida, o classificador corresponde uma consulta com as classes de intenções identificadas existentes ao localizar a correspondência mais próxima no espaço de vetor.

Foi criada uma série de controlos para ajudar a evitar respostas desfavoráveis com base nos conhecimentos de agentes inteligentes, como o Zo. Por predefinição, o Personality Chat está definido para responder apenas às intenções reconhecidas do utilizador. Pode querer testar se o Project Personality Chat é adequado para as suas circunstâncias. Os seus comentários são importantes se vir algo que precise de mais preparação.
