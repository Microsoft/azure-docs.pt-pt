---
title: Diretrizes de transcrição para modelos de serviços de voz de formação
titleSuffix: Azure Cognitive Services
description: Saiba como preparar o texto para personalizar acústicos e modelos de linguagem e vozes para os serviços de voz.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 0d7508ed9cf1807fa05c57a1d60c804af7d2244f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897219"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Diretrizes de transcrição para utilizar o serviço de voz

Para personalizar **conversão de voz em texto** ou **texto em voz**, tem de fornecer o texto, juntamente com a conversão de voz. Cada linha no texto corresponde a uma única expressão. O texto deve corresponder a voz mais próximo possível. O texto é chamado um *transcrição*, e deverá criá-la num formato específico.

Os serviços de voz normalizar a entrada para manter o texto consistente.

Este artigo descreve os dois tipos de normalizations. As diretrizes são ligeiramente diferentes para vários idiomas.

## <a name="us-english-en-us"></a>Inglês dos E.U.A. (en-us)

Dados de texto devem ser escritos uma expressão por linha, em texto simples, usando apenas o conjunto de carateres ASCII.

Evite o uso de expandida (Latim 1) ou carateres de pontuação do Unicode. Esses caracteres podem ser incluídos inadvertidamente quando preparar os dados num programa de processamento de texto ou extrair dados de páginas Web. Substitua os caracteres as substituições apropriadas das ASCII. Por exemplo:

| Carateres para evitar | Substituição |
|----- | ----- |
| "Hello world" (abrir e fechar o entre aspas duplas) | "Hello world" (fazer duplo entre aspas duplas) |
| Dia de John (à direita plica) | Dia de John (apóstrofe) |
| isso foi bom — não, ele era ótimo! (em dash) | isso foi bom, não, era ótimo! (hífenes) |

### <a name="text-normalization-rules-for-english"></a>Regras de normalização do texto para inglês

Os serviços de voz realizar as seguintes regras de normalização:

* Utilizar letras minúsculas para todo o texto
* Remover todos os pontuação, exceto apóstrofos internas do word
* Números de expansão para o formulário falado, incluindo quantidades de dólar

Eis alguns exemplos:

| Texto original | Depois de normalização |
|----- | ----- |
| "" Caramba "!" Batman disse. | "caramba" disse batman |
| "E"? sidekick disse Batman, Round Robin. | round robin de sidekick que disse batman |
| Vá obter - em! | Aceda get em |
| Eu sou double-jointed | Eu sou double jointed |
| 104 Elm Street | um AH quatro Elm Rua |
| Ajustar às 102.7 | Otimizar para um AH dois sete |
| Instalador de plataforma está prestes a 3.14 | Instalador de plataforma é aproximadamente três quatro de um de ponto |
| Custa us $3.14 | custa três quatorze |

Aplicam-se a normalização seguinte para suas transcrições de texto:

* Abreviações devem ser escritas em palavras.
* Cadeias de caracteres numéricas não padrão (por exemplo, alguns data ou formulários de contabilidade) devem ser escritas em palavras.
* Palavras com carateres não alfabéticos ou caracteres mistos de alfanuméricos devem ser transcrito tão pronunciados.
* Deixe as abreviaturas que são pronunciadas como palavras inalteradas (por exemplo, "planos", "laser," "RAM" ou "NATO").
* Escrever as letras abreviaturas que são pronunciadas como separadas, com letras separadas por espaços (por exemplo, "IBM," "CPU", "FBI", "TBD" ou "NaN").

Eis alguns exemplos:

| Texto original | Depois de normalização |
|----- | ----- |
| 14 NE 3rd Dr. | unidade Nordeste da terceira quatorze |
| Dr. Bruce faixa | O doutor Bruce faixa |
| James Bond, 007 | James Bond, faça duplo AH sete |
| Ke$ha | Kesha |
| Quanto tempo dura 2 x 4 | Quanto tempo dura a duas por quatro |
| A reunião avança a partir de 1-3 pm | A reunião avança entre uma a três pm |
| é o meu tipo de sangue S + | Meu tipo de sangue será O positivo |
| água é H20 | água é O de 2 de H |
| reproduzir OU812 através de Van Halen | reproduzir O U 2 de 1 de 8 através de Van Halen |
| UTF-8 com BOM | 8 de F T U com BOM |

