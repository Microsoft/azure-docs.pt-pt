---
title: Diretrizes de transcrições rotuladas por pessoas – serviço de fala
titleSuffix: Azure Cognitive Services
description: Se você estiver procurando melhorar a precisão do reconhecimento, especialmente os problemas causados quando palavras são excluídas ou substituídas incorretamente, você desejará usar transcrições com rótulo humano junto com seus dados de áudio. O que são transcrições com rótulo humano? É fácil, eles são palavras-por-palavra, transcrições textuais de um arquivo de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1645e97e5648032a1281e7cb410c42f0a28b6767
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559654"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Como criar transcrições com rótulo humano

Se você estiver procurando melhorar a precisão do reconhecimento, especialmente os problemas causados quando palavras são excluídas ou substituídas incorretamente, você desejará usar transcrições com rótulo humano junto com seus dados de áudio. O que são transcrições com rótulo humano? É fácil, eles são palavras-por-palavra, transcrições textuais de um arquivo de áudio.

Um exemplo grande de dados de transcrição é necessário para melhorar o reconhecimento, sugerimos fornecer entre 10 e 1.000 horas de dados de transcrição. Nesta página, examinaremos as diretrizes criadas para ajudá-lo a criar transcrições de alta qualidade. Este guia é dividido por localidade, com seções para inglês americano, mandarim chinês e alemão.

## <a name="us-english-en-us"></a>Inglês americano (en-US)

As transcrições com rótulo humano para áudio em inglês devem ser fornecidas como texto sem formatação, apenas usando caracteres ASCII. Evite o uso de caracteres de Pontuação latino-1 ou Unicode. Esses caracteres são frequentemente adicionados inadvertidamente ao copiar texto de um aplicativo de processamento de textos ou recorte de dados de páginas da Web. Se esses caracteres estiverem presentes, certifique-se de atualizá-los com a substituição ASCII apropriada.

Eis alguns exemplos:

| Caracteres a serem evitados | Substituição | Notas |
|---------------------|--------------|-------|
| "Olá, mundo" | "Olá, mundo" | As marcas de aspas de abertura e fechamento foram substituídas por caracteres ASCII apropriados. |
| Dia de João | Dia de João | O apóstrofo foi substituído pelo caractere ASCII apropriado. |
| Foi bom – não, foi ótimo! | Foi bom – não, foi ótimo! | O travessão foi substituído por dois hifens. |

### <a name="text-normalization-for-us-english"></a>Normalização de texto para inglês americano

A normalização de texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente. no entanto, é recomendável usar essas diretrizes ao preparar seus dados de transcrição rotulados por pessoas:

* Escreva abreviações em palavras.
* Escreva cadeias de caracteres numéricas não padrão em palavras (como termos de contabilidade).
* Caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritas como pronunciados.
* Abreviações pronunciadas como palavras não devem ser editadas (como "radar", "laser", "RAM" ou "OTAN").
* Escreva abreviações que são pronunciadas como letras separadas com cada letra separada por um espaço.

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| Dr. Faixa de Bruce | Faixa do Doctor Bruce |
| James sulfite, 007 | James sulfite, duplo Oh, sete |
| Ke $ ha | Kesha |
| Quanto tempo é o 2x4 | Por quanto tempo os dois por quatro |
| A reunião vai de 1 a 15h | A reunião vai de um a três PM |
| Meu tipo de sangue é O + | Meu tipo de sangue é O positivo |
| Água é H20 | Água é H 2 O |
| Reproduzir OU812 por Van Halen | Reproduzir O U 8 1 2 por Van Halen |
| UTF-8 com BOM | U T F 8 com BOM |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

* Use letras minúsculas.
* Remova toda a pontuação, exceto apóstrofos dentro de palavras.
* Expanda números em palavras/forma falada, como valores de dólar.

Aqui estão alguns exemplos de normalização executadas automaticamente na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| "Santa vaca!" disse Batman. | Santa vaca disse Batman |
| "O quê?" foi dito o Sidekick, o Robin do Batman. | o que disse o Batman do Sidekick |
| Vá em Get-em! | entrar em Get em |
| Estou em conjunto duplo | Estou em conjunto duplo |
| Rua do 104 Elm | rua do 1 0 4 Elm |
| Ajustar para 102,7 | ajustar para 1 0 2 7 |
| Pi é de cerca de 3,14 | Pi é de aproximadamente três pontos 1 4 |
Custos de ti $3.14| custos de ti 3 14 |

