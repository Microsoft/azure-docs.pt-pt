---
title: Conceitos de Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Conceitos básicos usados no serviço de fala da Microsoft.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965703"
---
# <a name="basic-concepts"></a>Conceitos básicos

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Esta página descreve alguns conceitos básicos do serviço de reconhecimento de fala da Microsoft. Recomendamos que você leia esta página antes de usar a API de reconhecimento de fala da Microsoft em seu aplicativo.

## <a name="understanding-speech-recognition"></a>Noções básicas sobre reconhecimento de fala

Se esta for a primeira vez que você está criando um aplicativo habilitado para fala, ou se for a primeira vez que você estiver adicionando recursos de fala a um aplicativo existente, esta seção ajudará você a começar. Se você já tem alguma experiência com aplicativos habilitados para fala, você pode optar por apenas dar uma olhada nesta seção ou pode ignorá-la inteiramente se você tiver uma mão antiga na fala e desejar chegar diretamente aos detalhes do protocolo.

### <a name="audio-streams"></a>Fluxos de áudio

A principal relação entre os conceitos básicos de fala é o *fluxo de áudio*. Ao contrário de um pressionamento de tecla, que ocorre em um único ponto no tempo e contém uma única informação, uma solicitação falada é distribuída por centenas de milissegundos e contém muitos quilobytes de informações. A duração do declarações falado apresenta uma dificuldade aos desenvolvedores que buscam fornecer uma experiência de fala simplificada e elegante para seu aplicativo. Os computadores e algoritmos atuais executam transcrição de fala em aproximadamente metade da duração do expressão, portanto, uma expressão de 2 segundos pode ser transcrita em aproximadamente 1 segundo, mas qualquer aplicativo que tenha um atraso de 1 segundo no processamento do usuário é Nem simplificado nem elegante.

Felizmente, há maneiras de "Ocultar" o tempo de transcrição executando a transcrição em uma parte do expressão enquanto o usuário está falando de outra parte. Por exemplo, dividindo um expressão de 1 segundo em 10 partes de 100 milissegundos e executando a transcrição em cada parte por vez, mais de 450 do total de 500 milissegundos necessários para a transcrição pode ser "ocultada" para que o usuário não esteja ciente de que a transcrição é sendo realizado enquanto eles estão falando. Ao pensar nesse exemplo, lembre-se de que o serviço está executando a transcrição nos 100 milissegundos de áudio anteriores, enquanto o usuário está falando sobre o próximo 100, portanto, quando o usuário parar de falar, o serviço terá que apenas transcrever aproximadamente 100 milissegundos de áudio para produzir um resultado.

Para atingir essa experiência do usuário, as informações de áudio faladas são coletadas em partes e transcritas à medida que o usuário fala. Esses blocos de áudio coletivamente do *fluxo de áudio*e o processo de envio dessas partes de áudio para o serviço são chamados de *streaming de áudio.* O streaming de áudio é uma parte importante de qualquer aplicativo habilitado para fala; ajustar o tamanho da parte e otimizar a implementação de streaming são algumas das maneiras mais impactantes de melhorar a experiência do usuário do aplicativo.

### <a name="microphones"></a>Microfones

As pessoas processam áudio falado usando seus ouvidos, mas o hardware inanime usa microfones. Para habilitar a fala em qualquer aplicativo, você precisa se integrar ao microfone que fornece o fluxo de áudio para seu aplicativo.

As APIs para seu microfone devem permitir que você inicie e pare de receber bytes de áudio do microfone. Você precisa decidir quais ações do usuário irão disparar o microfone para começar a ouvir a fala. Você pode optar por uma prensa de botão disparar o início da escuta ou pode optar por ter uma *palavra-chave* ou uma *palavra* spotter sempre ouvindo o microfone e usar a saída desse módulo para disparar o envio de áudio para a Microsoft Speech Serviço.

### <a name="end-of-speech"></a>Fim da fala

