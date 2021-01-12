---
title: Diretrizes de transcrições com rótulo humano - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Para melhorar a precisão do reconhecimento da fala, como quando as palavras são eliminadas ou substituídas incorretamente, pode utilizar transcrições com rótulo humano juntamente com os seus dados áudio. Transcrições com rótulo humano são transcrições palavra a palavra, transcrições verbatim de um ficheiro áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cc373a67ee0f4ed2e900e7b41cbcb96d31d67b34
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98126891"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Como criar transcrições com rótulo humano

Se procura melhorar a precisão do reconhecimento, especialmente os problemas que são causados quando as palavras são eliminadas ou substituídas incorretamente, irá querer utilizar transcrições com rótulo humano juntamente com os seus dados áudio. O que são transcrições com rótulo humano? É fácil, são transcrições verbais de um ficheiro áudio.

Uma grande amostra de dados de transcrição é necessária para melhorar o reconhecimento, sugerimos fornecer entre 10 a 20 horas de dados de transcrição. Nesta página, vamos rever as diretrizes desenhadas para ajudá-lo a criar transcrições de alta qualidade. Este guia é dividido por localidade, com secções para inglês americano, mandarim chinês e alemão.

> [!NOTE]
> Nem todos os modelos base suportam a personalização com ficheiros áudio. Se um modelo base não o suportar, a formação utilizará apenas o texto das transcrições da mesma forma que o texto relacionado é utilizado.

## <a name="us-english-en-us"></a>Inglês dos EUA (en-US)

As transcrições com rótulo humano para áudio inglês devem ser fornecidas como texto simples, utilizando apenas caracteres ASCII. Evite a utilização de caracteres de pontuação Latino-1 ou Unicode. Estes caracteres são muitas vezes adicionados inadvertidamente ao copiar texto de uma aplicação de processamento de palavras ou raspar dados de páginas web. Se estes caracteres estiverem presentes, certifique-se de atualizá-los com a substituição ASCII adequada.

Eis alguns exemplos:

| Personagens a evitar | Substitution | Notas |
| ------------------- | ------------ | ----- |
| "Olá mundo" | "Hello world" | As aspas de abertura e de fecho foram substituídas por caracteres ASCII adequados. |
| Dia de João | Dia de João | O apóstrofo foi substituído pelo carácter ASCII adequado. |
| foi bom - não, foi grande! | Foi bom- não, foi ótimo! | O traço em foi substituído por dois hífenes. |

### <a name="text-normalization-for-us-english"></a>Normalização de texto para inglês dos EUA

A normalização do texto é a transformação das palavras num formato consistente utilizado na formação de um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos a utilização destas diretrizes enquanto prepara os seus dados de transcrição com rótulo humano:

- Escreva abreviaturas em palavras.
- Escreva cordas numéricas não padrão em palavras (tais como termos contabilísticos).
- Os caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritos como pronunciados.
- Abreviaturas que são pronunciadas como palavras não devem ser editadas (como "radar", "laser", "RAM" ou "NATO").
- Escreva abreviaturas que são pronunciadas como letras separadas com cada letra separada por um espaço.

Aqui estão alguns exemplos de normalização que deve realizar na transcrição:

| Texto original               | Texto após normalização              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Dr. Bruce Banner                   |
| James Bond, 007             | James Bond, duplo oh sete           |
| Ke$ha                       | Rio Kesha                                 |
| Quanto tempo é o 2x4         | Quanto tempo são os dois por quatro           |
| A reunião vai das 13h00 às 15h00. | A reunião vai de uma a três horas. |
| O meu tipo sanguíneo é O+         | O meu tipo sanguíneo é O positivo.           |
| Água é H20                | Água é H 2 O                        |
| Jogo OU812 por Van Halen     | Jogar O U 8 12 por Van Halen           |
| UTF-8 with BOM              | U T F 8 com BOM                      |

As seguintes regras de normalização são automaticamente aplicadas às transcrições:

- Use letras minúsculas.
- Remova todas as pontuações exceto apóstrofos dentro das palavras.
- Expandir os números em palavras/formas faladas, tais como valores em dólares.

Aqui estão alguns exemplos de normalização realizadas automaticamente na transcrição:

