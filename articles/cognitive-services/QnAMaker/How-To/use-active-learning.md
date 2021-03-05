---
title: Use aprendizagem ativa com base de conhecimento - QnA Maker
description: Aprenda a melhorar a qualidade da sua base de conhecimentos com aprendizagem ativa. Reveja, aceite ou rejeite, adicione sem remover ou alterar as questões existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 87dde7662050794a24cf976a0bae6237b91d29b2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213713"
---
# <a name="active-learning"></a>Aprendizagem ativa

A funcionalidade _de sugestões de aprendizagem Ativa_ permite-lhe melhorar a qualidade da sua base de conhecimentos, sugerindo questões alternativas, baseadas em submissões de utilizadores, ao seu par de perguntas e respostas. Reveja essas sugestões, ou adicioná-las às questões existentes ou rejeitá-las.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração produza efeitos, tem de aceitar as sugestões. Estas sugestões adicionam perguntas mas não alteram ou removem as questões existentes.

## <a name="what-is-active-learning"></a>O que é aprendizagem ativa?

O QnA Maker aprende novas variações de perguntas com feedback implícito e explícito.

* [Feedback implícito](#how-qna-makers-implicit-feedback-works) – O ranker compreende quando uma pergunta do utilizador tem múltiplas respostas com pontuações que são muito próximas e considera isso como feedback. Não precisas de fazer nada para que isto aconteça.
* [Feedback explícito](#how-you-give-explicit-feedback-with-the-train-api) – Quando várias respostas com pouca variação nas pontuações são devolvidas da base de conhecimento, a aplicação do cliente pergunta ao utilizador qual é a pergunta correta. O feedback explícito do utilizador é enviado para o QnA Maker com a [API do comboio](../How-To/improve-knowledge-base.md#train-api).

Ambos os métodos fornecem ao ranker consultas semelhantes que estão agrupadas.

## <a name="how-active-learning-works"></a>Como funciona a aprendizagem ativa

A aprendizagem ativa é desencadeada com base nas pontuações das poucas respostas devolvidas pela QnA Maker. Se as diferenças de pontuação entre pares QnA que correspondem à consulta se situam dentro de um pequeno intervalo, então a consulta é considerada uma sugestão possível (como uma pergunta alternativa) para cada um dos possíveis pares QnA. Uma vez que você aceita a pergunta sugerida para um par QnA específico, é rejeitado para os outros pares. Tem de se lembrar de poupar e treinar, depois de aceitar sugestões.

A aprendizagem ativa dá as melhores sugestões possíveis nos casos em que os pontos finais estão recebendo uma quantidade razoável e variedade de consultas de uso. Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, o QnA Maker sugere as perguntas baseadas no utilizador ao designer de base de conhecimento para aceitar ou rejeitar. Todas as sugestões são agrupadas por semelhanças e as principais sugestões para questões alternativas são apresentadas com base na frequência das consultas específicas pelos utilizadores finais.

Uma vez que as perguntas são sugeridas no portal QnA Maker, você precisa rever e aceitar ou rejeitar essas sugestões. Não há uma API para gerir sugestões.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como funciona o feedback implícito da QnA Maker

O feedback implícito do QnA Maker usa um algoritmo para determinar a proximidade da pontuação e, em seguida, faz sugestões de aprendizagem ativa. O algoritmo para determinar a proximidade não é um cálculo simples. As gamas no exemplo seguinte não devem ser corrigidas, mas devem ser usadas como um guia apenas para entender o impacto do algoritmo.

Quando a pontuação de uma pergunta é altamente confiante, como 80%, o intervalo de pontuações que são considerados para a aprendizagem ativa são largos, aproximadamente dentro de 10%. À medida que a pontuação de confiança diminui, como 40%, o intervalo de pontuações também diminui, aproximadamente dentro de 4%.

Na resposta JSON seguinte de uma consulta à geração de 200 milhões de pessoas da QnA Maker, as pontuações para A, B e C estão próximas e seriam consideradas como sugestões.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

A QnA Maker não saberá qual a melhor resposta. Utilize a lista de sugestões do portal QnA Maker para selecionar a melhor resposta e treinar novamente.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como você dá feedback explícito com a API do comboio

O QnA Maker precisa de feedback explícito sobre qual das respostas foi a melhor resposta. A melhor resposta é determinada é consigo e pode incluir:

* Feedback do utilizador, selecionando uma das respostas.
* Lógica de negócio, como determinar um intervalo de pontuação aceitável.
* Uma combinação de feedback do utilizador e lógica de negócio.

Utilize a [API do comboio](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) para enviar a resposta correta à QnA Maker, depois de o utilizador a selecionar.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar versão de tempo de execução para usar a aprendizagem ativa

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Ative Learning é suportado na versão 4.4.0 ou superior. Se a sua base de conhecimento foi criada numa versão anterior, [atualize o seu tempo de execução](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) para utilizar esta funcionalidade.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Na gestão do QnA Maker (Preview), uma vez que o tempo de execução é hospedado pelo próprio serviço QnA Maker, não há necessidade de atualizar o tempo de funcionaamento manualmente.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ligue a aprendizagem ativa para questões alternativas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

A aprendizagem ativa está fora por defeito. Liga-o para ver as perguntas sugeridas. Depois de ligar a aprendizagem ativa, precisa enviar informações da aplicação do cliente para o QnA Maker. Para obter mais informações, consulte [o fluxo arquitetónico para utilizar as APIs generateAnswer e Train a partir de um bot.](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. Selecione **Publicar** para publicar a base de conhecimentos. As consultas de aprendizagem ativa são recolhidas apenas a partir do ponto final de previsão da API GenerateAnswer. As consultas ao painel de teste no portal QnA Maker não têm impacto na aprendizagem ativa.

1. Para ativar a aprendizagem no portal QnA Maker, vá para o canto superior direito, selecione o seu **Nome,** vá para [**As Definições de Serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ligue as alternativas de perguntas sugeridas pela aprendizagem ativa a partir da página de definições de Serviço. Selecione o nome do utilizador no menu superior direito e, em seguida, selecione Definições de Serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontre o serviço QnA Maker e, em seguida, altere a **Aprendizagem Ativa**.

    > [!div class="mx-imgBorder"]
    > [![Na página de definições de Serviço, alternar na funcionalidade De Aprendizagem Ativa. Se não conseguir alternar a funcionalidade, poderá ter de atualizar o seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. A sua versão pode ser diferente.

    Uma vez ativada a **Aprendizagem Ativa,** a base de conhecimento sugere novas questões a intervalos regulares com base em perguntas submetidas pelo utilizador. Pode desativar **a Aprendizagem Ativa** tocando novamente a definição.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Por padrão, a aprendizagem ativa está em **curso no** QnA Maker gerido (Preview). Para ver as perguntas alternativas sugeridas, [utilize as opções de Ver](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

---

## <a name="review-suggested-alternate-questions"></a>Revisão sugeriu perguntas alternativas

[Reveja perguntas alternativas sugeridas](improve-knowledge-base.md) na página **de Edição** de cada base de conhecimento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de conhecimento](./manage-knowledge-bases.md)