## <a name="mandarin-chinese-zh-cn"></a>Mandarim chinês (ZH-CN)

As transcrições rotuladas pelo homem para áudio mandarim chinês devem ser codificadas em UTF-8 com um marcador de ordem de byte. Evite o uso de caracteres de Pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente quando você prepara os dados em um programa de processamento de texto ou recorta dados de páginas da Web. Se esses caracteres estiverem presentes, certifique-se de atualizá-los com a substituição de largura inteira apropriada.

Eis alguns exemplos:

| Caracteres a serem evitados | Substituição | Notas |
|---------------------|--------------|-------|
| "你好" | "你好" | As marcas de aspas de abertura e fechamento foram substituídas pelos caracteres apropriados. |
| 需要什么帮助? | 需要什么帮助？ | O ponto de interrogação foi substituído pelo caractere apropriado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalização de texto para chinês mandarim

A normalização de texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente. no entanto, é recomendável usar essas diretrizes ao preparar seus dados de transcrição rotulados por pessoas:

* Escreva abreviações em palavras.
* Escreva cadeias de caracteres numéricas na forma falada.

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

* Remover toda a Pontuação
* Expandir números para formulário falado
* Converter letras de largura inteira em letras de meia largura
* Usando letras maiúsculas para todas as palavras em inglês

Aqui estão alguns exemplos de normalização executadas automaticamente na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| 3,1415 | 三 点 一 四 一 五 |
| $3.05 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 implantar 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemão (de-DE) e outras linguagens

As transcrições rotuladas pelo homem para áudio alemão (e outras linguagens de chinês não inglês ou mandarim) devem ser codificadas em UTF-8 com um marcador de ordem de byte. Uma transcrição com rótulo humano deve ser fornecida para cada arquivo de áudio.

### <a name="text-normalization-for-german"></a>Normalização de texto para alemão

A normalização de texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente. no entanto, é recomendável usar essas diretrizes ao preparar seus dados de transcrição rotulados por pessoas:

*   Gravar pontos decimais como "," e não ".".
*   Gravar separadores de tempo como ":" e não "." (por exemplo: 12:00 Uhr).
*   Abreviações como "CA". Não são substituídos. Recomendamos que você use o formulário falado completo.
*   Os quatro principais operadores matemáticos (+, \*-, e/) são removidos. É recomendável substituí-los pelo formato escrito: "mais", "menos," "mal" e "geteilt".
*   Operadores de comparação são removidos (=, < e >). É recomendável substituí-los por "Gleich", "Kleiner ALS" e "grösser ALS".
*   Escreva frações, como 3/4, no formato escrito (por exemplo: "Drei Viertel" em vez de 3/4).
*   Substitua o símbolo "€" pelo formato escrito "euro".

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização do usuário | Texto após a normalização do sistema |
|---------------|-------------------------------|---------------------------------|
| Es ist 12,23 Uhr | Es ist 12:23 Uhr | es ist Zwölf Uhr Drei und zwanzig Uhr |
| {12,45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 mais 3 menos 4 | Zwei Plus Drei menos vier |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

* Use letras minúsculas para todo o texto.
* Remova toda a pontuação, incluindo vários tipos de aspas ("Test", "Test", "Test" e «Test» estão OK).
* Descartar linhas com qualquer caractere especial deste conjunto: ¢ ¤ ¥, ¦ § © ª ¬® ° ± ² μ × ÿize ¬ ¬.
* Expanda números para formulário falado, incluindo valores de moeda ou de euro.
* Aceite apenas tremos de a, o e você. Outras serão substituídas por "th" ou descartadas.

Aqui estão alguns exemplos de normalização executadas automaticamente na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| Anel de Frankfurter | anel de Frankfurter |
| ¡ Eine Defrag! | Eine de desfragmentação |
| wir, haben | wir haben |

## <a name="next-steps"></a>Próximos Passos

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
