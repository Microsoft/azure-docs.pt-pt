---
title: Diretrizes de design de divulgação
titleSuffix: Azure Cognitive Services
description: Introdução às diretrizes de conceção de divulgação e avaliação do nível de divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: f1889d49d1eb4981e59af6248817a6a9b6e2002d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537260"
---
# <a name="disclosure-design-guidelines"></a>Diretrizes de conceção de divulgação
Aprenda a construir e manter a confiança com os clientes, sendo transparente sobre a natureza sintética da sua experiência de voz.

## <a name="what-is-disclosure"></a>O que é divulgação?

A divulgação é um meio de informar as pessoas de que&#39;interagindo ou ouvindo uma voz que é gerada sinteticamente.

## <a name="why-is-disclosure-necessary"></a>Por que a divulgação é necessária?

A necessidade de divulgar as origens sintéticas de uma voz gerada por computador é relativamente nova. No passado, as vozes geradas por computador eram obviamente que - ninguém nunca as confundiria com uma pessoa real. Todos os dias, porém, o realismo das vozes sintéticas melhora, e tornam-se cada vez mais indistinguíveis das vozes humanas.

## <a name="goals"></a>Objetivos
Estes são os princípios a ter em mente ao conceber experiências de voz sintéticas:

**Reforçar a confiança**
<br>Desenhe com a intenção de falhar no Teste de Turing sem degradar a experiência. Deixe os utilizadores insetarem-se no facto de estarem a interagir com uma voz sintética, permitindo-lhes envolver-se perfeitamente com a experiência.

**Adaptar-se ao contexto de utilização**
<br>Compreenda quando, onde e como os seus utilizadores irão interagir com a voz sintética para fornecer o tipo certo de divulgação no momento certo.

**Definir expectativas claras**
<br>Permitir que os utilizadores descubram e compreendam facilmente as capacidades do agente. Ofereça oportunidades para aprender mais sobre tecnologia de voz sintética a pedido.

**Abraçar o fracasso**
<br>Use momentos de falha para reforçar as capacidades do agente.

## <a name="how-to-use-this-guide"></a>Como utilizar este guia

Este guia ajuda-o a determinar quais os padrões de divulgação que melhor se adequam à sua experiência de voz sintética. Em seguida, oferecemos exemplos de como e quando usá-los. Cada um destes padrões é projetado para maximizar a transparência com os utilizadores sobre a fala sintética, mantendo-se fiel ao design centrado no ser humano.

Considerando o vasto corpo de orientação de design em experiências de voz, focamo-nos aqui especificamente em:

1. [**Avaliação de divulgação**](#disclosure-assessment): Um processo para determinar o tipo de divulgação recomendado para a sua experiência de voz sintética

2. [**Como divulgar**](concepts-disclosure-patterns.md): Exemplos de padrões de divulgação que podem ser aplicados à sua experiência de voz sintética

3. [**Quando divulgar**](concepts-disclosure-patterns.md#when-to-disclose): Momentos ideais para divulgar ao longo da jornada do utilizador

## <a name="disclosure-assessment"></a>Avaliação da divulgação
Considere os seus utilizadores&#39; expectativas sobre uma interação e o contexto em que irão experimentar a voz. Se o contexto deixar claro que uma voz sintética está &quot; a falar, a &quot; divulgação pode ser mínima, momentânea ou mesmo desnecessária. Os principais tipos de contexto que afetam a divulgação incluem o tipo de persona, o tipo de cenário e o nível de exposição. Também ajuda a considerar quem pode estar a ouvir.

### <a name="understand-context"></a>Compreender o contexto

Utilize esta folha de cálculo para determinar o contexto da sua experiência de voz sintética. Vais aplicar isto no próximo passo onde vais determinar o teu nível de divulgação.

|                                    | Contexto de utilização                                                                                                                                                                                                                                                                                                                                                       | Riscos potenciais & desafios                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Tipo persona**               | **Se alguma das seguintes se aplicarem, a sua personalidade enquadra-se na categoria "Persona semelhante ao humano":**<br><br><ul><li> Persona personifica um humano real, seja uma representação fictícia ou não. (por exemplo, fotografia ou uma prestação gerada por computador de uma pessoa real)<br><br><li> A voz sintética baseia-se na voz de uma pessoa real amplamente reconhecível (por exemplo, celebridade, figura política) | Quanto mais representações humanas você dá a sua persona, mais provável que um utilizador a associe a uma pessoa real, ou fazê-los acreditar que o conteúdo é falado por uma pessoa real em vez de gerado por computador. </ul>                                                                                                                                                                      |
| **2. Tipo de cenário**            | **Se alguma das seguintes se aplicarem, a sua experiência de voz enquadra-se na categoria 'Sensível':**<br><br><ul><li> Obtém ou exibe informações pessoais do utilizador <br><br> <li> Transmite notícias/informações sensíveis ao tempo (por exemplo, alerta de emergência)<br><br><li> Visa ajudar pessoas reais a comunicarem entre si (por exemplo, lê-se e-mails/textos pessoais)<br><br> <li> Presta assistência médica/saúde </ul>            | O uso da voz sintética pode não parecer adequado ou confiável para as pessoas que a utilizam quando os tópicos estão relacionados com assuntos sensíveis, pessoais ou urgentes. Podem também esperar o mesmo nível de empatia e consciência contextual que um verdadeiro ser humano. |
| **3. Nível de exposição** |**A sua experiência de voz provavelmente se enquadra na categoria 'High' se:** <br><br><ul><li>O utilizador ouvirá ou interagirá com a voz sintética frequentemente ou durante um longo período de tempo </ul>                                                                                                                                                                             | A importância de ser transparente e de criar confiança com os utilizadores é ainda maior quando se estabelecem relações de longo prazo.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determinar o nível de divulgação

Utilize o seguinte diagrama para determinar se a sua experiência de voz sintética requer uma divulgação alta ou baixa com base no seu contexto de utilização.

  ![Diagrama de avaliação de divulgação](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Ver também

* [Padrões de conceção de divulgação](concepts-disclosure-patterns.md)
* [Divulgação de Talento de Voz](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Orientações para a implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)