Detectar *quando* um palestrante *parou* de falar parece simples o bastante para os humanos, mas é um problema bastante difícil fora das condições do laboratório. Não é suficiente simplesmente procurar silêncio puro após um expressão, já que há muitas vezes há muito ruído de ambiente para complicar as coisas. O serviço de fala da Microsoft faz um excelente trabalho de detectar rapidamente quando um usuário parou de falar, e o serviço pode informar seu aplicativo sobre esse fato, mas essa organização significa que seu aplicativo é o último a saber quando o usuário pára de falar. Isso não é semelhante a outras formas de entrada em que seu aplicativo é o *primeiro* a saber quando a entrada do usuário começa *e* termina.

### <a name="asynchronous-service-responses"></a>Respostas de serviço assíncrono

O fato de que seu aplicativo precisa ser informado quando a entrada do usuário é concluída não impõe penalidades de desempenho ou dificuldades de programação em seu aplicativo, mas requer que você considere as solicitações de fala de forma diferente da solicitação de entrada/ padrões de resposta com os quais você está familiarizado. Como seu aplicativo não saberá quando o usuário parar de falar, seu aplicativo deverá continuar a transmitir áudio para o serviço enquanto aguarda e assincronamente a espera de uma resposta do serviço. Esse padrão é diferente de outros protocolos da Web de solicitação/resposta como HTTP. Nesses protocolos, você deve concluir uma solicitação antes de receber qualquer resposta; no Microsoft Speech Service Protocol, você recebe respostas *enquanto você ainda está transmitindo o áudio para a solicitação*.

> [!NOTE]
> Não há suporte para esse recurso ao usar a API REST de HTTP de fala.

### <a name="turns"></a>Activa

A fala é uma transportadora de informações. Quando você fala, está tentando transmitir informações que estão em sua posse para alguém que está ouvindo essas informações. Ao transmitir informações, você geralmente assume a fala e a escuta. Da mesma forma, seu aplicativo habilitado para fala interage com os usuários por meio de escuta e resposta alternativas, embora seu aplicativo geralmente faça a maior parte da escuta. A entrada falada do usuário e a resposta do serviço para essa entrada são chamadas de uma *rodada*. Uma *rodada* é iniciada quando o usuário fala e termina quando o aplicativo conclui o manuseio da resposta do serviço de fala.

### <a name="telemetry"></a>Telemetria

Criar um dispositivo ou aplicativo habilitado para fala pode ser desafiador, mesmo para desenvolvedores experientes. Os protocolos baseados em fluxo geralmente parecem desanimados à primeira vista, e detalhes importantes como a detecção de silêncio podem ser completamente novos. Com tantas mensagens que precisam ser enviadas e recebidas com êxito para concluir um único par de solicitação/resposta, é *muito* importante coletar dados completos e precisos sobre essas mensagens. O protocolo de serviço de fala da Microsoft fornece a coleção desses dados. Você deve fazer todos os esforços para fornecer os dados necessários o mais precisamente possível; ao fornecer dados completos e precisos, você estará se ajudando – caso precise de ajuda da equipe do serviço de fala da Microsoft para solucionar problemas de implementação do cliente, a qualidade dos dados de telemetria coletados será crítica para o problema analisa.

> [!NOTE]
> Não há suporte para este recurso ao usar a API REST de reconhecimento de fala.

### <a name="speech-application-states"></a>Estados do aplicativo de fala

As etapas que você seguir para habilitar a entrada de fala em seu aplicativo são um pouco diferentes das etapas para outras formas de entrada, como cliques do mouse ou toques de dedos. Você deve manter o controle de quando seu aplicativo está ouvindo o microfone e enviando dados para o serviço de fala, quando ele está aguardando uma resposta do serviço e quando está em estado ocioso. A relação entre esses Estados é mostrada no diagrama a seguir.

![Diagrama de estado do aplicativo de fala](Images/speech-application-state-diagram.png)

