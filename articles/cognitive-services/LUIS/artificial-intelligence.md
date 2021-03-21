---
title: Inteligência artificial (IA)
description: A LUIS utiliza a inteligência artificial (IA) para fornecer compreensão linguística aos seus dados, com base no esquema que definiu.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021376"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Inteligência artificial na Compreensão da Linguagem (LUIS)

A LUIS utiliza a inteligência artificial (IA) para fornecer compreensão natural da linguagem (NLU) aos seus dados, com base no esquema que definiu.

## <a name="natural-language-processing-nlp"></a>Processamento de linguagem natural (NLP)

A Compreensão da Linguagem Natural (NLU) é um subtópico específico do Processamento de Linguagem Natural (NLP).

O processamento de linguagem natural é um conceito mais amplo que trata de qualquer forma de processamento de dados textuais, isto inclui coisas como:

* Tokenização,
* Parte da Marcação (pos) do Discurso
* Segmentação
* Análise morfológica
* Semelhança semântica
* Discurso
* Tradução

## <a name="natural-language-processing-in-luis"></a>Processamento de linguagem natural em LUIS

O processamento de linguagem natural está disponível para a sua aplicação LUIS das seguintes formas:
* [Compreensão da linguagem natural](#natural-language-processing-nlp) (LUIS)
* Aspetos de NLP configuráveis em LUIS:
    * [Tokenização](luis-language-support.md#tokenization)
    * Morfologia através de diacríticos, pontuação e palavra forma [configurações de API](luis-reference-application-settings.md)
* Pré-processamento ou pós-processamento da expressão de consulta fornecida por outros [Serviços Cognitivos,](../what-are-cognitive-services.md) tais como:
    * [Tradução](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>Compreensão da linguagem natural (NLU)

NLU é a capacidade de _transformar_ uma declaração linguística numa representação que lhe permite entender os seus utilizadores naturalmente. A compreensão da linguagem natural continua a ser um problema muito desafiante e é definido como um problema _difícil de IA._

A LUIS destina-se a focar-se na intenção e extração, isto inclui ser capaz de identificar:
* O que o utilizador quer
* Do que estão a falar.

A LUIS tem pouco ou nenhum conhecimento dos aspetos mais amplos do _PNL,_ como a semelhança semântica, sem identificação explícita em exemplos. Por exemplo, os seguintes tokens (palavras) são três coisas diferentes até serem utilizados em contextos semelhantes nos exemplos fornecidos:

* comprar
* compra
* comprado

## <a name="next-steps"></a>Passos seguintes

* Conheça o [ciclo de vida de desenvolvimento de](luis-concept-app-iteration.md) uma app LUIS