## <a name="chinese-zh-cn"></a>Chinês (zh-cn)

Dados de texto que são carregados para os serviços de voz personalizada devem usar a codificação UTF-8 com um marcador de ordem de byte. O ficheiro deve ser escrito de uma expressão por linha.

Evite a utilização de carateres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente quando preparar os dados num programa de processamento de texto ou extrair dados de páginas Web. Substituí-los com as substituições apropriadas das largura total. Por exemplo:

| Carateres para evitar | Substituição |
|----- | ----- |
| "你好" (abrir e fechar o entre aspas duplas) | "你好" (entre aspas duplas) |
| 需要什么帮助? (ponto de interrogação) | 需要什么帮助? |

### <a name="text-normalization-rules-for-chinese"></a>Regras de normalização do texto para chinês

Os serviços de voz realizar as seguintes regras de normalização:

* Remover todos os pontuação
* Expandir números ao formulário falado
* Conversão de letras de largura total para as letras de meia largura
* Utilizar letras maiúsculas para todas as palavras em inglês

Eis alguns exemplos:

| Texto original | Depois de normalização |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Antes de importar o seu texto, aplicam-se a normalização seguinte à mesma:

* Abreviações devem ser escritas em palavras (como no formulário falado).
* Escreva cadeias de caracteres numérica no formato falado.

Eis alguns exemplos:

| Texto original | Depois de normalização |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Outros idiomas

Texto de dados carregados para o **conversão de voz em texto** serviço deve usar a codificação UTF-8 com um marcador de ordem de byte. O ficheiro deve ser escrito de uma expressão por linha.

> [!NOTE]
> Os exemplos seguintes utilizam o alemão. No entanto, as diretrizes se aplicam a todos os idiomas que não sejam inglês Americano ou chinês.

### <a name="text-normalization-rules-for-german"></a>Regras de normalização do texto para o alemão

Os serviços de voz realizar as seguintes regras de normalização:

* Utilizar letras minúsculas para todo o texto
* Remover todos os pontuação, incluindo vários tipos de aspas ("teste", "teste", "teste" e «testar» está OK)
* Linhas com qualquer caráter especial de ¢ o conjunto de descarte ¤ ¥ ¦ § © ª ¬® ° ± ² nossa × ÿ Ø¬¬
* Expansão de números a forma de palavra, incluindo dólar ou quantidades de Euro
* Os tremas do aceitar apenas para um, o e; outras pessoas serão substituídas por "th" ou eliminadas

Eis alguns exemplos:

| Texto original | Depois de normalização |
|----- | ----- |
| Anel de Frankfurter | anel de frankfurter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

Antes de importar o seu texto, aplicam-se a normalização seguinte à mesma:

* Pontos decimais devem ser ","e não".".
* Separadores de tempo entre as horas e minutos devem ser ":"e não"." (por exemplo, 12:00 Uhr).
* Abreviações como "ca". não são substituídos. Recomendamos que utilize o formulário completo.
* Os operadores de matemáticos principais quatro (+, -, \*, e /) são removidos. Recomendamos que substituí-los com o formato de literal: "Além disso," "menos," "mal" e "geteilt."
* A mesma regra se aplica a operadores de comparação (=, <, e >). Recomendamos que substituí-los com "gleich", "kleiner als," e "grösser als."
* Utilize frações, tais como 3/4, na forma de palavra (por exemplo, "drei viertel" em vez de ¾).
* Substituir o símbolo ' € com o formulário do word "Euro."

Eis alguns exemplos:

| Texto original | Depois de normalização do utilizador | Depois de normalização de sistema |
|--------  | ----- | -------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 e 3 menos 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação dos Serviços de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer voz em C#](quickstart-csharp-dotnet-windows.md)