Como o serviço de fala da Microsoft participa de alguns Estados, o protocolo de serviço define as mensagens que ajudam o aplicativo a fazer a transição entre os Estados. Seu aplicativo precisa interpretar e agir sobre essas mensagens de protocolo para rastrear e gerenciar os Estados do aplicativo de fala.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Usando o serviço de reconhecimento de fala de seus aplicativos

O serviço de reconhecimento de fala da Microsoft fornece duas maneiras para os desenvolvedores adicionarem fala aos seus aplicativos.

- [APIs REST](GetStarted/GetStartedREST.md): Os desenvolvedores podem usar chamadas HTTP de seus aplicativos para o serviço para reconhecimento de fala.
- [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md): Para recursos avançados, os desenvolvedores podem baixar as bibliotecas de cliente do Microsoft Speech e vinculá-las a seus aplicativos.  As bibliotecas de cliente estão disponíveis em várias plataformas (Windows, Android, iOS) usando diferentes idiomasC#(, Java, JavaScript, ObjectiveC).

| Casos de utilização | [APIs REST](GetStarted/GetStartedREST.md) | [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Converter um áudio falado curto, por exemplo, comandos (comprimento de áudio < 15 s) sem resultados provisórios | Sim | Sim |
| Converter um áudio longo (> 15 s) | Não | Sim |
| Transmitir áudio com resultados provisórios desejados | Não | Sim |
| Entender o texto convertido de áudio usando LUIS | Não | Sim |

 Se sua linguagem ou plataforma ainda não tiver um SDK, você poderá criar sua própria implementação com base na [documentação do protocolo](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Modos de reconhecimento

Há três modos de reconhecimento: `interactive`, `conversation`e `dictation`. O modo de reconhecimento ajusta o reconhecimento de fala com base em como os usuários provavelmente falam. Escolha o modo de reconhecimento apropriado para seu aplicativo.

> [!NOTE]
> Os modos de reconhecimento podem ter comportamentos diferentes no protocolo REST do que no protocolo WebSocket. Por exemplo, a API REST não dá suporte ao reconhecimento contínuo, mesmo no modo de conversa ou de ditado.
> [!NOTE]
> Esses modos são aplicáveis quando você usa diretamente o protocolo REST ou WebSocket. As [bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) usam parâmetros diferentes para especificar o modo de reconhecimento. Para obter mais informações, consulte a biblioteca de cliente de sua escolha.

O serviço de fala da Microsoft retorna apenas um resultado de senha de reconhecimento para todos os modos de reconhecimento. Há um limite de 15 segundos para qualquer expressão único.

### <a name="interactive-mode"></a>Modo interativo

No `interactive` modo, um usuário faz solicitações curtas e espera que o aplicativo execute uma ação em resposta.

As seguintes características são típicas de aplicativos de modo interativo:

- Os usuários sabem que estão falando com um computador e não com outro humano.
- Os usuários de aplicativos conhecem antecipadamente o que querem dizer, com base no que querem que o aplicativo faça.
- Declarações geralmente, por cerca de 2-3 segundos.

### <a name="conversation-mode"></a>Modo de conversa

No `conversation` modo, os usuários estão envolvidos em uma conversa humana para a humana.

As seguintes características são típicas dos aplicativos de modo de conversa:

- Os usuários sabem que estão conversando com outra pessoa.
- O reconhecimento de fala aprimora as conversas humanas, permitindo que um ou ambos os participantes vejam o texto falado.
- Os usuários nem sempre planejam o que querem dizer.
- Os usuários frequentemente usam gírias e outra fala informal.

### <a name="dictation-mode"></a>Modo de ditado

No `dictation` modo, os usuários recitam mais declarações para o aplicativo para processamento adicional.

As seguintes características são típicas dos aplicativos de modo de ditado:

- Os usuários sabem que estão conversando com um computador.
- Os usuários são exibidos nos resultados do texto de reconhecimento de fala.
- Os usuários geralmente planejam o que querem dizer e usam uma linguagem mais formal.
- Os usuários empregam frases completas que duram 5-8 segundos.

> [!NOTE]
> Nos modos de ditado e de conversa, o serviço de fala da Microsoft não retorna resultados parciais. Em vez disso, o serviço retorna resultados de frase estável após os limites de silêncio no fluxo de áudio. A Microsoft pode aprimorar o protocolo de fala para melhorar a experiência do usuário nesses modos de reconhecimento contínuo.

## <a name="recognition-languages"></a>Idiomas de reconhecimento

O *idioma de reconhecimento* especifica o idioma que o usuário do aplicativo fala. Especifique o *idioma de reconhecimento* com o parâmetro de consulta URL de *idioma* na conexão. O valor do parâmetro de consulta de *linguagem* usa a marca de linguagem IETF [bcp 47](https://en.wikipedia.org/wiki/IETF_language_tag)e **deve** ser um dos idiomas com suporte da API de reconhecimento de fala. A lista completa de idiomas com suporte no serviço de fala pode ser encontrada na página [idiomas com suporte](API-Reference-REST/supportedlanguages.md).

O serviço de fala da Microsoft rejeita solicitações de conexão inválidas exibindo uma `HTTP 400 Bad Request` resposta. Uma solicitação inválida é aquela que:

- Não inclui um valor de parâmetro de consulta de *linguagem* .
- Inclui um parâmetro de consulta de *linguagem* que está formatado incorretamente.
- Inclui um parâmetro de consulta de *linguagem* que não é um dos idiomas de suporte.

Você pode optar por criar um aplicativo que dê suporte a um ou todos os idiomas com suporte no serviço.

### <a name="example"></a>Exemplo

No exemplo a seguir, um aplicativo usa o modo de reconhecimento de fala de *conversa* para um palestrante em inglês dos EUA.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Respostas de transcrição

As respostas de transcrição retornam o texto convertido de áudio para clientes. Uma resposta de transcrição contém os seguintes campos:

- `RecognitionStatus`Especifica o status do reconhecimento. Os valores possíveis são fornecidos na tabela a seguir.

| State | Descrição |
| ------------- | ---------------- |
| Êxito | O reconhecimento foi bem-sucedido e o campo DisplayText está presente |
| NoMatch | Conversão de voz foi detetada no fluxo de áudio, mas sem palavras do idioma de destino foram correspondidas. Consulte [status de reconhecimento NoMatch (re#nomatch-Recognition-status) para obter mais detalhes  |
| InitialSilenceTimeout | O início do fluxo de áudio continha apenas silêncio e o tempo limite do serviço expirou aguardando a fala |
| BabbleTimeout | O início do fluxo de áudio continha apenas ruído e o tempo limite do serviço expirou aguardando a fala |
| Erro | O serviço de reconhecimento encontrou um erro interno e não pôde continuar |

- `DisplayText`representa a frase reconhecida após a capitalização, a pontuação e a normalização de texto inverso foram aplicadas e a profanação foi mascarada com asteriscos. O campo exibirtexto estará presente *somente* se o `RecognitionStatus` campo tiver o valor `Success`.

- `Offset`Especifica o deslocamento (em unidades 100-nanossegundos) em que a frase foi reconhecida, em relação ao início do fluxo de áudio.

- `Duration`Especifica a duração (em unidades de 100 a nanossegundos) dessa frase de fala.

Uma resposta de transcrição retorna mais informações, se desejado. Consulte [formato de saída](#output-format) para saber como retornar saídas mais detalhadas.

O Microsoft Speech Service dá suporte ao processo adicional de transcrição que inclui adicionar maiúsculas e minúsculas, mascarar a profanação e normalizar o texto para formulários comuns. Por exemplo, se um usuário falasse uma frase representada pelas palavras "Lembre-me de comprar seis iPhones", os serviços de fala da Microsoft retornarão o texto transcrevedo "Lembre-me de comprar 6 iPhones". O processo que converte a palavra "seis" para o número "6" é chamado de *normalização de texto inverso* (*em* para curto).

### <a name="nomatch-recognition-status"></a>Status do reconhecimento NoMatch

A resposta de transcrição `NoMatch` retorna `RecognitionStatus` quando o serviço de fala da Microsoft detecta a fala no fluxo de áudio, mas não consegue fazer a correspondência dessa fala com a gramática de idioma que está sendo usada para a solicitação. Por exemplo, uma condição *NoMatch* pode ocorrer se um usuário disser algo em alemão quando o reconhecedor espera-se inglês como o idioma falado. O padrão de forma de onda do expressão indicaria a presença da fala humana, mas nenhuma das palavras faladas corresponderia ao léxico do inglês dos EUA sendo usado pelo reconhecedor.

Outra condição *NoMatch* ocorre quando o algoritmo de reconhecimento não consegue encontrar uma correspondência precisa para os sons contidos no fluxo de áudio. Quando essa condição acontece, o serviço de fala da Microsoft pode produzir mensagens de *fala. hipótese* que contêm *texto hipotético* , mas produzirá uma mensagem de *fala. frase* na qual *RecognitionStatus* é *nocorrespondente* . Essa condição é normal; Você não deve fazer suposições sobre a precisão ou a fidelidade do texto na mensagem de *fala. hipótese* . Além disso, você não deve assumir isso porque o serviço de fala da Microsoft produz mensagens de *fala. hipótese* de que o serviço é capaz de produzir uma mensagem de *fala. frase* com *êxito* *RecognitionStatus* .

## <a name="output-format"></a>Formato de saída

O serviço de fala da Microsoft pode retornar uma variedade de formatos de carga nas respostas de transcrição. Todas as cargas são estruturas JSON.

Você pode controlar o formato de resultado da frase especificando `format` o parâmetro de consulta da URL. Por padrão, o serviço retorna `simple` resultados.

| Formato | Descrição |
|-----|-----|
| `simple` | Um resultado de frase simplificada que contém o status de reconhecimento e o texto reconhecido no formulário de exibição. |
| `detailed` | Um status de reconhecimento e N-melhor lista de resultados de frase em que cada resultado de frase contém todos os quatro formulários de reconhecimento e uma pontuação de confiança. |

O `detailed` formato contém [N-melhores valores](#n-best-values) `RecognitionStatus`, além de, `Offset`e `duration`, na resposta.

### <a name="n-best-values"></a>N-melhores valores

Ouvintes, sejam humanos ou de máquina, nunca podem ter certeza de que eles souberam *exatamente* o que foi falado. Um ouvinte pode atribuir uma *probabilidade* apenas a uma interpretação específica de um expressão.

Em condições normais, ao falar com outras pessoas com as quais elas interagem com frequência, as pessoas têm uma alta probabilidade de reconhecer as palavras faladas. Os ouvintes de fala baseados em computador buscam obter níveis de precisão semelhantes e, sob as condições certas, [eles obtêm paridade com seres humanos](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Os algoritmos usados no reconhecimento de fala exploram interpretações alternativas de um expressão como parte do processamento normal. Normalmente, essas alternativas são descartadas à medida que a evidência em favor de uma única interpretação se torna impressionante. No entanto, em condições inferiores a ideais, o reconhecedor de fala termina com uma lista de possíveis interpretações alternativas. As *n* principais alternativas nessa lista são chamadas de *n-melhor lista*. Cada alternativa é atribuída a uma [Pontuação de confiança](#confidence). As pontuações de confiança variam de 0 a 1. Uma pontuação de 1 representa o nível mais alto de confiança. Uma pontuação de 0 representa o nível mais baixo de confiança.

> [!NOTE]
> O número de entradas na lista N-melhor varia em vários declarações. O número de entradas pode variar entre vários reconhecimentos do *mesmo* expressão. Essa variação é um resultado natural e esperado da natureza probabilística do algoritmo de reconhecimento de fala.

Cada entrada retornada na lista N-melhores contém

- `Confidence`, que representa as [pontuações de confiança](#confidence) dessa entrada.
- `Lexical`, que é a [forma lexical](#lexical-form) do texto reconhecido.
- `ITN`, que é a [forma em](#itn-form) do texto reconhecido.
- `MaskedITN`, que é a [forma em mascarada](#masked-itn-form) do texto reconhecido.
- `Display`, que é a [forma de exibição](#display-form) do texto reconhecido.

### Pontuações de confiança<a id="confidence"></a>

As pontuações de confiança são integrantes aos sistemas de reconhecimento de fala. O serviço de fala da Microsoft obtém as pontuações de confiança de um *classificador de confiança*. A Microsoft treina o classificador de confiança em um conjunto de recursos que são projetados para discriminar váriasr de modo máximo entre o reconhecimento correto e incorreto. As pontuações de confiança são avaliadas para palavras individuais e declarações inteira.

Se você optar por usar as pontuações de confiança que são retornadas pelo serviço, lembre-se do seguinte comportamento:

- As pontuações de confiança podem ser comparadas apenas dentro do mesmo modo de reconhecimento e idioma. Não compare pontuações entre diferentes idiomas ou modos de reconhecimento diferentes. Por exemplo, uma pontuação de confiança no modo de reconhecimento interativo *não tem nenhuma* correlação com uma pontuação de confiança no modo de ditado.
- As pontuações de confiança são mais bem usadas em um conjunto restrito de declarações. Existe naturalmente um grande grau de variabilidade nas pontuações para um grande conjunto de declarações.

Se você optar por usar um valor de Pontuação de confiança como um *limite* no qual seu aplicativo atua, use o reconhecimento de fala para estabelecer os valores de limite.

- Execute o reconhecimento de fala em uma amostra representativa de declarações para seu aplicativo.
- Colete as pontuações de confiança para cada reconhecimento no conjunto de exemplo.
- Baseie seu valor limite em algum percentil de confiança para esse exemplo.

Nenhum valor de limite único é apropriado para todos os aplicativos. Uma pontuação de confiança aceitável para um aplicativo pode ser inaceitável para outro aplicativo.

### <a name="lexical-form"></a>forma lexical

O formato léxico é o texto reconhecido, exatamente como ele ocorreu no expressão e sem pontuação ou capitalização. Por exemplo, a forma lexical do endereço "1020 Enterprise Way" seria *10 20*, supondo que foi falado dessa forma. A forma lexical da frase "lembrar-me de comprar 5 lápis" está *me lembrando de comprar cinco lápis*.

O formato léxico é mais apropriado para aplicativos que precisam executar normalização de texto não padrão. O formulário léxico também é apropriado para aplicativos que precisam de palavras de reconhecimento não processadas.

A profanação nunca é mascarada no formato léxico.

### <a name="itn-form"></a>Formulário em

Normalização de texto é o processo de conversão de texto de um formulário para outro formulário "canônico". Por exemplo, o número de telefone "555-1212" pode ser convertido para a forma canônica *5 5 5 1 2 1 2*. A normalização de texto *inverso* (em) reverte esse processo, convertendo as palavras "5 5 5 1 2 1 2" na forma canônica invertida *555-1212*. A forma em de um resultado de reconhecimento não inclui maiúsculas ou minúsculas.

O formulário em é mais apropriado para aplicativos que atuam no texto reconhecido. Por exemplo, um aplicativo que permite que um usuário fale os termos de pesquisa e, em seguida, use esses termos em uma consulta da Web para usar o formulário em. A profanação nunca é mascarada no formulário em. Para mascarar a profanação, use o *formulário mascarado em*.

### <a name="masked-itn-form"></a>Formulário em mascarado

Como a profanação é naturalmente uma parte da linguagem falada, o serviço de fala da Microsoft reconhece essas palavras e frases quando elas são faladas. No entanto, a profanação pode não ser apropriada para todos os aplicativos, especialmente para os aplicativos com um público de usuários restrito e não adulto.

O formulário mascarado em aplica a máscara de profanação ao formulário de normalização de texto inverso. Para mascarar a profanação, defina o valor do parâmetro de profanação `masked`como. Quando a profanação é mascarada, as palavras reconhecidas como parte do léxico de profanação da linguagem são substituídas por asteriscos. Por exemplo: *Lembre-me de comprar 5* lápis. A forma mascarada em de um resultado de reconhecimento não inclui maiúsculas ou minúsculas.

> [!NOTE]
> Se o valor do parâmetro de consulta de profanação `raw`for definido como, o formulário em mascarado será o mesmo que o formulário em. A profanação *não* está mascarada.

### <a name="display-form"></a>Formulário de apresentação

Sinal de Pontuação e capitalização onde colocar ênfase, onde pausar e assim por diante, o que torna o texto mais fácil de entender. O formulário de exibição adiciona Pontuação e capitalização aos resultados de reconhecimento, tornando-o a forma mais apropriada para aplicativos que exibem o texto falado.

Como o formulário de exibição estende o formulário mascarado em, você pode definir o valor do parâmetro profanaity `raw`como `masked` or. Se o valor for definido como `raw`, os resultados de reconhecimento incluirão qualquer profanação falada pelo usuário. Se o valor for definido `masked`como, as palavras reconhecidas como parte do léxico de profanação da linguagem serão substituídas por asteriscos.

### <a name="sample-responses"></a>Respostas de exemplo

Todas as cargas são estruturas JSON.

O formato da carga do `simple` resultado da frase:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

O formato da carga do `detailed` resultado da frase:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Manipulação de obscenidades no reconhecimento de fala

O serviço de fala da Microsoft reconhece todas as formas de fala humana, incluindo palavras e frases que muitas pessoas classificaram como "profanação". Você pode controlar como o serviço trata a profanação usando o parâmetro de consulta de *profanação* . Por padrão, o serviço mascara a profanação nos resultados de *fala. frase* e não retorna mensagens de *fala. hipótese* que contenham profanação.

| Valor de *profanação* | Descrição |
| - | - |
| `masked` | Mascara a profanação com asteriscos. Esse comportamento é o padrão. |
| `removed` | Remove a profanação de todos os resultados. |
| `raw` | Reconhece e retorna profanação em todos os resultados. |

### <a name="profanity-value-masked"></a>Valor de profanação`Masked`

Para mascarar a profanação, defina o parâmetro de consulta de *profanação* como o valor *mascarado*. Quando o parâmetro de consulta de *profanação* tem esse valor ou não é especificado para uma solicitação, o serviço *mascara* a profanação. O serviço executa o mascaramento substituindo a profanação nos resultados do reconhecimento por asteriscos. Quando você especifica a manipulação de mascaramento de profanação, o serviço não retorna mensagens de *fala. hipótese* que contenham profanação.

### <a name="profanity-value-removed"></a>Valor de profanação`Removed`

Quando o parâmetro de consulta de *profanação* tiver o valor *removido*, o serviço removerá a profanação das mensagens de *fala. frase* e *fala. hipótese* . Os resultados são os mesmos de *se as palavras obscenas não fossem faladas*.

#### <a name="profanity-only-utterances"></a>Declarações somente de profanação

Um usuário pode falar *apenas* de profanação quando um aplicativo tiver configurado o serviço para remover a profanação. Para esse cenário, se o modo de reconhecimento for *ditado* ou *conversa*, o serviço não retornará um *discurso. resultado*. Se o modo de reconhecimento for *interativo*, o serviço retornará uma *fala. resultado* com o código de status *NoMatch*.

### <a name="profanity-value-raw"></a>Valor de profanação`Raw`

Quando o parâmetro de consulta de *profanação* tem o valor *RAW*, o serviço não remove nem mascara a profanação nas mensagens *Speech. Formula* ou *Speech. hipótese* .
