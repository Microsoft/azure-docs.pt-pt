---
title: Conceitos de voz do Bing | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Conceitos básicos usados no serviço de voz de Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c114c726bea34465972a282acac6b8acbbf9a80f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670439"
---
# <a name="basic-concepts"></a>Conceitos básicos

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Esta página descreve alguns conceitos básicos no serviço de reconhecimento de voz de Microsoft. Recomendamos que leia esta página antes de utilizar a API de reconhecimento de voz de Microsoft em seu aplicativo.

## <a name="understanding-speech-recognition"></a>Noções básicas sobre reconhecimento de fala

Se esta for a primeira vez que esteja criando um aplicativo habilitado para fala, ou se for a primeira vez que estiver a adicionar recursos de fala num aplicativo existente, esta secção ajuda-o a começar a utilizar. Se já tiver alguma experiência com aplicativos habilitados para voz, pode optar por apenas passar nesta secção, ou pode ignorá-la, se tiver uma mão do antigo na conversão de voz e pretende ir direto os detalhes de protocolo.

### <a name="audio-streams"></a>Fluxos de áudio

É mais avançada entre os conceitos básicos de voz a *transmissão de áudio*. Ao contrário de um pressionamento de tecla, o que ocorre num único ponto no tempo e contém uma parte das informações, um pedido de falado está distribuído por centenas de milissegundos e contém muitas quilobytes de informações. A duração de expressões faladas apresenta algumas dificuldades para os desenvolvedores que desejam para fornecer uma experiência simplificada e elegante fala da respetiva aplicação. Computadores atuais e os algoritmos de realizar transcrição de voz em aproximadamente metade da duração da expressão, portanto, uma expressão de 2 segundos pode ser transcrito em aproximadamente 1 segundo, mas qualquer aplicativo que ocorre com um atraso de 1 segundo utilizador de processamento não simplificada nem elegante.

Felizmente, existem formas de "Ocultar" o tempo de transcrição, efetuando a transcrição numa parte da expressão enquanto o utilizador está a falar outra parte. Por exemplo, dividindo uma expressão de 1 segundo em 10 segmentos de 100 milissegundos e através de transcrição em cada segmento por sua vez, mais do que o total 500 milissegundos, necessários para a transcrição pode ser "oculto" de 450, para que o utilizador não tem transcrição é a ser executadas enquanto ele está a falar. Ao pensar sobre neste exemplo, lembre-se de que o serviço está a funcionar transcrição nos anteriores 100 milissegundos de áudio enquanto o utilizador está a falar os próximos 100, por isso, quando o utilizador para falando, o serviço só terá de transcrever aproximadamente 100 milissegundos de áudio para produzir um resultado.

Para obter esta experiência de utilizador, informações de áudio faladas recolhidas em blocos e transcrito conforme o usuário participa como palestrante. Esses segmentos áudio coletivamente a partir da *transmissão de áudio*, e o processo de enviar esses segmentos de áudio para o serviço é chamado *streaming de áudio.* Streaming de áudio é uma parte importante de qualquer aplicativo habilitado para fala; Ajuste o tamanho do segmento e otimizar a implementação de transmissão em fluxo são algumas das formas de melhorar a experiência do utilizador da sua aplicação maior impacto.

### <a name="microphones"></a>Microfones

As pessoas processam áudio falado usando seus ouvidos, mas utiliza de hardware inanimado microfones. Para ativar a conversão de voz em qualquer aplicativo, precisa integrar com o microfone fornecendo o fluxo de áudio para a sua aplicação.

As APIs para o seu microfone tem de permitir-lhe iniciar e parar a receção de bytes de áudio do microfone. Terá de decidir o que ações do utilizador acionará o microfone para começar a ouvir voz. Pode optar por ter um pressionamento do botão disparar a escutar ou pode optar por ter uma *palavra-chave* ou *reativação word* spotter sempre a escutar o microfone e usar a saída desse módulo para acione o envio de áudio para o serviço de voz de Microsoft.

### <a name="end-of-speech"></a>Fim da conversão de voz

