---
title: Diretrizes de Design de Divulgação
titleSuffix: Azure Cognitive Services
description: Introdução às diretrizes de design de divulgação e avaliação do nível de divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74776390"
---
# <a name="disclosure-design-guidelines"></a>Diretrizes de conceção de divulgação
Aprenda a construir e manter a confiança com os clientes sendo transparente sobre a natureza sintética da sua experiência de voz.

## <a name="what-is-disclosure"></a>O que é a divulgação?

A divulgação é um meio de deixar as pessoas saberem que&#39;a interagir ou a ouvir uma voz que é gerada sinteticamente.

## <a name="why-is-disclosure-necessary"></a>Por que a divulgação é necessária?

A necessidade de divulgar as origens sintéticas de uma voz gerada por computador é relativamente nova. No passado, as vozes geradas por computador eram obviamente que - ninguém as confundiria com uma pessoa real. No entanto, todos os dias, o realismo das vozes sintéticas melhora, e tornam-se cada vez mais indistinguíveis das vozes humanas.

## <a name="goals"></a>Objetivos
Estes são os princípios a ter em mente ao conceber experiências de voz sintéticas:

**Reforçar a confiança**
<br>Desenhe com a intenção de falhar o Teste de Turing sem degradar a experiência. Deixe os utilizadores falarem sobre o facto de estarem a interagir com uma voz sintética, permitindo-lhes envolver-se perfeitamente com a experiência.

**Adaptar-se ao contexto de utilização**
<br>Compreenda quando, onde e como os seus utilizadores irão interagir com a voz sintética para fornecer o tipo certo de divulgação no momento certo.

**Definir expectativas claras**
<br>Permitir que os utilizadores descubram e compreendam facilmente as capacidades do agente. Ofereça oportunidades para aprender mais sobre tecnologia de voz sintética a pedido.

**Abraçar o fracasso**
<br>Use momentos de falha para reforçar as capacidades do agente.

## <a name="how-to-use-this-guide"></a>Como utilizar este guia

Este guia ajuda-o a determinar quais os padrões de divulgação mais adequados para a sua experiência de voz sintética. Em seguida, oferecemos exemplos de como e quando usá-los. Cada um destes padrões é projetado para maximizar a transparência com os utilizadores sobre a fala sintética, mantendo-se fiel ao design centrado no homem.

Considerando o vasto corpo de orientação de design sobre experiências de voz, focamo-nos aqui especificamente em:

1. [**Avaliação da divulgação**](#disclosure-assessment): Um processo para determinar o tipo de divulgação recomendado para a sua experiência de voz sintética

2. [**Como divulgar**](concepts-disclosure-patterns.md): Exemplos de padrões de divulgação que podem ser aplicados à sua experiência de voz sintética

3. [**Quando divulgar**](concepts-disclosure-patterns.md#when-to-disclose): Momentos ideais para divulgar ao longo da viagem do utilizador

## <a name="disclosure-assessment"></a>Avaliação da divulgação
Considere os seus utilizadores&#39; expectativas sobre uma interação e o contexto em que irão experimentar a voz. Se o contexto deixar claro que &quot;uma&quot; voz sintética está a falar, a divulgação pode ser mínima, momentânea ou mesmo desnecessária. Os principais tipos de contexto que afetam a divulgação incluem o tipo de persona, o tipo de cenário e o nível de exposição. Também ajuda a considerar quem pode estar a ouvir.

### <a name="understand-context"></a>Compreender o contexto

Utilize esta folha de cálculo para determinar o contexto da sua experiência de voz sintética. Vai aplicar isto no próximo passo onde determinará o seu nível de divulgação.

|                                    | Contexto de utilização                                                                                                                                                                                                                                                                                                                                                       | Riscos potenciais & Desafios                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Tipo persona**               | **Se algum dos seguintes se aplicar, a sua personalidade enquadra-se na categoria "Persona humana":**<br><br><ul><li> Persona personifica um humano real, quer seja uma representação fictícia ou não. (por exemplo, fotografia ou renderização gerada por computador de uma pessoa real)<br><br><li> A voz sintética baseia-se na voz de uma pessoa real amplamente reconhecível (por exemplo, celebridade, figura política) | Quanto mais representações humanas dás à tua persona, mais provável é que um utilizador a associe a uma pessoa real, ou faça com que acreditem que o conteúdo é falado por uma pessoa real e não por computador. </ul>                                                                                                                                                                      |
| **2. Tipo de cenário**            | **Se algum dos seguintes se aplicar, a sua experiência de voz enquadra-se na categoria "Sensível":**<br><br><ul><li> Obtém ou exibe informações pessoais do utilizador <br><br> <li> Notícias/informações sensíveis ao tempo de transmissão (por exemplo, alerta de emergência)<br><br><li> Tem como objetivo ajudar pessoas reais a comunicarem entre si (por exemplo, lê e-mails/textos pessoais)<br><br> <li> Presta assistência médica/sanitária </ul>            | O uso da voz sintética pode não se sentir apropriado ou de confiança para as pessoas que a utilizam quando os tópicos estão relacionados com questões sensíveis, pessoais ou urgentes. Podem também esperar o mesmo nível de empatia e consciência contextual que um humano real. |
| **3. Nível de exposição** |**A sua experiência de voz provavelmente se enquadra na categoria "High" se:** <br><br><ul><li>O utilizador ouvirá ou interagirá com a voz sintética com frequência ou por um longo período de tempo </ul>                                                                                                                                                                             | A importância de ser transparente e de criar confiança com os utilizadores é ainda maior quando se estabelecem relações a longo prazo.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determinar o nível de divulgação

Utilize o seguinte diagrama para determinar se a sua experiência de voz sintética requer alta ou baixa divulgação com base no seu contexto de utilização.

  ![Diagrama de avaliação de divulgação](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Doutorados de referência

* [Divulgação de Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Diretrizes para a implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
* [Visão geral de gating](concepts-gating-overview.md)

## <a name="next-steps"></a>Passos seguintes

* [Padrões de conceção de divulgação](concepts-disclosure-patterns.md)
