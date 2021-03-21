---
title: Preparar dados para Discurso Personalizado - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Ao testar a precisão do reconhecimento de voz da Microsoft ou treinar os seus modelos personalizados, necessitará de dados de áudio e texto. Nesta página, cobrimos os tipos de dados, como usá-los e geri-los.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 2c98546d20e9f977a605ccbac21010aa9b1dbadc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232499"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para Voz Personalizada

Ao testar a precisão do reconhecimento de voz da Microsoft ou treinar os seus modelos personalizados, necessitará de dados de áudio e texto. Nesta página, cobrimos os tipos de dados que um modelo de fala personalizado necessita.

## <a name="data-diversity"></a>Diversidade de dados

Texto e áudio usados para testar e treinar um modelo personalizado precisam incluir amostras de um conjunto diversificado de alto-falantes e cenários que você precisa que o seu modelo reconheça.
Considere estes fatores ao recolher dados para testes e treino de modelos personalizados:

* Os dados de áudio de texto e de fala precisam de cobrir os tipos de declarações verbais que os seus utilizadores farão quando interagirem com o seu modelo. Por exemplo, um modelo que aumente e reduza a temperatura precisa de formação em declarações que as pessoas podem fazer para solicitar tais alterações.
* Os seus dados precisam de incluir todas as variações de fala que o seu modelo terá de reconhecer. Muitos fatores podem variar a fala, incluindo sotaques, dialetos, mistura de linguagem, idade, sexo, voz, nível de stress e hora do dia.
* Deve incluir amostras de diferentes ambientes (interior, exterior, ruído de estrada) onde o seu modelo será utilizado.
* O áudio deve ser recolhido utilizando dispositivos de hardware que o sistema de produção utilizará. Se o seu modelo necessitar de identificar a fala gravada em dispositivos de gravação de qualidade variada, os dados áudio que fornece para treinar o seu modelo também devem representar estes diferentes cenários.
* Pode adicionar mais dados ao seu modelo mais tarde, mas tenha o cuidado de manter o conjunto de dados diversificado e representativo das necessidades do seu projeto.
* A inclusão de dados que *não* estão dentro das suas necessidades de reconhecimento personalizadas pode prejudicar a qualidade do reconhecimento em geral, pelo que não inclua dados que o seu modelo não precisa de transcrever.

Um modelo treinado num subconjunto de cenários só pode ter um bom desempenho nesses cenários. Escolha cuidadosamente os dados que representam o alcance completo dos cenários que precisa que o seu modelo personalizado reconheça.

> [!TIP]
> Comece com pequenos conjuntos de dados de amostra que correspondam à linguagem e acústica que o seu modelo irá encontrar.
> Por exemplo, grave uma pequena mas representativa amostra de áudio no mesmo hardware e no mesmo ambiente acústico que o seu modelo encontrará em cenários de produção.
> Pequenos conjuntos de dados de dados representativos podem expor problemas antes de ter investido na recolha de conjuntos de dados muito maiores para a formação.
>
> Para começar rapidamente, considere usar dados da amostra. Consulte este repositório GitHub para <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">amostra de dados de discurso personalizado </a>

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceites, quando cada tipo de dados deve ser utilizado, e a quantidade recomendada. Nem todos os tipos de dados são necessários para criar um modelo. Os requisitos de dados variam consoante esteja a criar um teste ou a treinar um modelo.