| Texto original                          | Texto após normalização          |
| -------------------------------------- | --------------------------------- |
| "Vaca sagrada!" disse Batman.               | vaca sagrada disse batman              |
| "O que?" disse o ajudante de Batman, Robin. | o que disse robin ajudante batman |
| Vai buscá-los!                            | ir em                         |
| Tenho dupla articulação.                     | Sou duplamente articulado.                |
| Rua Elm, 104                         | uma oh quatro rua Elm            |
| Sintonização para 102.7                          | sintonizar a um oh dois ponto sete    |
| Pi é cerca de 3.14                       | pi é cerca de três ponto um quatro  |
| Custa \$ 3,14                        | custa três catorze           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarim chinês (zh-CN)

As transcrições com rótulo humano para áudio chinês mandarim devem ser UTF-8 codificadas com um marcador de encomenda. Evite a utilização de caracteres de pontuação de meia largura. Estes caracteres podem ser incluídos inadvertidamente quando preparam os dados num programa de processamento de palavras ou raspam dados a partir de páginas web. Se estes caracteres estiverem presentes, certifique-se de atualizá-los com a substituição adequada de largura total.

Eis alguns exemplos:

| Personagens a evitar | Substitution   | Notas |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | As aspas de abertura e de fecho foram substituídas por caracteres apropriados. |
| 需要什么帮助? | 需要什么帮助?| O ponto de interrogação foi substituído pelo carácter apropriado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalização de texto para mandarim chinês

A normalização do texto é a transformação das palavras num formato consistente utilizado na formação de um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos a utilização destas diretrizes enquanto prepara os seus dados de transcrição com rótulo humano:

- Escreva abreviaturas em palavras.
- Escreva cordas numéricas na forma falada.

Aqui estão alguns exemplos de normalização que deve realizar na transcrição:

| Texto original | Texto após normalização |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

As seguintes regras de normalização são automaticamente aplicadas às transcrições:

- Remover toda a pontuação
- Expandir os números para a forma falada
- Converter letras de largura total em letras de meia largura
- Usando letras maiúsculas para todas as palavras inglesas

Aqui estão alguns exemplos de normalização realizadas automaticamente na transcrição:

| Texto original | Texto após normalização |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ≤ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemão (de-DE) e outras línguas

As transcrições com rótulo humano para áudio alemão (e outras línguas chinesas não inglesas ou mandarim) devem ser UTF-8 codificadas com um marcador de encomenda. Deve ser fornecida uma transcrição com rótulo humano para cada ficheiro áudio.

### <a name="text-normalization-for-german"></a>Normalização de texto para alemão

A normalização do texto é a transformação das palavras num formato consistente utilizado na formação de um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos a utilização destas diretrizes enquanto prepara os seus dados de transcrição com rótulo humano:

- Escreva pontos decimais como "" e não "."
- Escreva separadores de tempo como " e não "." (por exemplo: 12:00 Uhr).
- Abreviaturas como "ca". não são substituídos. Recomendamos que utilize o formulário falado completo.
- Os quatro principais operadores matemáticos (+, -, \* e/) são removidos. Recomendamos substituí-los pela forma escrita: "plus", "menos", "mal" e "geteilt".
- Os operadores de comparação são removidos (=, < e >). Recomendamos substituí-los por "gleich", "kleiner als" e "grösser als".
- Escreva frações, como 3/4, em forma escrita (por exemplo: "drei viertel" em vez de 3/4).
- Substitua o símbolo "€" pela sua forma escrita "Euro".

Aqui estão alguns exemplos de normalização que deve realizar na transcrição:

| Texto original    | Texto após normalização do utilizador | Texto após normalização do sistema       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 mais 3 menos 4              | zwei mais drei menos vier             |

As seguintes regras de normalização são automaticamente aplicadas às transcrições:

- Utilize letras minúsculas para todos os textos.
- Remova todas as pontuações, incluindo vários tipos de marcas de aspas ("teste", "teste", "teste" e "teste" são OK).
- Deite fora as linhas com caracteres especiais deste conjunto: ≤ ≤ § © ≤ ®º ± ² μ × Ø≤..
- Expandir os números para formulário falado, incluindo valores em dólar ou euro.
- Aceite umlauts apenas para um, o, e você. Outros serão substituídos por "th" ou serão descartados.

Aqui estão alguns exemplos de normalização realizadas automaticamente na transcrição:

| Texto original    | Texto após normalização |
| ---------------- | ------------------------ |
| Anel de Frankfurter | anel frankfurter         |
| Eine Frage!     | frage eine               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Passos Seguintes

- [Prepare e teste os seus dados](./how-to-custom-speech-test-and-train.md)
- [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
- [Preparar o seu modelo](how-to-custom-speech-train-model.md)
- [Implemente o seu modelo](./how-to-custom-speech-train-model.md)