Detetar *quando* tem um palestrante *parado* falando parece simples o suficiente para seres humanos, mas é um problema bem mais difícil fora de condições do laboratório. Não é suficiente simplesmente procurar silêncio puro depois de uma expressão, uma vez que muitas vezes, é muito ruído para complicar as coisas. O serviço de voz de Microsoft realiza um excelente trabalho de rapidamente detetar quando um utilizador parou falando e o serviço pode informar a sua aplicação desse fato, mas essa organização significa que seu aplicativo é o último saber quando o usuário parar de fala. Não em todos os outros formulários de entrada em que seu aplicativo é o *primeira* saber quando a entrada do utilizador é iniciada *e* termina.

### <a name="asynchronous-service-responses"></a>Respostas de serviço assíncrono

O fato de que a sua aplicação precisa para se manter informado sobre quando a entrada do usuário estiver concluída não impõe qualquer penalidades de desempenho ou dificuldades de programação em seu aplicativo, mas requer que pensa sobre pedidos de voz de forma diferente do pedido de entrada / padrões de resposta com o qual está familiarizado. Uma vez que o seu aplicativo não saberá quando o utilizador para falando, a aplicação tem de continuar a transmitir áudio para o serviço enquanto estiver em simultâneo e de forma assíncrona aguardando uma resposta do serviço. Este padrão é ao contrário de outros protocolos de web de solicitação/resposta, como o HTTP. Esses protocolos, tem de concluir um pedido antes de receber qualquer resposta; no protocolo serviço de voz da Microsoft, receber respostas *enquanto ainda estão transmissão em fluxo em áudio para o pedido*.

> [!NOTE]
> Esta funcionalidade não é suportada ao utilizar a API de REST de HTTP de voz.

### <a name="turns"></a>Ativa

Conversão de voz é uma operadora de informações. Quando fala, que está a tentar transmitir informações que está em sua posse para alguém que está à escuta dessas informações. Quando a transferência das informações, normalmente fazer turnos de fala e à escuta. Da mesma forma, seu aplicativo habilitado para fala interage com os utilizadores, em alternativa escutando e respondendo, embora seu aplicativo, normalmente, faz a maior parte da escuta. A entrada do usuário falado e a resposta do serviço para esta entrada é chamado um *ativar*. R *ativar* começa quando o usuário participa como palestrante e termina quando seu aplicativo tiver concluído o processamento da resposta do serviço de voz.

### <a name="telemetry"></a>Telemetria

Criar uma aplicação ou dispositivo habilitado para fala pode ser um desafio, mesmo para os desenvolvedores experientes. Protocolos baseados no Stream, muitas vezes, parecem desanimadora à primeira vista e detalhes importantes, como deteção de silêncio pode ser completamente nova. Com tantas mensagens que precisem de ser enviados e recebidos com êxito para concluir um par único de solicitação/resposta, é *muito* importante para recolher dados completos e precisos sobre essas mensagens. O protocolo de serviço de voz da Microsoft fornece para a recolha destes dados. Deve se esforçar para fornecer os dados necessários de forma mais precisa possível; ao fornecer dados completos e precisos, ajudará a mesmo – deve alguma vez precisar de ajuda da equipe de serviço de voz da Microsoft na sua implementação do cliente de resolução de problemas, a qualidade dos dados de telemetria que reuniu será fundamental para o problema análise.

> [!NOTE]
> Esta funcionalidade não é suportada ao utilizar a API de REST de reconhecimento de voz.

### <a name="speech-application-states"></a>Estados do aplicativo de voz

Os passos que efetuar para ativar a entrada de voz na sua aplicação são um pouco diferentes de passos para outras formas de entrada, como cliques do mouse ou o dedo toca. Deve manter um registo de quando seu aplicativo está escutando para o microfone e enviar dados para o serviço de voz, quando está a aguardar por uma resposta do serviço e quando ele estiver num estado inativo. A relação entre esses Estados é mostrada no diagrama abaixo.

![Diagrama de estado do aplicativo de voz](Images/speech-application-state-diagram.png)

