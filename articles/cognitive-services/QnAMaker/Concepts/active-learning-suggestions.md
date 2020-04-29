---
title: Sugestões de aprendizagem ativa - QnA Maker
description: Sugestões de aprendizagem ativa permitem-lhe melhorar a qualidade da sua base de conhecimentos sugerindo perguntas alternativas, baseadas em submissões de utilizador, ao seu par de perguntas e respostas.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: edbe06b12fbb97473b28ccca968fd3e7d8366152
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804225"
---
# <a name="active-learning-suggestions"></a>Sugestões de aprendizagem ativa

A funcionalidade de sugestões de _aprendizagem Ativa_ permite-lhe melhorar a qualidade da sua base de conhecimentos, sugerindo perguntas alternativas, baseadas em submissões de utilizador, ao seu par de perguntas e respostas. Revê essas sugestões, quer as adicione às perguntas existentes ou as rejeite.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração entre em vigor, deve aceitar as sugestões. Estas sugestões acrescentam perguntas, mas não alteram ou removem as questões existentes.

## <a name="what-is-active-learning"></a>O que é a aprendizagem ativa?

QnA Maker aprende novas variações de perguntas com feedback implícito e explícito.

* [Feedback implícito](#how-qna-makers-implicit-feedback-works) – O ranker compreende quando uma pergunta do utilizador tem múltiplas respostas com pontuações muito próximas e considera isso como feedback. Não precisas de fazer nada para que isto aconteça.
* [Feedback explícito](#how-you-give-explicit-feedback-with-the-train-api) – Quando várias respostas com pouca variação nas pontuações são devolvidas da base de conhecimento, a aplicação do cliente pergunta ao utilizador qual a pergunta correta. O feedback explícito do utilizador é enviado para o Fabricante qnA com a [API](../How-to/improve-knowledge-base.md#train-api)do comboio .

Ambos os métodos fornecem ao ranker consultas semelhantes que são agrupadas.

## <a name="how-active-learning-works"></a>Como funciona a aprendizagem ativa

A aprendizagem ativa é desencadeada com base nas pontuações das poucas respostas mais altas devolvidas pela QnA Maker. Se as diferenças de pontuação entre pares QnA que correspondem à consulta estão dentro de um pequeno intervalo, então a consulta é considerada uma possível sugestão (como uma pergunta alternativa) para cada um dos possíveis pares QnA. Uma vez que você aceita a pergunta sugerida para um par qnA específico, é rejeitado para os outros pares. Tens de te lembrar de poupar e treinar, depois de aceitarsugestões.

A aprendizagem ativa dá as melhores sugestões possíveis nos casos em que os pontos finais estão recebendo uma quantidade razoável e uma variedade de consultas de uso. Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, a QnA Maker sugere que as perguntas baseadas no utilizador ao designer de base de conhecimento aceitem ou rejeitem. Todas as sugestões são agrupadas por semelhanças e as sugestões de topo para perguntas alternativas são apresentadas com base na frequência das consultas específicas pelos utilizadores finais.

Uma vez sugeridas perguntas no portal QnA Maker, é necessário rever e aceitar ou rejeitar essas sugestões. Não há uma API para gerir sugestões.

## <a name="turn-on-active-learning"></a>Ativar a aprendizagem ativa

Por defeito, a aprendizagem ativa está **desligada.**
Para utilizar a aprendizagem ativa:
* Você precisa ativar a [aprendizagem ativa](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) para que o QnA Maker colete perguntas alternativas para a sua base de conhecimento.
* Para ver as perguntas alternativas sugeridas, [utilize as opções do View](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como funciona o feedback implícito do QnA Maker

O feedback implícito do QnA Maker usa um algoritmo para determinar a proximidade da pontuação e depois faz sugestões de aprendizagem ativa. O algoritmo para determinar a proximidade não é um cálculo simples. As gamas no exemplo seguinte não devem ser corrigidas, mas devem ser usadas como guia para entender apenas o impacto do algoritmo.

Quando a pontuação de uma pergunta é altamente confiante, como 80%, o leque de pontuações que são consideradas para a aprendizagem ativa são amplas, aproximadamente dentro de 10%. À medida que a pontuação da confiança diminui, como 40%, o intervalo de pontuações também diminui, aproximadamente dentro de 4%.

Na seguinte resposta jSON de uma consulta à geração da QnA MakerAnswer, as pontuações para A, B e C estão próximas e seriam consideradas como sugestões.

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

O Fabricante qna não saberá qual a resposta melhor. Utilize a lista de sugestões do portal QnA Maker para selecionar a melhor resposta e treinar novamente.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como dá feedback explícito com a API do comboio

QnA Maker precisa de feedback explícito sobre qual das respostas foi a melhor resposta. Como a melhor resposta é determinada é consigo e pode incluir:

* Feedback do utilizador, selecionando uma das respostas.
* Lógica empresarial, como determinar um intervalo de pontuação aceitável.
* Uma combinação de feedback do utilizador e lógica de negócio.

Utilize a [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) do comboio para enviar a resposta correta ao Fabricante QnA, depois de o utilizador o selecionar.

## <a name="next-step"></a>Passo seguinte

> [!div class="nextstepaction"]
> [Consulta da base de conhecimento](query-knowledge-base.md)