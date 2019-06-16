---
title: Diretrizes de etiqueta humanos transcrições - serviços de voz
titlesuffix: Azure Cognitive Services
description: Se estiver procurando para melhorar a precisão de reconhecimento, especialmente os problemas causados quando palavras são excluídas ou substituídas incorretamente, desejará usar com etiqueta humanos transcrições juntamente com os seus dados de áudio. Quais são etiquetados por humanos transcrições? Isso é fácil, eles são transcrições palavra por palavra, textuais de um arquivo de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ba6d137a7eb5fc2c56bfc39aa8f831d76411fb46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060865"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Como criar etiqueta humanos transcrições

Se estiver procurando para melhorar a precisão de reconhecimento, especialmente os problemas causados quando palavras são excluídas ou substituídas incorretamente, desejará usar com etiqueta humanos transcrições juntamente com os seus dados de áudio. Quais são etiquetados por humanos transcrições? Isso é fácil, eles são transcrições palavra por palavra, textuais de um arquivo de áudio.

Um exemplo de grandes de dados de transcrição é necessário para melhorar o reconhecimento, sugerimos que fornecer entre 10 e 1000 horas de dados de transcrição. Nesta página, vamos rever diretrizes concebidas para ajudar a criar transcrições de alta qualidade. Este guia é dividido por Localidade, com seções para inglês Americano, Mandarim chinês e alemão.

## <a name="us-english-en-us"></a>Inglês dos Estados Unidos (en-US)

Transcrições de etiqueta humanos para áudio em inglês, devem ser fornecidas como texto simples, usando apenas carateres ASCII. Evite a utilização de carateres de pontuação Latin-1 ou Unicode. Estes carateres inadvertidamente, muitas vezes, são adicionados ao copiar o texto a partir de um aplicativo de processamento de texto ou a captura de dados a partir de páginas da web. Se estes carateres estiverem presentes, certifique-se de atualizá-los com a substituição de ASCII adequada.

Eis alguns exemplos:

| Carateres para evitar | Substituição | Notas |
|---------------------|--------------|-------|
| "Hello world" | "Hello world" | As marcas de cotações chavetas tem foi substituídas com caracteres de ASCII adequados. |
| Dia de João | Dia de João | O apóstrofe foi substituído com os carateres ASCII adequados. |
| isso foi bom — não, ele era ótimo! | isso foi bom, não, era ótimo! | O traço em foi substituído com dois hífenes. |

### <a name="text-normalization-for-us-english"></a>Normalização de texto para inglês Americano

Normalização do texto é a transformação de palavras num formato consistente utilizada quando preparar um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos a utilização estas diretrizes para preparar seus dados de transcrição de etiqueta humanos:

* Escreva abreviaturas em palavras.
* Escreva cadeias de caracteres numéricos não padrão em palavras (por exemplo, termos de contabilidade).
* Devem ser transcrito carateres não alfabéticos ou caracteres mistos de alfanuméricos tão pronunciados.
* Não devem ser editadas abreviaturas que são pronunciadas como palavras (por exemplo, "planos", "laser", "RAM" ou "NATO").
* Escrita as abreviaturas que são pronunciado como letras separadas por cada letra separada por um espaço.

Aqui estão alguns exemplos de normalização que deverá efetuar na transcrição:

| Texto original | Texto depois do normalização |
|---------------|--------------------------|
| Dr. Bruce faixa | O doutor Bruce faixa |
| James Bond, 007 | James Bond, faça duplo AH sete |
| Ke$ha | Kesha |
| Quanto tempo dura 2 x 4 | Quanto tempo dura a duas por quatro |
| A reunião avança a partir de 1-3 pm | A reunião avança entre uma a três pm |
| é o meu tipo de sangue S + | Meu tipo de sangue será O positivo |
| água é H20 | água é O de 2 de H |
| reproduzir OU812 através de Van Halen | reproduzir O U 2 de 1 de 8 através de Van Halen |
| UTF-8 com BOM | 8 de F T U com BOM |

As seguintes regras de normalização são automaticamente aplicadas no transcrições:

* Utilize letras minúsculas.
* Remova todos os pontuação, exceto os apóstrofos dentro palavras.
* Expanda números para o formulário palavras/falado, tais como quantidades de dólar.

Aqui estão alguns exemplos de normalização executada automaticamente na transcrição:

| Texto original | Texto depois do normalização |
|---------------|--------------------------|
| "" Caramba "!" Batman disse. | "caramba" disse batman |
| "E"? sidekick disse Batman, Round Robin. | round robin de sidekick que disse batman |
| Vá obter - em! | Aceda get em |
| Eu sou double-jointed | Eu sou double jointed |
| 104 Elm Street | um AH quatro Elm Rua |
| Ajustar às 102.7 | Otimizar para um AH dois sete |
| Instalador de plataforma está prestes a 3.14 | Instalador de plataforma é aproximadamente três quatro de um de ponto |
Custa us $3.14| custa três quatorze |

## <a name="mandarin-chinese-zh-cn"></a>Mandarim chinês (zh-CN)

Transcrições de etiqueta humanos para áudio de chinês Mandarim tem de ser codificado com um marcador de ordem de byte de UTF-8. Evite a utilização de carateres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente quando preparar os dados num programa de processamento de texto ou extrair dados de páginas da web. Se estes carateres estiverem presentes, certifique-se de atualizá-los com a substituição de largura total apropriada.

Eis alguns exemplos:

| Carateres para evitar | Substituição | Notas |
|---------------------|--------------|-------|
| "你好" | "你好" | Marcas de cotações de chavetas tem sido substituídas pelo carateres certos. |
| 需要什么帮助? | 需要什么帮助? | O ponto de interrogação foi substituído por caráter apropriado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalização de texto para Mandarim chinês

Normalização do texto é a transformação de palavras num formato consistente utilizada quando preparar um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos a utilização estas diretrizes para preparar seus dados de transcrição de etiqueta humanos:

* Escreva abreviaturas em palavras.
* Escreva cadeias de caracteres numérica no formato falado.

Aqui estão alguns exemplos de normalização que deverá efetuar na transcrição:

| Texto original | Texto depois do normalização |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

As seguintes regras de normalização são automaticamente aplicadas no transcrições:

* Remover todos os pontuação
* Expanda números ao formulário falado
* Converter a largura total letras em letras de meia largura
* Utilizar letras maiúsculas para todas as palavras em inglês

Aqui estão alguns exemplos de normalização executada automaticamente na transcrição:

| Texto original | Texto depois do normalização |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemão (Alemanha-DE) e outras linguagens

Transcrições de etiqueta humanos para áudio alemão (e outros que não o inglês ou as línguas chinesas Mandarim) tem de ser codificado com um marcador de ordem de byte de UTF-8. Deve fornecer uma transcrição de etiqueta humanos para cada arquivo de áudio.

### <a name="text-normalization-for-german"></a>Normalização de texto para o alemão

Normalização do texto é a transformação de palavras num formato consistente utilizada quando preparar um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos a utilização estas diretrizes para preparar seus dados de transcrição de etiqueta humanos:

*   Escrever pontos decimais como ","e não".".
*   Escrever separadores de hora como ":"e não"." (por exemplo: 12:00 Uhr).
*   Abreviações como "ca". não são substituídos. Recomendamos que utilize o formulário falado completo.
*   Os operadores de matemáticos principais quatro (+, -, \*, e /) são removidos. Recomendamos que substituí-los com a forma escrita: "Além disso," "menos," "mal" e "geteilt."
*   Operadores de comparação são removidas (=, <, e >). Recomendamos que substituí-los com "gleich", "kleiner als," e "grösser als."
*   Escrever frações, tais como 3/4, de forma escrita (por exemplo: "drei viertel" em vez de 3/4).
*   Substituir o símbolo de "€" com a forma escrita "Euro."

Aqui estão alguns exemplos de normalização que deverá efetuar na transcrição:

| Texto original | Texto depois de normalização de utilizador | Texto depois de normalização de sistema |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 e 3 menos 4 | zwei plus drei minus vier |

As seguintes regras de normalização são automaticamente aplicadas no transcrições:

* Utilize letras minúsculas para todo o texto.
* Remover todos os pontuação, incluindo vários tipos de aspas ("teste", "teste", "teste" e «testar» está OK).
* Eliminar linhas com carateres especiais a partir desse conjunto: ¢ ¤ ¥ ¦ § © ª ¬® ° ± ² nossa × ÿ Ø¬¬.
* Expanda números para o formulário falado, incluindo dólar ou quantidades Euro.
* Aceitar os tremas do apenas para um, o e. Outras pessoas serão substituídas por "th" ou rejeitadas.

Aqui estão alguns exemplos de normalização executada automaticamente na transcrição:

| Texto original | Texto depois do normalização |
|---------------|--------------------------|
| Anel de Frankfurter | anel de frankfurter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>Próximos Passos

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)