Uma vez que o serviço de voz de Microsoft participa em alguns dos Estados, o protocolo de serviço define as mensagens que o ajudam a sua transição de aplicativo entre Estados. Seu aplicativo precisa interprete e atue nessas mensagens de protocolo para controlar e gerir os Estados do aplicativo de voz.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Utilizar o serviço de reconhecimento de voz das suas aplicações

Serviço de reconhecimento de voz de Microsoft fornece duas formas para os programadores adicionarem voz às suas aplicações.

- [REST APIs](GetStarted/GetStartedREST.md): Os desenvolvedores podem usar chamadas HTTP a partir das suas aplicações para o serviço para o reconhecimento de fala.
- [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md): Para recursos avançados, os desenvolvedores podem transferir as bibliotecas de cliente do Microsoft Speech e vincular a seus aplicativos.  As bibliotecas de cliente estão disponíveis em várias plataformas (Windows, Android, iOS) usando linguagens diferentes (c#, Java, JavaScript, ObjectiveC).

| Casos de utilização | [APIs REST](GetStarted/GetStartedREST.md) | [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Converter um áudio falado resumo, por exemplo, os comandos (comprimento áudio < 15 s) sem resultados intermediárias | Sim | Sim |
| Converta um áudio longo (> 15 s) | Não | Sim |
| Áudio de Stream com resultados provisórias pretendido | Não | Sim |
| Compreender o texto convertido de áudio a utilizar o LUIS | Não | Sim |

 Se a sua linguagem ou plataforma ainda não tem um SDK, pode criar sua própria implementação com base na [documentação de protocolo](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Modos de reconhecimento

Existem três modos de reconhecimento: `interactive`, `conversation`, e `dictation`. O modo de reconhecimento ajusta o reconhecimento de voz com base na forma como os utilizadores têm probabilidades de falar. Escolha o modo de reconhecimento apropriado para a sua aplicação.

> [!NOTE]
> Modos de reconhecimento podem ter diferentes comportamentos no protocolo REST do que no protocolo WebSocket. Por exemplo, a API REST não suporta reconhecimento contínuo, até mesmo no modo de ditado ou da conversação.
> [!NOTE]
> Esses modos são aplicáveis quando utiliza o protocolo REST ou WebSocket diretamente. O [bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) utilizar parâmetros diferentes para especificar o modo de reconhecimento. Para obter mais informações, consulte a biblioteca de cliente da sua preferência.

O serviço de voz de Microsoft devolve apenas um resultado de frase de reconhecimento para todos os modos de reconhecimento. Existe um limite de 15 segundos para qualquer expressão única.

### <a name="interactive-mode"></a>Modo interativo

No `interactive` modo, um usuário faz solicitações de curtas e espera que o aplicativo execute uma ação em resposta.

As seguintes características são característicos de aplicativos do modo interativo:

- Os utilizadores saibam que estão falando numa máquina e não por humanos de outro.
- Utilizadores da aplicação sabem antecipadamente o que quer dizer, com base no que eles querem que o aplicativo para o fazer.
- Expressões com normalmente da última sobre 2 a 3 segundos.

### <a name="conversation-mode"></a>Modo de conversação

No `conversation` modo, os utilizadores fazem parte de uma conversa humanos.

As seguintes características são típicas de aplicações do modo de conversa:

- Os utilizadores saibam que estão a comunicar para outra pessoa.
- Reconhecimento de fala melhora as conversas humanas, permitindo que um ou ambos os participantes ver o texto falado.
- Os utilizadores não planear sempre o que quer dizer.
- Os utilizadores usam com freqüência gíria e outro voz informal.

### <a name="dictation-mode"></a>Modo de ditado

No `dictation` modo, os utilizadores recitar mais expressões com a aplicação para processamento adicional.

As seguintes características são típicas de aplicações do modo de ditado:

- Os utilizadores saibam que estão a comunicar a uma máquina.
- Os utilizadores são mostrados os resultados de texto de reconhecimento de voz.
- Os usuários muitas vezes, planeie o que quer dizer e utilizar uma linguagem mais formal.
- Utilizadores empregam completa sentences que últimos 5 a 8 segundos.

> [!NOTE]
> Em modos de conversações e ditado, o serviço de voz de Microsoft não devolver resultados parciais. Em vez disso, o serviço devolve resultados de frase estável depois de limites de silêncio no fluxo de áudio. Microsoft pode melhorar o protocolo de voz para melhorar a experiência de utilizador nesses modos de reconhecimento contínuo.

## <a name="recognition-languages"></a>Idiomas de reconhecimento

O *linguagem do reconhecimento* Especifica o idioma que o usuário do aplicativo participa como palestrante. Especifique a *linguagem do reconhecimento* com o *linguagem* parâmetro de consulta de URL na ligação. O valor do *linguagem* consulta parâmetro utiliza a etiqueta de idioma IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), e **tem** ser um dos idiomas que são suportados pela API de reconhecimento de voz. A lista completa de idiomas suportados pelo serviço de voz pode ser encontrada na página [idiomas suportados](API-Reference-REST/supportedlanguages.md).

O serviço de voz de Microsoft rejeita pedidos de ligação inválido ao apresentar um `HTTP 400 Bad Request` resposta. Um pedido inválido é um que:

- Não inclui um *linguagem* consultar o valor do parâmetro.
- Inclui um *linguagem* consultar o parâmetro que tem um formato incorreto.
- Inclui um *linguagem* parâmetro que não é um dos idiomas com suporte de consulta.

Pode optar por criar uma aplicação que suporte um ou todos os idiomas que são suportados pelo serviço.

### <a name="example"></a>Exemplo

No exemplo a seguir, um aplicativo use *conversação* modo de reconhecimento de voz de um orador em inglês dos EUA.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Respostas de transcrição

As respostas de transcrição devolvem o texto convertido de áudio aos clientes. Uma resposta de transcrição contém os seguintes campos:

- `RecognitionStatus` Especifica o estado desse reconhecimento. Os valores possíveis são indicados na tabela abaixo.

| Estado | Descrição |
| ------------- | ---------------- |
| Êxito | O reconhecimento foi concluída com êxito e o campo de DisplayText está presente |
| NoMatch | Conversão de voz foi detetada no fluxo de áudio, mas sem palavras do idioma de destino foram correspondidas. Veja [Status(#nomatch-recognition-status) de NoMatch reconhecimento para obter mais detalhes  |
| InitialSilenceTimeout | O início do fluxo de áudio contidos apenas silêncio e o serviço excedeu o tempo aguardar por voz |
| BabbleTimeout | O início do fluxo de áudio contidos apenas ruído e o serviço excedeu o tempo aguardar por voz |
| Erro | O serviço de reconhecimento de obteve um erro interno e não foi possível continuar |

- `DisplayText` representa a frase reconhecida depois de terem sido aplicadas capitalização, pontuação e normalização do texto inverso e linguagem inapropriada tem sido oculto com asteriscos. O campo de DisplayText está presente *apenas* se o `RecognitionStatus` campo tem o valor `Success`.

- `Offset` Especifica o deslocamento (em unidades de 100 nanossegundos) em que a expressão foi reconhecida, em relação ao início do fluxo de áudio.

- `Duration`Especifica a duração (em unidades de 100 nanossegundos) desta frase de voz.

Uma resposta de transcrição devolve obter mais informações, se assim o desejar. Ver [formato de saída](#output-format) para como devolver mais saídas.

Serviço de voz do Microsoft suporta o processo de transcrição adicionais que inclui a adição de capitalização e pontuação, máscara de linguagem inapropriada e texto para formatos comuns de normalizar. Por exemplo, se um usuário participa como palestrante numa frase representada pelas palavras "lembrar-me comprar seis iPhones", serviços de voz da Microsoft irá devolver o texto transcrito "Lembrar-me comprar 6 iPhones." O processo que converte a palavra "seis" para o número de "6" é chamado *inversa de texto de normalização* (*ITN* para abreviar).

### <a name="nomatch-recognition-status"></a>Estado de reconhecimento de NoMatch

Devolve a resposta de transcrição `NoMatch` em `RecognitionStatus` quando o serviço de voz de Microsoft Deteta fala no fluxo de áudio, mas é não é possível comparar essa conversão de voz na gramática de idioma que está a ser utilizada para o pedido. Por exemplo, um *NoMatch* condição pode ocorrer se um usuário diz algo em alemão, quando o reconhecedor espera inglês Americano como o idioma falado. O padrão de forma de onda da expressão indica a presença de voz humana, mas nenhuma das palavras faladas corresponderia o léxico em inglês dos EUA a ser utilizado pelo reconhecedor.

Outro *NoMatch* condição ocorre quando o algoritmo de reconhecimento não conseguiu encontrar uma correspondência exata para sons contidos no fluxo de áudio. Quando esta condição ocorre, o serviço de voz da Microsoft pode produzir *speech.hypothesis* mensagens que contêm *hipotética texto* mas produzirá um *speech.phrase*mensagem no qual o *RecognitionStatus* é *NoMatch*. Esta condição é normal; não pode fazer suposições sobre a precisão ou fidelidade do texto da *speech.hypothesis* mensagem. Além disso, tem não suponha isso, pois o serviço de voz de Microsoft produz *speech.hypothesis* mensagens que o serviço é capaz de produzir um *speech.phrase* de mensagens com  *RecognitionStatus* *êxito*.

## <a name="output-format"></a>Formato de saída

Serviço de voz da Microsoft pode devolver uma variedade de formatos de payload respostas de transcrição. Todos os payloads são estruturas JSON.

Pode controlar o formato de resultado da expressão, especificando o `format` parâmetro de consulta de URL. Por predefinição, o serviço devolve `simple` resultados.

| Formato | Descrição |
|-----|-----|
| `simple` | Um resultado de frase simplificada que contém o estado de reconhecimento e o texto reconhecido no formulário de apresentação. |
| `detailed` | Um Estado de reconhecimento e a lista de múltipla de resultados de frase em que cada resultado da expressão contém todos os formulários de reconhecimento de quatro e uma pontuação de confiança. |

O `detailed` formato tiver [valores múltipla](#n-best-values), para além `RecognitionStatus`, `Offset`, e `duration`, na resposta.

### <a name="n-best-values"></a>Valores de múltipla

Serviços de escuta, se a máquina, ou humano podem nunca estar certos de que ter ouvido *exatamente* que foi dito. Um serviço de escuta pode atribuir um *probabilidade* apenas para uma interpretação específica de uma expressão.

Em condições normais, ao conversar com outras pessoas com quem interagem com frequência, as pessoas têm uma probabilidade elevada de reconhecer as palavras que foram faladas. Serviços de escuta de fala baseados em máquina se esforçar alcançar os níveis de precisão semelhantes e, sob as condições adequadas, [elas obtêm paridade com seres humanos](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Os algoritmos que são utilizados no reconhecimento de fala explore interpretações alternativas de uma expressão como parte do processamento normal. Normalmente, essas alternativas são eliminadas conforme as evidências em prol de uma única interpretação se tornará exagerada. Em condições não ideais, no entanto, o reconhecedor de voz conclui com uma lista de alternativas interpretações possíveis. Parte superior *N* alternativas nesta lista são chamadas os *lista múltipla*. Cada alternativa é atribuída um [pontuação de confiança](#confidence). Confiança pontuações intervalo de 0 a 1. Uma pontuação igual a 1 representa o maior nível de confiança. Uma pontuação igual a 0 representa o nível mais baixo de confiança.

> [!NOTE]
> O número de entradas na lista múltipla varia entre várias expressões. O número de entradas pode variar entre vários reconhecimentos do *mesmo* expressão. Esta variação é um resultado esperado e natural da natureza probabilístico do algoritmo de reconhecimento de voz.

Cada entrada da lista de múltipla contém

- `Confidence`, que representa a [pontuações de confiança](#confidence) desta entrada.
- `Lexical`, que é o [formulário de léxico](#lexical-form) do texto reconhecido.
- `ITN`, que é o [formulário ITN](#itn-form) do texto reconhecido.
- `MaskedITN`, que é o [mascarados formulário ITN](#masked-itn-form) do texto reconhecido.
- `Display`, que é o [apresentar formulário](#display-form) do texto reconhecido.

### Pontuações de confiança <a id="confidence"></a>

Pontuações de confiança são integrais para sistemas de reconhecimento de voz. O serviço de voz de Microsoft obtém as pontuações de confiança de uma *classificador de confiança*. Microsoft prepara o classificador de confiança através de um conjunto de recursos que foram concebidos para maximally distinguir entre reconhecimento correto e incorreto. Pontuações de confiança são avaliadas para palavras individuais e expressões com todos.

Se optar por utilizar as pontuações de confiança que são devolvidas pelo serviço, tenha em atenção o seguinte comportamento:

- Pontuações de confiança possam ser comparadas apenas dentro do mesmo modo de reconhecimento e a linguagem. Não é compare as pontuações entre diferentes idiomas ou modos diferentes de reconhecimento. Por exemplo, tem uma pontuação de confiança no modo interativo reconhecimento *nenhuma* correlação com uma pontuação de confiança no modo de ditado.
- São recomendadas para um conjunto restrito de expressões com pontuações de confiança. Naturalmente, não existe um alto grau de variabilidade nas pontuações para um grande conjunto de expressões.

Se optar por utilizar um valor de pontuação de confiança como um *limiar* no qual a aplicação atua, utilize o reconhecimento de fala para estabelecer os valores de limiar.

- Execute o reconhecimento de fala numa amostra representativa de expressões de com para a sua aplicação.
- Recolha as pontuações de confiança para cada reconhecimento no conjunto de exemplo.
- O valor de limiar base-se a alguns percentil de confiança para esse exemplo.

Nenhum valor de limiar simples é adequada para todos os aplicativos. Uma pontuação de confiança aceitável para um aplicativo pode ser aceitável para outra aplicação.

### <a name="lexical-form"></a>formulário léxico

O formulário de léxico é o texto reconhecido, exatamente como ocorreu a expressão e sem pontuação ou de uso. Por exemplo, o formulário léxico do endereço "1020 Enterprise maneira" seria *dez vinte enterprise forma*, supondo que ele foi falado dessa forma. O formulário léxico da sentença "lembrar-me comprar 5 lápis" é *lembrar-me comprar cinco lápis*.

O formulário léxico é mais adequado para aplicativos que precisam para realizar a normalização do texto não-padrão. O formulário léxico também é adequado para aplicativos que precisam de palavras de reconhecimento não processado.

Linguagem inapropriada nunca está oculto no formulário léxico.

### <a name="itn-form"></a>Formulário ITN

Normalização do texto é o processo de conversão de texto de um formato para outro formulário "canonical". Por exemplo, o número de telefone "555-1212" pode ser convertido para a forma canónica *cinco cinco cinco um dois um dois*. *Inversa* normalização do texto (ITN) reverte este processo, convertendo as palavras "cinco cinco cinco um dois um dois" para a forma canónica invertida *555-1212*. A forma ITN de um resultado de reconhecimento não inclui o uso ou pontuação.

O formulário ITN é mais adequado para aplicativos que atuam em texto reconhecido. Por exemplo, um aplicativo que permite que um usuário falar termos de pesquisa e, em seguida, utiliza estes termos numa consulta de web usaria o formulário ITN. Linguagem inapropriada nunca está oculto no formulário ITN. Mascarar palavras ofensivas, utilize o *formulário de na mascarada*.

### <a name="masked-itn-form"></a>Formulário ITN mascarado

Como linguagem inapropriada naturalmente faz parte de um idioma falado, o serviço de voz de Microsoft reconhece essas palavras e frases quando eles são ditas. Linguagem inapropriada não, no entanto, seria apropriada para todos os aplicativos, especialmente os aplicativos com um público de usuários restritas, não adulto.

O formulário ITN mascarado aplica-se a linguagem inapropriada máscara para o formulário de normalização do texto inversa. Para mascarar palavras ofensivas, defina o valor do valor de parâmetro de linguagem inapropriada a `masked`. Quando está oculto linguagem inapropriada, as palavras que são reconhecidas como parte da léxico de linguagem inapropriada da linguagem são substituídas por asteriscos. Por exemplo: *lembrar-me comprar 5 *** lápis*. A forma ITN mascarada de um resultado de reconhecimento não inclui o uso ou pontuação.

> [!NOTE]
> Se o valor de parâmetro de consulta de linguagem inapropriada estiver definido como `raw`, o formulário ITN mascarado é o mesmo que o formulário ITN. É de linguagem inapropriada *não* mascarados.

### <a name="display-form"></a>Formulário de apresentação

Pontuação e capitalização sinalizam onde colocar ênfase, onde a colocar em pausa e assim por diante, o que torna mais fácil de entender o texto. O formulário de apresentação adiciona a pontuação e capitalização para resultados de reconhecimento, tornando-a forma mais adequada para aplicativos que exibem o texto falado.

Como o formulário de apresentação estende o formulário ITN mascarado, pode definir o valor do parâmetro de linguagem inapropriada `masked` ou `raw`. Se o valor é definido como `raw`, os resultados de reconhecimento incluem alguma profanidade falada pelo utilizador. Se o valor é definido como `masked`, palavras reconhecidas como parte da léxico de linguagem inapropriada da linguagem são substituídas por asteriscos.

### <a name="sample-responses"></a>Respostas de exemplo

Todos os payloads são estruturas JSON.

O formato do payload do `simple` frase resultado:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

O formato do payload do `detailed` frase resultado:

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

## <a name="profanity-handling-in-speech-recognition"></a>Processamento de linguagem inapropriada em reconhecimento de fala

O serviço de voz de Microsoft reconhece todos os formulários de conversão de voz humana, incluindo palavras e expressões que muitas pessoas seriam classificar como "linguagem inapropriada." Pode controlar como o serviço processa a linguagem inapropriada, utilizando o *linguagem inapropriada* parâmetro de consulta. Por predefinição, o serviço dissimula a linguagem inapropriada em *speech.phrase* resulta e não devolve *speech.hypothesis* mensagens que contêm a linguagem inapropriada.

| *Linguagem inapropriada* valor | Descrição |
| - | - |
| `masked` | Máscaras linguagem inapropriada por asteriscos. Este comportamento é a predefinição. |
| `removed` | Remove a linguagem inapropriada de todos os resultados. |
| `raw` | Reconhece e retorna a linguagem inapropriada em todos os resultados. |

### <a name="profanity-value-masked"></a>Valor de linguagem inapropriada `Masked`

Para mascarar palavras ofensivas, defina o *linguagem inapropriada* parâmetro para o valor de consulta *mascarados*. Quando o *linguagem inapropriada* tiver este valor de parâmetro de consulta ou não está especificado para um pedido, o serviço *máscaras* linguagem inapropriada. O serviço executa máscara ao substituir a linguagem inapropriada nos resultados de reconhecimento por asteriscos. Quando especifica o tratamento de máscara de linguagem inapropriada, o serviço não devolve *speech.hypothesis* mensagens que contêm a linguagem inapropriada.

### <a name="profanity-value-removed"></a>Valor de linguagem inapropriada `Removed`

Quando o *linguagem inapropriada* consulta parâmetro tem o valor *removido*, o serviço remove palavras ofensivas da ambos *speech.phrase* e *speech.hypothesis* mensagens. Os resultados são os mesmos *como se as palavras de linguagem inapropriada não foram faladas*.

#### <a name="profanity-only-utterances"></a>Expressões com somente de linguagem inapropriada

Um utilizador pode falar *apenas* linguagem inapropriada quando um aplicativo tiver configurado o serviço de remover a linguagem inapropriada. Para este cenário, se for o modo de reconhecimento *ditado* ou *conversação*, o serviço não devolveu um *speech.result*. Se o modo de reconhecimento for *interativo*, o serviço retorna uma *speech.result* com o código de estado *NoMatch*.

### <a name="profanity-value-raw"></a>Valor de linguagem inapropriada `Raw`

Quando o *linguagem inapropriada* consulta parâmetro tem o valor *brutos*, o serviço não remove nem mascarar palavras ofensivas em qualquer um de *speech.phrase* ou  *Speech.hypothesis* mensagens.
