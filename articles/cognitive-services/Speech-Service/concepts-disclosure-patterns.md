---
title: Padrões de design de divulgação
titleSuffix: Azure Cognitive Services
description: Padrões de design e boas práticas para a divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 5cf933a11956bb8459bf6f5cd1b2b366eb8419d8
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508647"
---
# <a name="disclosure-design-patterns"></a>Padrões de conceção de divulgação
Agora que&#39;determinou o [nível](concepts-disclosure-guidelines.md#disclosure-assessment) certo de divulgação para a sua experiência de voz sintética,&#39;é uma boa altura para explorar potenciais padrões de design.
## <a name="overview"></a>Descrição Geral
Há um espectro de padrões de design de divulgação que pode aplicar à sua experiência de voz sintética. Se o resultado da sua avaliação de divulgação foi "Alta Divulgação", recomendamos [**a divulgação explícita,**](#explicit-disclosure)o que significa comunicar as origens da voz sintética. [**A divulgação implícita**](#implicit-disclosure) inclui pistas e padrões de interação que beneficiam experiências de voz, quer os níveis de divulgação necessários sejam ou não elevados ou baixos.
![Espectro de padrões de divulgação](media/responsible-ai/disclosure-patterns/affordances.png)






| Padrões de divulgação explícitos                                                                                                                                                                                    | Padrões de divulgação implícitos                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Introdução Transparente](#transparent-introduction)<br> [Introdução Verbal Transparente](#verbal-transparent-introduction)<br>  [Byline explícito](#explicit-byline)<br>  [Personalização e Calibração](#customization-and-calibration)<br> [Divulgação parental](#parental-disclosure)<br> [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Divulgação de capacidades](#capability-disclosure)<br>[Pistas implícitas e feedback](#implicit-cues--feedback)<br> [Transparência de Conversação](#conversational-transparency) |



Utilize o gráfico seguinte para se referir diretamente aos padrões que se aplicam à sua voz sintética. Algumas das outras condições deste gráfico também podem aplicar-se ao seu cenário:<br/>



| Se a sua experiência de voz sintética... | Recomendações | Padrões de estrutura |
| --- | --- | --- |
| Requer alta divulgação  | Use pelo menos um padrão explícito e pistas implícitas na frente para ajudar os utilizadores a construir associações. |[Divulgação Explícita](#explicit-disclosure)<br>[Divulgação Implícita](#implicit-disclosure)  |
| Requer baixa divulgação | A divulgação pode ser mínima ou desnecessária, mas pode beneficiar de alguns padrões implícitos. | [Divulgação de capacidades](#capability-disclosure)<br>[Transparência de Conversação](#conversational-transparency)  |
| Tem um alto nível de envolvimento | Construa a longo prazo e ofereça vários pontos de entrada para a divulgação ao longo da jornada do utilizador. É altamente recomendado ter uma experiência de embarque. | [Introdução Transparente](#transparent-introduction)<br>[Personalização e Calibração](#customization-and-calibration)<br>[Divulgação de capacidades](#capability-disclosure) |
| Inclui as crianças como o público principal pretendido | Os pais-alvo como o público principal de divulgação e garantir que eles podem comunicar eficazmente a divulgação às crianças.  | [Divulgação parental](#parental-disclosure)<br>[Introdução Verbal Transparente](#verbal-transparent-introduction)<br> [Divulgação Implícita](#implicit-disclosure)<br> [Transparência de Conversação](#conversational-transparency)  |
| Inclui utilizadores cegos ou pessoas com baixa visão como o público principal pretendido  | Inclua todos os utilizadores e certifique-se de que qualquer forma de divulgação visual tem efeitos de texto ou som alternativos associados. Aderir às normas de acessibilidade para a relação contraste e o tamanho do ecrã. Use pistas auditivas para comunicar a divulgação.  | [Introdução Verbal Transparente](#verbal-transparent-introduction) <br>[Pistas Auditivas](#implicit-cues--feedback)<br>[Pistas Hápticas](#implicit-cues--feedback)<br>[Transparência de Conversação](#conversational-transparency)<br>[Normas de acessibilidade](https://www.microsoft.com/accessibility) |
| É sem ecrã, sem dispositivo ou usa a voz como o modo primário ou único de interação | Use pistas auditivas para comunicar a divulgação. | [Introdução Verbal Transparente](#verbal-transparent-introduction) <br> [Pistas Auditivas](#implicit-cues--feedback)  |
| Potencialmente inclui vários utilizadores/ouvintes (por exemplo, assistente pessoal em vários agregados familiares)  | Esteja atento a vários contextos de utilizador e níveis de compreensão e ofereça múltiplas oportunidades de divulgação na viagem do utilizador.  | [Introdução Transparente (Utilizador de Retorno)](#transparent-introduction)<br> [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Transparência de Conversação](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Divulgação explícita
Se a sua experiência de voz sintética requer alta divulgação, é melhor usar pelo menos um dos seguintes padrões explícitos para indicar claramente a natureza sintética.
### <a name="transparent-introduction"></a>Introdução Transparente

Antes do início da experiência de voz, introduza o assistente digital sendo totalmente transparente sobre as origens da sua voz e das suas capacidades. O momento ideal para utilizar este padrão é quando está a bordo de um novo utilizador ou ao introduzir novas funcionalidades num utilizador que regressa. Implementar pistas implícitas durante uma introdução ajuda os utilizadores a formar um modelo mental sobre a natureza sintética do agente digital.

#### <a name="first-time-user-experience"></a>Experiência de utilizador pela primeira vez

![Introdução transparente durante a primeira experiência de execução](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*A voz sintética é introduzida durante o embarque de um novo utilizador.*

Recomendações
- Descreva que a voz é artificial (por &quot; exemplo, &quot; digital)
- Descreva o que o agente é capaz de fazer
- Explicitamente declarar a voz&#39;as origens
- Ofereça um ponto de entrada para saber mais sobre a voz sintética

#### <a name="returning-user-experience"></a>Experiência de utilizador de retorno

Se um utilizador ignorar a experiência de embarque, continue a oferecer pontos de entrada à experiência Introdução Transparente até que o utilizador desencadeie a voz pela primeira vez.
<br/>

![Introdução transparente durante a experiência do utilizador de retorno](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Proporcionar um ponto de entrada consistente à experiência de voz sintética. Deixe o utilizador voltar à experiência de embarque quando ativar a voz pela primeira vez em qualquer ponto da viagem do utilizador.*


### <a name="verbal-transparent-introduction"></a>Introdução Verbal Transparente

Um aviso falado indicando as origens do assistente digital&#39;voz é explícito o suficiente por si só para alcançar a divulgação. Este padrão é o melhor para cenários de alta divulgação onde a voz é o único modo de interação disponível.
<br/>

![Introdução transparente verbalmente falada](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Utilize uma introdução transparente quando houver momentos na experiência do utilizador em que já possa introduzir ou atribuir uma pessoa&#39;voz.*


![Introdução verbalmente falada e transparente na primeira pessoa](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Para maior transparência, o ator de voz pode divulgar as origens da voz sintética na primeira pessoa.*

### <a name="explicit-byline"></a>Byline explícito

Utilize este padrão se o utilizador interagir com um leitor de áudio ou um componente interativo para ativar a voz.


![Byline explícito em um cenário de imprensa](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Um byline explícito é a atribuição de onde a voz veio.*

Recomendações

- Oferta ponto de entrada para saber mais sobre a voz sintetizada

### <a name="customization-and-calibration"></a>Personalização e Calibração

Fornecer aos utilizadores o controlo sobre a forma como o assistente digital responde a eles (isto é, como a voz soa).  Quando um utilizador interage com um sistema nos seus próprios termos e com objetivos específicos em mente, então, por definição, já entendeu que&#39;não é uma pessoa real.

#### <a name="user-control"></a>Controlo do Utilizador

Oferecer escolhas que tenham um impacto significativo e percetível na experiência de voz sintética.

![Preferências dos utilizadores](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*As preferências dos utilizadores permitem aos utilizadores personalizar e melhorar a sua experiência.*

Recomendações

- Permitir que os utilizadores personalizem a voz (por exemplo, selecione linguagem e tipo de voz)
- Fornecer aos utilizadores uma forma de ensinar o sistema a responder à sua voz única (por exemplo, calibração de voz, comandos personalizados)
- Otimizar para interações geradas pelo utilizador ou contextuais (por exemplo, lembretes)

#### <a name="persona-customization"></a>Personalização persona

Ofereça formas de personalizar a voz do assistente digital&#39;. Se a voz for baseada numa celebridade ou numa pessoa amplamente reconhecível, considere usar introduções visuais e faladas quando os utilizadores pré-visualizarem a voz.

![Personalização de voz](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Oferecer a capacidade de selecionar a partir de um conjunto de vozes ajuda a transmitir a natureza artificial.*

Recomendações
- Permitir que os utilizadores pré-visualizam o som de cada voz
- Use uma introdução autêntica para cada voz
- Ofereça pontos de entrada para saber mais sobre a voz sintetizada

### <a name="parental-disclosure"></a>Divulgação parental

Além de cumprir os regulamentos da COPPA, forneça a divulgação aos pais se o seu público principal pretendido for crianças pequenas e o seu nível de exposição é elevado. Para usos sensíveis, considere aproveitar a experiência até que um adulto reconheça o uso da voz sintética. Encoraje os pais a comunicarem a mensagem aos seus filhos.

![Divulgação para os pais](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Uma introdução transparente otimizada para os pais garante que um adulto foi sensibilizado para a natureza sintética da voz antes de uma criança interagir com ela.*

Recomendações

- Pais-alvo como o público principal para a divulgação
- Encoraje os pais a comunicar a divulgação aos seus filhos
- Ofereça pontos de entrada para saber mais sobre a voz sintetizada
- Gate a experiência, pedindo aos pais uma simples &quot; questão de salvaguarda &quot; para mostrar que leram a divulgação

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Proporcionando oportunidades para aprender mais sobre como a voz foi feita

Oferecer pontos de entrada sensíveis ao contexto a uma página, pop-up ou site externo que forneça mais informações sobre a tecnologia de voz sintética. Por exemplo, pode emergir um link para saber mais durante o embarque ou quando o utilizador pede mais informações durante a conversação.

![Ponto de entrada para saber mais](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Exemplo de um ponto de entrada para oferecer a oportunidade de aprender mais sobre a voz sintetizada.*

Uma vez que um utilizador solicita mais informações sobre a voz sintética, o objetivo principal é educá-los sobre as origens da voz sintética e ser transparente sobre a tecnologia.

![Fornecer aos utilizadores mais informações sobre a voz sintética](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Mais informações podem ser oferecidas num site externo de ajuda.*

Recomendações

- Simplificar conceitos complexos e evitar a utilização de um jargão legal e técnico
- Não enterre este conteúdo em privacidade e termos de uso declarações
- Mantenha o conteúdo conciso e use imagens quando disponível

## <a name="implicit-disclosure"></a>Divulgação implícita

A consistência é a chave para alcançar a divulgação implicitamente ao longo da viagem do utilizador. O uso consistente de pistas visuais e auditivas através de dispositivos e modos de interação pode ajudar a construir associações entre padrões implícitos e divulgação explícita.

![Consistência das pistas implícitas](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Sugestões implícitas & feedback

O antropomorfismo pode manifestar-se de diferentes formas, desde a representação visual real do agente, à voz, sons, padrões de luz, formas saltitantes ou até mesmo a vibração de um dispositivo. Ao definir a sua persona, aproveite pistas implícitas e padrões de feedback em vez de apontar para um avatar muito humano. Esta é uma forma de minimizar a necessidade de uma divulgação mais explícita.

![Pistas visuais e feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Estas deixas ajudam a antropomorfizar o agente sem ser muito humano. Podem também tornar-se mecanismos eficazes de divulgação por si só quando utilizados de forma consistente ao longo do tempo.*

Considere os diferentes modos de interações da sua experiência ao incorporar os seguintes tipos de pistas:

| Pistas Visuais                                                                                                                                                               | Pistas Auditivas                                                      | Pistas Hápticas |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Pistas em tempo real responsivas (por exemplo, animações)<br> Pistas não-screen (por exemplo, luzes e padrões num dispositivo)<br>  | Sonicon (por exemplo, um breve som distinto, série de notas musicais) | Vibração   |

### <a name="capability-disclosure"></a>Divulgação de capacidades

A divulgação pode ser alcançada implicitamente estabelecendo expectativas precisas para o que o assistente digital é capaz de fazer. Forneça comandos de amostra para que os utilizadores possam aprender a interagir com o assistente digital e oferecer ajuda contextual para aprender mais sobre a voz sintética durante as fases iniciais da experiência.

![Exemplo de respostas padrão a uma conversa que pode criar.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Transparência de Conversação

Quando as conversas caem em caminhos inesperados, considere criar respostas padrão que possam ajudar a repor expectativas, reforçar a transparência e orientar os utilizadores para caminhos bem sucedidos. Há oportunidades de usar a divulgação explícita na conversa também.

![Manuseando caminhos inesperados](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Questões fora da tarefa ou &quot; &quot; pessoais dirigidas ao agente são uma boa altura para lembrar aos utilizadores a natureza sintética do agente e guiá-los a envolverem-se adequadamente com ele ou a redirecioná-los para uma pessoa real.

![Tratamento de questões de tarefa](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Quando divulgar

Existem muitas oportunidades de divulgação ao longo da viagem de utilizador. Design para primeira utilização, segunda utilização, nth use..., mas também abraçar momentos de falta de &quot; &quot; realce na transparência — como quando o sistema comete um erro ou quando o utilizador descobre uma limitação das capacidades do agente&#39;.

![Oportunidades de divulgação ao longo de uma jornada de utilizador](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Exemplo de uma viagem de utilizador digital assistente padrão destacando várias oportunidades de divulgação.

### <a name="up-front"></a>Na frente

O momento ideal para a divulgação é a primeira vez que uma pessoa interage com a voz sintética.Num cenário de assistente de voz pessoal, esta seria durante o embarque, ou a primeira vez que o utilizador praticamente desembrulhou a experiência. Noutros cenários, pode ser a primeira vez que uma voz sintética lê conteúdo num website ou a primeira vez que um utilizador interage com um personagem virtual.

- [Introdução Transparente](#transparent-introduction)
- [Divulgação de capacidades](#capability-disclosure)
- [Personalização e Calibração](#customization-and-calibration)
- [Pistas Implícitas](#implicit-cues--feedback)

### <a name="upon-request"></a>A pedido

Os utilizadores devem poder aceder facilmente a informações adicionais, controlar preferências e receber comunicação transparente em qualquer ponto durante a viagem do utilizador quando solicitado.

- [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Personalização e Calibração](#customization-and-calibration)
- [Transparência de Conversação](#conversational-transparency)

### <a name="continuously"></a>Continuamente

Utilize os padrões de design implícitos que melhoram a experiência do utilizador continuamente.

- [Divulgação de capacidades](#capability-disclosure)
- [Pistas Implícitas](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Quando o sistema falha

Use a divulgação como uma oportunidade para falhar graciosamente.

- [Transparência de Conversação](#conversational-transparency)
- [Proporcionando oportunidades para aprender mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Entrega ao ser humano](#conversational-transparency)



## <a name="additional-resources"></a>Recursos adicionais
- [Diretrizes do Microsoft Bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Diretrizes de Design cortana](/cortana/voice-commands/voicecommand-design-guidelines)
- [Diretrizes de design de discurso do Microsoft Windows UWP](/windows/uwp/design/input/speech-interactions)
- [Diretrizes de comando de voz de realidade mista do Microsoft Windows](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Documentos de referência

* [Divulgação de Talento de Voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Orientações para a implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
* [Como Divulgar](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Passos seguintes

* [Divulgação de Talento de Voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)