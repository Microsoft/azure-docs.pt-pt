---
title: Sugestões de aprendizagem ativa - QnA Maker
description: As sugestões de aprendizagem ativa permitem-lhe melhorar a qualidade da sua base de conhecimentos, sugerindo questões alternativas, baseadas em submissões de utilizadores, ao seu par de perguntas e respostas.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: d39e34bd85c4524a6f28d188f977a7ab37eecc58
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445018"
---
# <a name="active-learning-suggestions"></a>Sugestões de aprendizagem ativa

A funcionalidade _de sugestões de aprendizagem Ativa_ permite-lhe melhorar a qualidade da sua base de conhecimentos, sugerindo questões alternativas, baseadas em submissões de utilizadores, ao seu par de perguntas e respostas. Reveja essas sugestões, ou adicioná-las às questões existentes ou rejeitá-las.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração produza efeitos, tem de aceitar as sugestões. Estas sugestões adicionam perguntas mas não alteram ou removem as questões existentes.

## <a name="what-is-active-learning"></a>O que é aprendizagem ativa?

O QnA Maker aprende novas variações de perguntas com feedback implícito e explícito.

* [Feedback implícito](#how-qna-makers-implicit-feedback-works) – O ranker compreende quando uma pergunta do utilizador tem múltiplas respostas com pontuações que são muito próximas e considera isso como feedback. Não precisas de fazer nada para que isto aconteça.
* [Feedback explícito](#how-you-give-explicit-feedback-with-the-train-api) – Quando várias respostas com pouca variação nas pontuações são devolvidas da base de conhecimento, a aplicação do cliente pergunta ao utilizador qual é a pergunta correta. O feedback explícito do utilizador é enviado para o QnA Maker com a [API do comboio](../How-to/improve-knowledge-base.md#train-api).

Ambos os métodos fornecem ao ranker consultas semelhantes que estão agrupadas.

## <a name="how-active-learning-works"></a>Como funciona a aprendizagem ativa

A aprendizagem ativa é desencadeada com base nas pontuações das poucas respostas devolvidas pela QnA Maker. Se as diferenças de pontuação entre pares QnA que correspondem à consulta se situam dentro de um pequeno intervalo, então a consulta é considerada uma sugestão possível (como uma pergunta alternativa) para cada um dos possíveis pares QnA. Uma vez que você aceita a pergunta sugerida para um par QnA específico, é rejeitado para os outros pares. Tem de se lembrar de poupar e treinar, depois de aceitar sugestões.

A aprendizagem ativa dá as melhores sugestões possíveis nos casos em que os pontos finais estão recebendo uma quantidade razoável e variedade de consultas de uso. Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, o QnA Maker sugere as perguntas baseadas no utilizador ao designer de base de conhecimento para aceitar ou rejeitar. Todas as sugestões são agrupadas por semelhanças e as principais sugestões para questões alternativas são apresentadas com base na frequência das consultas específicas pelos utilizadores finais.

Uma vez que as perguntas são sugeridas no portal QnA Maker, você precisa rever e aceitar ou rejeitar essas sugestões. Não há uma API para gerir sugestões.

## <a name="turn-on-active-learning"></a>Ativar a aprendizagem ativa

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Por predefinição, a aprendizagem ativa está **desligada**.
Para utilizar a aprendizagem ativa:
* Tens de ativar a [aprendizagem ativa](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) para que a QnA Maker recolha perguntas alternativas para a tua base de conhecimento.
* Para ver as perguntas alternativas sugeridas, [utilize as opções de Ver](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Por padrão, a aprendizagem ativa está em **curso no** QnA Maker gerido (Preview). Para ver as perguntas alternativas sugeridas, [utilize as opções de Ver](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

---

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

Utilize a [API do comboio](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/runtime/train) para enviar a resposta correta à QnA Maker, depois de o utilizador a selecionar.

## <a name="next-step"></a>Passo seguinte

> [!div class="nextstepaction"]
> [Consultar a base de conhecimentos](query-knowledge-base.md)