| Tipo de dados | Utilizado para testes | Quantidade recomendada | Usado para treino | Quantidade recomendada |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Yes<br>Usado para inspeção visual | 5+ ficheiros áudio | No | N/D |
| [Transcrições com rótulo humano + áudio +](#audio--human-labeled-transcript-data-for-testingtraining) | Yes<br>Usado para avaliar a precisão | 0,5-5 horas de áudio | Yes | 1-20 horas de áudio |
| [Texto relacionado](#related-text-data-for-training) | No | N/a | Yes | 1-200 MB de texto relacionado |

Os ficheiros devem ser agrupados por tipo num conjunto de dados e carregados como um ficheiro .zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!TIP]
> Quando treinar um novo modelo, comece com [textos relacionados.](#related-text-data-for-training) Estes dados já melhorarão o reconhecimento de termos e frases especiais. Treinar com texto é muito mais rápido do que treinar com áudio (minutos vs. dias).

> [!NOTE]
> Nem todos os modelos base suportam o treino com áudio. Se um modelo base não o suportar, o serviço Desemaguiso utilizará apenas o texto das transcrições e ignorará o áudio. Consulte [o suporte linguístico](language-support.md#speech-to-text) para uma lista de modelos base que suportam a formação com dados áudio. Mesmo que um modelo base suporte a formação com dados áudio, o serviço pode usar apenas uma parte do áudio. Ainda assim, vai usar todas as transcrições.
>
> Nos casos em que altera o modelo base utilizado para o treino, e tem áudio no conjunto de dados de treino, verifique *sempre* se o novo modelo base selecionado [suporta a formação com dados áudio](language-support.md#speech-to-text). Se o modelo base anteriormente utilizado não suportasse a formação com dados áudio, e o conjunto de dados de formação contiver áudio, o tempo de treino com o novo modelo base aumentará **drasticamente,** podendo facilmente passar de várias horas para vários dias e mais. Isto é especialmente verdade se a subscrição do seu serviço Desem declarações **não** estiver numa [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para a formação.
>
> Se encarar a questão descrita no parágrafo acima, pode diminuir rapidamente o tempo de treino reduzindo a quantidade de áudio no conjunto de dados ou removendo-o completamente e deixando apenas o texto. Esta última opção é altamente recomendada se a subscrição do serviço Speech **não** estiver numa [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para a formação.
>
> Em regiões com hardware dedicado para formação, o serviço De Discurso utilizará até 20 horas de áudio para treinar. Noutras regiões, só utilizará até 8 horas de áudio.

## <a name="upload-data"></a>Carregar dados

Para fazer o upload dos seus dados, navegue para o <a href="https://speech.microsoft.com/customspeech" target="_blank">Estúdio da Fala. </a> A partir do portal, clique em **Carregar dados** para lançar o assistente e criar o seu primeiro conjunto de dados. Será solicitado que selecione um tipo de dados de voz para o seu conjunto de dados, antes de lhe permitir fazer o upload dos seus dados.

![Screenshot que realça a opção de upload áudio a partir do Portal do Discurso.](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que fizer o upload deve satisfazer os requisitos para o tipo de dados que escolher. Os seus dados devem ser corretamente formatados antes de serem carregados. Dados formatados corretamente garantem que será processado com precisão pelo serviço Desemedicado. Os requisitos estão listados nas seguintes secções.

Depois do seu conjunto de dados ser carregado, tem algumas opções:

* Pode navegar para o **separador Teste** e inspecionar visualmente apenas áudio ou áudio + dados de transcrição com rótulo humano.
* Pode navegar para o **separador Training** e utilizar dados de transcrição áudio + humanos ou dados de texto relacionados para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados áudio para testes

Os dados áudio são ideais para testar a precisão do modelo de fala-texto da Microsoft ou um modelo personalizado. Tenha em mente que os dados áudio são utilizados para inspecionar a precisão da fala no que diz respeito ao desempenho de um modelo específico. Se pretende quantificar a precisão de um modelo, utilize dados de [transcrição áudio + com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining).

Utilize esta tabela para garantir que os seus ficheiros áudio são formatados corretamente para utilização com Discurso Personalizado:

| Propriedade                 | Valor                 |
|--------------------------|-----------------------|
| Formato do ficheiro              | RIFF (WAV)            |
| Taxa de amostra              | 8.000 Hz ou 16.000 Hz |
| Canais                 | 1 (mono)              |
| Comprimento máximo por áudio | Duas horas               |
| Sample format (Formato de exemplo)            | PCM, 16-bit           |
| Formato de arquivo           | .zip                  |
| Tamanho máximo do arquivo     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Ao carregar dados de treino e teste, o tamanho do ficheiro .zip não pode exceder 2 GB. Se necessitar de mais dados para a formação, divida-os em vários ficheiros .zip e faça o upload separadamente. Mais tarde, pode optar por treinar a partir de *vários* conjuntos de dados. No entanto, só é possível testar a partir de um *único* conjunto de dados.

Utilize <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">o SoX </a> para verificar as propriedades áudio ou converter o áudio existente nos formatos apropriados. Abaixo estão alguns exemplos de como cada uma destas atividades pode ser feita através da linha de comando SoX:

| Atividade | Description | Comando SoX |
|----------|-------------|-------------|
| Verifique o formato áudio | Use este comando para verificar<br>o formato de ficheiro áudio. | `sox --i <filename>` |
| Converter formato áudio | Use este comando para converter<br>o ficheiro áudio para um único canal, 16-bit, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dados de transcrição com rótulo humano + para testes/formação

Para medir a precisão da precisão fala-a-texto da Microsoft ao processar os seus ficheiros áudio, deve fornecer transcrições com rótulo humano (palavra a palavra) para comparação. Embora a transcrição com rótulo humano seja muitas vezes morosa, é necessário avaliar a precisão e treinar o modelo para os seus casos de uso. Tenha em mente que as melhorias no reconhecimento só serão tão boas quanto os dados fornecidos. Por essa razão, é importante que apenas transcrições de alta qualidade sejam carregadas.

Os ficheiros áudio podem ter silêncio no início e no fim da gravação. Se possível, inclua pelo menos meio segundo de silêncio antes e depois da fala em cada ficheiro de amostra. Embora o áudio com baixo volume de gravação ou ruído de fundo disruptivo não seja útil, não deve prejudicar o seu modelo personalizado. Considere sempre atualizar os microfones e o hardware de processamento de sinais antes de recolher amostras de áudio.

| Propriedade                 | Valor                               |
|--------------------------|-------------------------------------|
| Formato do ficheiro              | RIFF (WAV)                          |
| Taxa de amostra              | 8.000 Hz ou 16.000 Hz               |
| Canais                 | 1 (mono)                            |
| Comprimento máximo por áudio | 2 horas (testes) / 60 s (treino) |
| Sample format (Formato de exemplo)            | PCM, 16-bit                         |
| Formato de arquivo           | .zip                                |
| Tamanho máximo do fecho de correr         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Ao carregar dados de treino e teste, o tamanho do ficheiro .zip não pode exceder 2 GB. Só pode testar a partir de um *único* conjunto de dados, certifique-se de mantê-lo dentro do tamanho apropriado do ficheiro. Além disso, cada ficheiro de treino não pode exceder 60 segundos, caso contrário, irá errar.

Para resolver questões como a eliminação ou substituição de palavras, é necessária uma quantidade significativa de dados para melhorar o reconhecimento. Geralmente, é recomendado fornecer transcrições palavra-a-palavra para 1 a 20 horas de áudio. No entanto, mesmo que apenas 30 minutos possam ajudar a melhorar os resultados do reconhecimento. As transcrições para todos os ficheiros WAV devem estar contidas num único ficheiro de texto simples. Cada linha do ficheiro de transcrição deve ter o nome de um dos ficheiros de áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t).

Por exemplo:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> A transcrição deve ser codificada como UTF-8 byte order mark (BOM).

As transcrições são normalizadas para texto, de modo a que o sistema as possa processar. No entanto, existem algumas normalizações importantes que devem ser feitas antes de enviar os dados para o Estúdio da Fala. Para que a língua apropriada utilize quando prepara as suas transcrições, consulte [Como criar uma transcrição com rótulo humano](how-to-custom-speech-human-labeled-transcriptions.md)

Depois de ter recolhido os seus ficheiros áudio e transcrições correspondentes, embale-os como um único ficheiro .zip antes de enviar para o Estúdio da <a href="https://speech.microsoft.com/customspeech" target="_blank">Fala </a>. Abaixo está um conjunto de dados de exemplo com três ficheiros áudio e um ficheiro de transcrição com rótulo humano:

> [!div class="mx-imgBorder"]
> ![Selecione áudio do Portal do Discurso](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Consulte [configurar a sua conta Azure](custom-speech-overview.md#set-up-your-azure-account) para obter uma lista de regiões recomendadas para as suas subscrições de serviço de Discurso. A criação das assinaturas Discurso numa destas regiões reduzirá o tempo necessário para formar o modelo. Nestas regiões, a formação pode processar cerca de 10 horas de áudio por dia em comparação com apenas 1 hora por dia noutras regiões. Se a formação de modelos não puder ser concluída dentro de uma semana, o modelo será marcado como falhado.

Nem todos os modelos base suportam o treino com dados áudio. Se o modelo base não o suportar, o serviço ignorará o áudio e apenas treinará com o texto das transcrições. Neste caso, a formação será a mesma que a formação com texto relacionado. Consulte [o suporte linguístico](language-support.md#speech-to-text) para uma lista de modelos base que suportam a formação com dados áudio.

## <a name="related-text-data-for-training"></a>Dados de texto relacionados para a formação

Os nomes ou funcionalidades do produto que são únicos, devem incluir dados de texto relacionados para a formação. O texto relacionado ajuda a garantir o reconhecimento correto. Podem ser fornecidos dois tipos de dados de texto relacionados para melhorar o reconhecimento:

| Tipo de dados | Como estes dados melhoram o reconhecimento |
|-----------|------------------------------------|
| Frases (expressões) | Melhore a precisão ao reconhecer os nomes dos produtos ou o vocabulário específico da indústria no contexto de uma frase. |
| Pronúncias | Melhorar a pronúncia de termos incomuns, siglas ou outras palavras com pronúncias indefinidas. |

As frases podem ser fornecidas como um único ficheiro de texto ou vários ficheiros de texto. Para melhorar a precisão, utilize dados de texto mais próximos das declarações faladas esperadas. As pronúncias devem ser fornecidas como um único ficheiro de texto. Tudo pode ser embalado como um único ficheiro zip e enviado para o <a href="https://speech.microsoft.com/customspeech" target="_blank">Estúdio de Fala </a>.

O treino com texto relacionado geralmente completa em poucos minutos.

### <a name="guidelines-to-create-a-sentences-file"></a>Diretrizes para criar um ficheiro de frases

Para criar um modelo personalizado usando frases, você precisará fornecer uma lista de expressões de amostras. As expressões _não_ precisam de ser completas ou gramaticalmente corretas, mas devem refletir com precisão a entrada falada que se espera na produção. Se quiser que determinados termos tenham um peso acrescido, adicione várias frases que incluem estes termos específicos.

Como orientação geral, a adaptação do modelo é mais eficaz quando o texto de formação está o mais próximo possível do texto real esperado na produção. Jargão específico de domínio e frases que você está direcionando para melhorar, devem ser incluídas em texto de treino. Quando possível, tente controlar uma frase ou palavra-chave numa linha separada. Para palavras-chave e frases que sejam importantes para si (por exemplo, nomes de produtos), pode copiá-las algumas vezes. Mas lembre-se, não copie muito - pode afetar a taxa de reconhecimento geral.

Utilize esta tabela para garantir que o seu ficheiro de dados relacionado para expressões seja formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM |
| N.º de expressões por linha | 1 |
| Tamanho máximo do ficheiro | 200 MB |

Além disso, você vai querer prestar contas das seguintes restrições:

* Evite repetir caracteres, palavras ou grupos de palavras mais de três vezes. Por exemplo: "aaaa", "sim sim sim sim", ou "é isso que é que é isto". O serviço de discurso pode deixar cair linhas com demasiadas repetições.
* Não utilize caracteres especiais ou caracteres UTF-8 acima `U+00A1` .
* UrIs será rejeitado.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um ficheiro de pronúncia

Se existirem termos incomuns sem pronúncias padrão que os seus utilizadores irão encontrar ou usar, pode fornecer um ficheiro de pronúncia personalizado para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é aconselhável utilizar ficheiros de pronúncia personalizados para alterar a pronúncia das palavras comuns.

Isto inclui exemplos de uma expressão falada, e uma pronúncia personalizada para cada um:

| Formulário reconhecido/exibido | Formulário falado |
|--------------|--------------------------|
| 3CPO | três c p o |
| CNTK | c n t k |
| IEEE | i triplo e |

A forma falada é a sequência fonética escrita. Pode ser composto por letra, palavras, sílabas, ou uma combinação de todos os três.

A pronúncia personalizada está disponível em inglês `en-US` e alemão ( em inglês) e em alemão `de-DE` . Esta tabela mostra caracteres suportados pela linguagem:

| Linguagem | Região | Personagens |
|----------|--------|------------|
| Inglês | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Alemão | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Utilize a seguinte tabela para garantir que o seu ficheiro de dados relacionado para pronúncias está corretamente formatado. Os ficheiros de pronúncia são pequenos, e devem ter apenas alguns quilobytes de tamanho.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM (ANSI também é apoiado para inglês) |
| # de pronúncias por linha | 1 |
| Tamanho máximo do ficheiro | 1 MB (1 KB para nível livre) |

## <a name="next-steps"></a>Passos seguintes

* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implemente o seu modelo](./how-to-custom-speech-train-model.md)