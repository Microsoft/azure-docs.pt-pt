---
title: Notas de lançamento - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Um registo de execução de lançamentos, melhorias, correções de erros e problemas conhecidos.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: f97ecedd4088a825b9ec5a076f4da70df92a3269
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775408"
---
# <a name="speech-service-release-notes"></a>Notas de lançamento do Serviço de Fala

## <a name="text-to-speech-2021-april-release"></a>Lançamento de texto-a-discurso 2021-abril

**Neural TTS está disponível em 21 regiões**

- **Doze novas regiões adicionadas** - O TTS Neural está agora disponível nestas novas 12 regiões: `Japan East` , `Japan West` , `Korea Central` `North Central US` `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` `West US 2` . Consulte [aqui](regions.md#text-to-speech) a lista completa de 21 regiões apoiadas.

## <a name="text-to-speech-2021-march-release"></a>Lançamento de texto-a-discurso 2021-março

**Novas línguas e vozes adicionadas para TTS neurais**

- **Seis novas línguas introduzidas** - 12 novas vozes em 6 novos locais são adicionadas à lista neural de línguas TTS: Nia em `cy-GB` Galês (Reino Unido), Aled em `cy-GB` Galês (Reino Unido), Rosa em `en-PH` inglês (Filipinas), James em `en-PH` inglês (Filipinas), Char Linha em `fr-BE` francês (Bélgica), Gerard em `fr-BE` francês (Bélgica), Dena em `nl-BE` holandês (Bélgica), Arnaud em `nl-BE` Holandês (Bélgica), Polina em `uk-UA` Ucraniano (Ucrânia), Ostap em `uk-UA` Ucraniano (Ucrânia), Uzma em `ur-PK` Urdu (Paquistão), Asad em `ur-PK` Urdu (Paquistão).

- **Cinco línguas desde a pré-estreia à GA** - 10 vozes em 5 locais introduzidas em 2020-novembro são ga: Kert na `et-EE` Estónia (Estónia), Colm em `ga-IE` Irlandês (Irlanda), Nils na `lv-LV` Letónia (Letónia), Leonas na `lt-LT` Lituânia (Lituânia), Joseph em `mt-MT` Maltese (Malta).

- **Nova voz masculina adicionada para francês (Canadá)** - Uma nova voz Antoine está disponível para `fr-CA` francês (Canadá).

- **Melhoria da qualidade** - Redução da taxa de erro de pronúncia em `hu-HU` húngaro - 48,17%, `nb-NO` norueguês - 52,76%, `nl-NL` Holandês (Holanda) - 22,11%.

Com esta libertação, apoiamos agora um total de 142 vozes neurais em 60 línguas/locais. Além disso, mais de 70 vozes padrão estão disponíveis em 49 línguas/locais. Visite o [suporte linguístico](language-support.md#text-to-speech) para a lista completa.

**Obtenha eventos de pose facial para animar personagens**

O [evento Viseme](how-to-speech-synthesis-viseme.md) é adicionado ao Neural TTS, que permite aos utilizadores obter a sequência de pose facial e a duração da fala sintetizada. Viseme pode ser usado para controlar o movimento de modelos de avatar 2D e 3D, combinando perfeitamente os movimentos da boca com a fala sintetizada. Agora, viseme só funciona para a voz en-US-AriaNeural.

**Adicione o elemento marcador na linguagem de marcação de síntese de fala (SSML)**

O [elemento marcador](speech-synthesis-markup.md#bookmark-element) permite-lhe inserir marcadores personalizados em SSML para obter a compensação de cada marcador no fluxo de áudio. Pode ser usado para referenciar uma localização específica na sequência de texto ou tag.

## <a name="speech-sdk-1160-2021-march-release"></a>Discurso SDK 1.16.0: Lançamento 2021-março

> [!NOTE]
> O Speech SDK no Windows depende do Microsoft Visual C++ Redistribuable para Visual Studio 2015, 2017 e 2019. Descarregue [aqui.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

#### <a name="new-features"></a>Novas funcionalidades

- **C++/C#/Java/Python**: Mudou-se para a versão mais recente do GStreamer (1.18.3) para adicionar suporte para transcrever qualquer formato de media no Windows, Linux e Android. Consulte a documentação [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python**: Suporte adicional para descodição de TTS/áudio sintetizado ao SDK. Se definir o formato de áudio de saída para PCM e GStreamer está disponível no seu sistema, o SDK solicitará automaticamente áudio comprimido do serviço para poupar largura de banda e descodificar o áudio no cliente. Pode `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` `false` configurar para desativar esta funcionalidade. Detalhes para [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet), [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python).
- **JavaScript**: Node.js os utilizadores podem agora utilizar a [ `AudioConfig.fromWavFileInput` API](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_). Isto aborda a [questão do GitHub #252.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252)
- **C++/C#/Java/Objective-C/Python**: Método adicionado `GetVoicesAsync()` para tTS devolver todas as vozes de síntese disponíveis. Detalhes para [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods), [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods) [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)e [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods).
- **C++/C#/Java/JavaScript/Objective-C/Python**: Evento adicionado `VisemeReceived` para TTS/síntese de fala para devolver animação de viseme sincronizada. Consulte a documentação [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python**: Evento adicionado `BookmarkReached` para TTS. Pode definir marcadores na entrada SSML e obter as compensações de áudio para cada marcador. Consulte a documentação [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java**: Apoio adicional às APIs de reconhecimento de altifalantes. Detalhes [aqui.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/JavaScript/Objective-C/Python**: Adicione dois novos formatos de áudio de saída com o recipiente WebM para TTS (Webm16Khz16BitMonoOpus e Webm24Khz16BitMonoOpus). Estes são melhores formatos para o streaming de áudio com o codec Opus. Detalhes para [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet), [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable) [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python).
- **C++/C#/Java**: Suporte adicional para recuperar o perfil de voz para o cenário de reconhecimento de altifalantes. Detalhes para [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)e [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable).
- **C++/C#/Java/Objective-C/Python**: Suporte adicional para biblioteca partilhada separada para microfone áudio e controlo de altifalantes. Isto permite utilizar o SDK em ambientes que não tenham exigido dependências de biblioteca sonora.
- **Objectivo-C/Swift**: Suporte adicional para a estrutura do módulo com cabeçalho de guarda-chuva. Isto permite importar o Speech SDK como um módulo em aplicações iOS/Mac Objective-C/Swift. Trata-se da [questão do GitHub #452.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452)
- **Python**: Apoio adicional para [Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) e deixou cair o suporte para Python 3,5 por [Python's end-of-life por 3,5](https://devguide.python.org/devcycle/#end-of-life-branches).

**Problemas conhecidos**

- **C++/C#/Java:** `DialogServiceConnector` não pode utilizar uma `CustomCommandsConfig` aplicação de Comandos Personalizados e, em vez disso, encontrará um erro de ligação. Isto pode ser trabalhado ao adicionar manualmente o seu ID de aplicação ao pedido com `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` . O comportamento esperado `CustomCommandsConfig` será restaurado no próximo lançamento.

#### <a name="improvements"></a>Melhorias

- Como parte do nosso esforço de lançamento multi-lançamento para reduzir o uso de memória e a pegada de disco do Speech SDK, os binários Android são agora 3% a 5% menores.
- Melhor precisão, legibilidade e secções da nossa documentação de referência C# [aqui.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet)

#### <a name="bug-fixes"></a>Correções de erros

- **JavaScript**: Os cabeçalhos de ficheiros WAV grandes são agora analisados corretamente (aumenta a fatia do cabeçalho para 512 bytes). Trata-se da [questão do GitHub #962.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962)
- **JavaScript**: Problema de tempo de microfone corrigido se o fluxo de microfone terminar antes de parar o reconhecimento, abordando um problema com o Reconhecimento de Voz que não funciona no Firefox.
- **JavaScript**: Agora lidamos corretamente com a promessa de inicialização quando o navegador força o microfone desligado antes de turnOn completar.
- **JavaScript**: Substituímos a dependência de url por url-parse. Trata-se da [questão do GitHub #264.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264)
- **Android**: Chamadas fixas que não funcionam quando `minifyEnabled` está definido para ser verdadeiro.
- **C++/C#/Java/Objective-C/Python:** `TCP_NODELAY` será corretamente definido para a tomada subjacente IO para TTS para reduzir a latência.
- **C++/C#/Java/Python/Objective-C/Go**: Fixou uma colisão ocasional quando o reconhecedor foi destruído logo após iniciar um reconhecimento.
- **C++/C#/Java**: Fixou uma colisão ocasional na destruição do reconhecimento do altifalante.

#### <a name="samples"></a>Amostras

- **JavaScript**: [As amostras](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) de navegador já não requerem o download separado do ficheiro da biblioteca JavaScript.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Speech CLI (também conhecido como SPX): lançamento 2021-março

> [!NOTE]
> Começa aqui com a interface da linha de [](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics)comando do serviço Azure Speech (CLI). O CLI permite-lhe utilizar o serviço Azure Speech sem escrever qualquer código.

#### <a name="new-features"></a>Novas funcionalidades

- Comando `spx intent` adicional para reconhecimento de intenções, substituição `spx recognize intent` .
- Reconhecer e intenção pode agora usar funções Azure para calcular a taxa de erro de palavras usando `spx recognize --wer url <URL>` .
- Reconheça agora os resultados de saída como ficheiros VTT utilizando `spx recognize --output vtt file <FILENAME>` .
- Informações sensíveis da chave agora obscurecidas na saída de depurado/verboso.
- A verificação de URL adicionada e a mensagem de erro para o campo de conteúdo na transcrição do lote criam.

**Testes abreviados COVID-19:**

À medida que a pandemia em curso continua a exigir que os nossos engenheiros trabalhem a partir de casa, os scripts de verificação manual pré-pandemia foram significativamente reduzidos. Testamos em menos dispositivos com menos configurações, e a probabilidade de erros específicos do ambiente podem ser aumentadas. Ainda validamos rigorosamente com um grande conjunto de automatização. No caso improvável de termos perdido algo, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Mantenha-se saudável!

## <a name="text-to-speech-2021-february-release"></a>Lançamento de texto-a-discurso 2021-fevereiro

**Voz Neural Personalizada GA**

Custom Neural Voice é GA em fevereiro em 13 línguas: chinês (mandarim, simplificado), inglês (Austrália), inglês (Índia), inglês (Reino Unido), inglês (Estados Unidos), francês (Canadá), francês (França), alemão (Alemanha), italiano (Itália), japonês (Japão), coreano (Coreia), português (Brasil), espanhol (México) e espanhol (Espanha). Saiba mais sobre [o que é a Voz Neural Personalizada](custom-neural-voice.md) e como [usá-la de forma responsável.](concepts-guidelines-responsible-deployment-synthetic.md) A funcionalidade Voz Neural Personalizada requer registo e a Microsoft pode limitar o acesso com base nos critérios de elegibilidade da Microsoft. Saiba mais sobre o [acesso limitado.](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)  

## <a name="speech-sdk-1150-2021-january-release"></a>Discurso SDK 1.15.0: Lançamento 2021-janeiro

> [!NOTE]
> O Speech SDK no Windows depende do Microsoft Visual C++ Redistribuable para Visual Studio 2015, 2017 e 2019. Descarregue [aqui.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Resumo dos destaques**
- Memória menor e pegada de disco tornando o SDK mais eficiente.
- Formatos de saída de maior fidelidade disponíveis para pré-visualização personalizada de voz neural privada.
- O Intenção Recogniser pode agora obter retorno mais do que a intenção superior, dando-lhe a capacidade de fazer uma avaliação separada sobre a intenção do seu cliente.
- O seu assistente de voz ou bot são agora mais fáceis de configurar, e pode fazê-lo parar de ouvir imediatamente, e exercer um maior controlo sobre como responde a erros.
- Melhorou o desempenho do dispositivo através da compressão opcional.
- Utilize o SDK de discurso no Windows ARM/ARM64.
- Melhoração de baixo nível.
- A funcionalidade de avaliação da pronúncia está agora mais disponível.
- Várias correções de Bug para resolver problemas Você, os nossos valiosos clientes, sinalizaram no GitHub! Obrigado! Mantenha o feedback vindo!

**Melhorias**
- O Discurso SDK é agora mais eficiente e leve. Iniciámos um esforço de lançamento multi-lançamento para reduzir o uso da memória e da pegada de disco da SDK do Speech SDK. Como primeiro passo, fizemos reduções significativas no tamanho de ficheiros nas bibliotecas partilhadas na maioria das plataformas. Comparado com a versão 1.14:
  - As bibliotecas Windows compatíveis com 64 bits são cerca de 30% menores.
  - As bibliotecas windows de 32 bits ainda não estão a ver melhorias de tamanho.
  - As bibliotecas Linux são 20-25% menores.
  - As bibliotecas android são 3-5% menores.

**Novas funcionalidades**
- **Todos**: Novos formatos de saída de 48KHz disponíveis para a pré-visualização privada da voz neural personalizada através da síntese de discurso TTS API: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **Tudo:** A voz personalizada também é mais fácil de usar. Suporte adicionado para definir voz personalizada através de `EndpointId` ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java,](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_) [JavaScript,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId) [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Antes desta alteração, os utilizadores de voz personalizados precisavam de definir o URL do ponto final através do `FromEndpoint` método. Agora os clientes podem usar o `FromSubscription` método tal como as vozes públicas e, em seguida, fornecer o ID de implementação por definição `EndpointId` . Isto simplifica a criação de vozes personalizadas. 
- **C++/C#/Java/Objective-C/Python**: Obtenha mais do que a intenção superior de `IntentRecognizer` . Agora suporta configurar o resultado do JSON contendo todas as intenções e não apenas a intenção de pontuação superior através do `LanguageUnderstandingModel FromEndpoint` método, utilizando `verbose=true` o parâmetro uri. Trata-se da [questão do GitHub #880.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880) Consulte a documentação atualizada [aqui.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java**: Faça o seu assistente de voz ou bot parar de ouvir imediatamente. `DialogServiceConnector`[(C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector) [, C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java)](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)agora tem um `StopListeningAsync()` método para `ListenOnceAsync()` acompanhar. Isto irá parar imediatamente a captura de áudio e esperar graciosamente por um resultado, tornando-o perfeito para ser usado com cenários de pressão de botões "stop now".
- **C++/C#/Java/JavaScript:** Faça o seu assistente de voz ou bot reagir melhor aos erros subjacentes do sistema. `DialogServiceConnector`[(C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector) [, C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java,](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) [JavaScript)](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)tem agora um novo `TurnStatusReceived` manipulador de eventos. Estes eventos opcionais correspondem a todas as [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) resoluções sobre o Bot e reportarão falhas de execução quando ocorrerem, por exemplo, como resultado de uma exceção, tempo limite ou queda de rede entre o Direct Line Speech e o bot. `TurnStatusReceived` facilita a resposta às condições de insucesso. Por exemplo, se um bot demorar muito tempo numa consulta de base de dados de backend (por exemplo, procurar um produto), `TurnStatusReceived` permite que o cliente saiba repromptar com "desculpe, eu não consegui isso, poderia tentar novamente" ou algo semelhante.
- **C++/C#**: Utilize o SDK de discurso em mais plataformas. O [pacote Speech SDK NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) suporta agora binários nativos do ambiente de trabalho Windows ARM/ARM64 (UWP já estava suportado) para tornar o SDK de discurso mais útil em mais tipos de máquinas.
- **Java**: [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) agora tem um método que foi excluído `setSpeechActivityTemplate()` involuntariamente da língua anteriormente. Isto equivale à definição da `Conversation_Speech_Activity_Template` propriedade e solicitará que todas as futuras atividades do Quadro Bot originadas pelo serviço Direct Line Speech fundam o conteúdo fornecido nas suas cargas JSON.
- **Java**: Melhor depuração de baixo nível. A [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) classe tem agora um `MessageReceived` evento, semelhante a outros idiomas de programação (C++, C#). Este evento fornece acesso de baixo nível aos dados recebidos do serviço e pode ser útil para diagnósticos e depurações.
- **JavaScript**: Configuração mais fácil para Assistentes de Voz e bots através [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) de , que agora tem e `fromHost()` `fromEndpoint()` métodos de fábrica que simplificam o uso de locais de serviço personalizados versus propriedades de definição manual. Também padronizámos especificações opcionais de `botId` usar um bot não padrão em todas as fábricas de configuração.
- **JavaScript**: Melhorado o desempenho do dispositivo através de uma propriedade de controlo de cordas adicionada para compressão websocket. Por razões de desempenho, desativamos a compressão websocket por padrão. Isto pode ser reentransivelado para cenários de largura de banda baixa. Mais detalhes [aqui.](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid) Trata-se da [questão do GitHub #242.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242)
- **JavaScript**: Apoio adicional à avaliação da pronúncia para permitir a avaliação da pronúncia da fala. Veja o arranque rápido [aqui.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Correções de bugs**
- **Tudo** (exceto JavaScript): Fixou uma regressão na versão 1.14, na qual foi atribuída demasiada memória pelo reconhecedor.
- **C++**: Fixou um problema de recolha de lixo `DialogServiceConnector` com, abordando a [questão do GitHub #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C.**: Fixar um problema com a paragem do fio que fez com que os objetos bloqueassem durante cerca de um segundo quando eliminados.
- **C++/C#/Java**: Fixou uma exceção que impede uma aplicação de definir o token de autorização de fala ou o modelo de atividade mais de uma vez num `DialogServiceConnector` .
- **C++/C#/Java**: Fixou uma queda de reconhecimento devido a uma condição de corrida em demolição.
- **JavaScript**: [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) não honrou previamente o parâmetro opcional `botId` especificado nas `BotFrameworkConfig` fábricas. Isto tornou necessário definir manualmente o parâmetro de cadeia de `botId` consulta para utilizar um bot não padrão. O bug foi corrigido e `botId` os valores fornecidos às `BotFrameworkConfig` fábricas serão honrados e utilizados, incluindo os novos `fromHost()` e `fromEndpoint()` acréscimos. Isto também se aplica ao `applicationId` parâmetro para `CustomCommandsConfig` .
- **JavaScript**: Problema [fixo do GitHub #881,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881)permitindo a reutilização do objeto reconhecível.
- **JavaScript**: Corrigiu um problema onde o SKD enviava `speech.config` várias vezes numa sessão de TTS, desperdiçando largura de banda.
- **JavaScript**: Tratamento de erro simplificado na autorização do microfone, permitindo que uma mensagem mais descritiva se insira quando o utilizador não permitiu a entrada do microfone no seu navegador.
- **JavaScript**: Problema fixo [do GitHub #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) onde erros de tipo `ConversationTranslator` e `ConversationTranscriber` causados um erro de compilação para utilizadores do TypeScript.
- **Objectivo-C**: Corrigiu um problema em que a construção do GStreamer falhou para o iOS no Xcode 11.4, abordando a [questão do GitHub #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python**: Emissão fixa [do GitHub #870,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870)removendo "DeprecationWarning: o módulo imp é depreciado a favor do importlib".

**Amostras**
- [A amostra de ficheiros para o navegador JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) agora usa ficheiros para reconhecimento de voz. Trata-se da [questão do GitHub #884.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884)

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech CLI (também conhecido como SPX): lançamento 2021-janeiro

**Novas funcionalidades**
- O Speech CLI está agora disponível como [um pacote NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) e pode ser instalado através de .NET CLI como uma ferramenta global .NET que pode chamar a partir da linha shell/comando.
- O [repo do modelo de devops de discurso personalizado](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) foi atualizado para usar o CLI do discurso para os seus fluxos de trabalho de discurso personalizado.

**Testes abreviados COVID-19**: À medida que a pandemia em curso continua a exigir que os nossos engenheiros trabalhem a partir de casa, os scripts de verificação manual pré-pandemia foram significativamente reduzidos. Testamos em menos dispositivos com menos configurações, e a probabilidade de erros específicos do ambiente podem ser aumentadas. Ainda validamos rigorosamente com um grande conjunto de automatização. No caso improvável de termos perdido algo, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Mantenha-se saudável!

## <a name="text-to-speech-2020-december-release"></a>Lançamento de texto-a-discurso 2020-dezembro

**Novas vozes neurais em GA e pré-visualização**

Lançou 51 novas vozes para um total de 129 vozes neurais em 54 línguas/locais:

- **46 novas vozes em locais da AG**: Shakir em `ar-EG` árabe (Egito), Hamed em `ar-SA` árabe (Arábia Saudita), Borislav em `bg-BG` búlgaro (Bulgária), Joana em `ca-ES` catalão (Espanha), Antonin em `cs-CZ` Checo (República Checa), Jeppe em `da-DK` Dinamarquês (Dinamarca), Jonas em `de-AT` Alemão (Áustria).), jan em `de-CH` alemão (Suíça), Nestoras em `el-GR` grego (Grécia), Liam em `en-CA` inglês (Canadá), Connor em `en-IE` inglês (Irlanda), Madhur em `en-IN` Hindi (Índia), Mohan em `en-IN` Telugu (Índia), Prabhat em `en-IN` inglês (Índia), ValluVar em `en-IN` Tamil (Índia), Enric em `es-ES` catalão (Espanha), Kert na `et-EE` Estónia (Estónia), Harri em `fi-FI` finlandês (Finlândia), Selma em `fi-FI` finlandês (Finlândia), Fabrice em `fr-CH` francês (Suíça), Colm em `ga-IE` Irlandês (Irlanda), Avri em `he-IL` hebraico (Israel), Srecko em `hr-HR` Croata (Croácia), Tamas em Húngaro `hu-HU` (Hungria), Gadis na `id-ID` Indonésia (Indonésia), Leonas na `lt-LT` Lituânia (Lituânia), Nils em `lv-LV` Letão (Letónia), Osman na `ms-MY` Malásia (Malásia), Joseph em `mt-MT` Maltês (Malta) , Finlandês em `nb-NO` Norueguês, Bokmål (Noruega), Pernille em `nb-NO` norueguês, Bokmål (Noruega), Fenna em `nl-NL` holandês (Holanda), Maarten em `nl-NL` Holanda (Holanda), Agnieszka em `pl-PL` polaco (Polónia), Marek `pl-PL` em Polaco (Polónia), Duarte `pt-BR` em Português (Brasil), Raquel em `pt-PT` Português (Potugal), Emil na `ro-RO` Roménia (Roménia), Dmitry em `ru-RU` Russo (Rússia), Svetlana em `ru-RU` russo (Rússia), Lukas na `sk-SK` Eslováquia( Eslováquia), Rok na `sl-SI` Eslovénia (Eslovénia), Mattias em `sv-SE` sueco (Suécia), Sofie em `sv-SE` sueco (Suécia), Niwat em `th-TH` tailandês (Tailândia), Ahmet em `tr-TR` turco (Turquia), NamMinh em `vi-VN` vietnamita (Vietname), HsiaoChen em `zh-TW` Taiwan Mandarim (Taiwan), YunJhe em `zh-TW` Taiwan Mandarim (Taiwan), HiuMaan em `zh-HK` cantonês chinês (Hong Kong), WanLung em `zh-HK` Língua Chinesa (Hong Kong).

- **5 novas vozes em locais de pré-visualização**: Kert in `et-EE` Estónia (Estónia), Colm in `ga-IE` Irish (Irlanda), Nils in `lv-LV` Letã (Letónia), Leonas in `lt-LT` Lituano (Lituânia), Joseph in `mt-MT` Maltese (Malta).

Com esta libertação, apoiamos agora um total de 129 vozes neurais em 54 línguas/locais. Além disso, mais de 70 vozes padrão estão disponíveis em 49 línguas/locais. Visite o [suporte linguístico](language-support.md#text-to-speech) para a lista completa.

**Atualizações para criação de conteúdo sonoro**
- UI de seleção de voz melhorada com categorias de voz e descrições de voz detalhadas. 
- Afinação de entoação ativada para todas as vozes neurais em diferentes línguas.
- Automatizar a localização da UI com base no idioma do navegador.
- Controlos `StyleDegree` ativados para todas as `zh-CN` vozes neurais.
Visite a [ferramenta de Criação de Conteúdos Áudio](https://speech.microsoft.com/audiocontentcreation) para conferir mais do que as novas funcionalidades. 

**Atualizações para vozes zh-CN**
- Atualizou todas as `zh-CN` vozes neurais para apoiar o inglês.
- Permitiu que todas as `zh-CN` vozes neurais suportassem o ajuste da entoação. A ferramenta SSML ou Audio Content Creation pode ser usada para se ajustar para a melhor entoação.
- Atualizado todas as `zh-CN` vozes neurais multi-estilo para suportar `StyleDegree` o controlo. A intensidade da emoção (macia ou forte) é ajustável.
- Atualizado `zh-CN-YunyeNeural` para suportar vários estilos que podem executar diferentes emoções.

## <a name="text-to-speech-2020-november-release"></a>Lançamento de texto-a-discurso 2020-novembro

**Novos locais e vozes na pré-visualização**
- **Cinco novas vozes e línguas** são introduzidas no portfólio Neural TTS. São eles: Grace in Maltese (Malta), Ona na Lituânia (Lituânia), Anu na Estónia (Estónia), Orla em Irlandês (Irlanda) e Everita na Letónia (Letónia).
- **Cinco novas `zh-CN` vozes com múltiplos estilos e papéis de apoio**: Xiaohan, Xiaomo, Xiaorui, Xiaoxuan e Yunxi.

> Estas vozes estão disponíveis em pré-visualização pública em três regiões de Azure: EastUS, SouthEastAsia e WestEurope.

**Recipiente neural TTS GA**
- Com o Recipiente Neural TTS, os desenvolvedores podem executar síntese de fala com as vozes digitais mais naturais no seu próprio ambiente para requisitos específicos de segurança e governação de dados. Verifique [como instalar os Recipientes de Fala](speech-container-howto.md). 

**Novas funcionalidades**
- **Voz Personalizada**: permitiu aos utilizadores copiar um modelo de voz de uma região para outra; suspensão de ponto final apoiado e retoma. Vá para o [portal](https://speech.microsoft.com/customvoice) aqui.
- [Suporte de etiqueta de silêncio SSML.](speech-synthesis-markup.md#add-silence) 
- Melhorias gerais da qualidade da voz TTS: Melhoramento da precisão da pronúncia ao nível da palavra em nb-NO. Redução de 53% de erro de pronúncia.

> Leia mais [neste blog tecnológico.](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)

## <a name="text-to-speech-2020-october-release"></a>Lançamento de texto-a-discurso 2020-outubro

**Novas funcionalidades**
- Jenny apoia um novo `newscast` estilo. Veja [como utilizar os estilos de fala em SSML](speech-synthesis-markup.md#adjust-speaking-styles).
- **Vozes neurais atualizadas para o vocoder HiFiNet, com maior fidelidade áudio e velocidade de síntese mais rápida.** Isto beneficia os clientes cujo cenário se baseia em áudio hi-fi ou interações longas, incluindo dubbing de vídeo, livros de áudio ou materiais de educação online. [Leia mais sobre a história e ouça as amostras de voz no nosso blog da comunidade tecnológica](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Estúdio de](https://speech.microsoft.com/customvoice)Criação de Conteúdo sonoro personalizado  &  [](https://speech.microsoft.com/audiocontentcreation) localizado em 17 locais.** Os utilizadores podem facilmente mudar a UI para um idioma local para uma experiência mais amigável.   
- **Criação de Conteúdo Sonoro**: Controlo de grau de estilo adicionado para XiaoxiaoNeural; Aperfeiçoou a funcionalidade de break personalizada para incluir intervalos incrementais de 50ms. 

**Melhorias gerais da qualidade da voz TTS**
- Melhoria da precisão da pronúncia ao nível da palavra `pl-PL` (redução da taxa de erro: 51%) e `fi-FI` (redução da taxa de erro: 58%)
- Melhor leitura `ja-JP` de palavra única para o cenário do dicionário. Erro de pronúncia reduzido em 80%.
- `zh-CN-XiaoxiaoNeural`: Melhor sentimento/CustomerService/Newscast/Alegre/Qualidade de voz de estilo zangado.
- `zh-CN`: Pronúncia melhorada de Erhua e tom leve e prosódia espacial refinada, o que melhora consideravelmente a inteligibilidade. 

## <a name="speech-sdk-1140-2020-october-release"></a>Discurso SDK 1.14.0: lançamento 2020-outubro

> [!NOTE]
> O Speech SDK no Windows depende do Microsoft Visual C++ Redistribuable para Visual Studio 2015, 2017 e 2019. Descarregue [aqui.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Novas funcionalidades**
- **Linux**: Apoio adicional a Debian 10 e Ubuntu 20.04 LTS.
- **Python/Objective-C:** Apoio adicional à `KeywordRecognizer` API. A documentação estará [aqui.](./custom-keyword-basics.md)
- **C++/Java/C#**: Suporte adicional para definir qualquer `HttpHeader` chave/valor através de `ServicePropertyChannel::HttpHeader` .
- **JavaScript**: Suporte adicional para a `ConversationTranscriber` API. Leia a documentação [aqui.](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 
- **C++/C#**: Adicione novo `AudioDataStream FromWavFileInput` método (para ler . Ficheiros WAV) [aqui (C++)](/cpp/cognitive-services/speech/audiodatastream) e [aqui (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/C#/Java/Python/Objective-C/Swift**: Adicionei um `stopSpeakingAsync()` método para parar a síntese de texto-a-fala. Leia aqui a documentação de referência [(C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), [aqui (C#)](/dotnet/api/microsoft.cognitiveservices.speech), [aqui (Java)](/java/api/com.microsoft.cognitiveservices.speech), [aqui (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)e [aqui (Objectivo-C/Swift)](/objectivec/cognitive-services/speech/).
- **C#, C++, Java**: Adicione uma `FromDialogServiceConnector()` função à classe que pode ser usada para monitorizar eventos de `Connection` ligação e desconexão para `DialogServiceConnector` . Leia a documentação de referência [aqui (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [aqui (C++)](/cpp/cognitive-services/speech/connection), e [aqui (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/C#/Java/Python/Objective-C/Swift**: Apoio adicional à Avaliação da Pronúncia, que avalia a pronúncia da fala e dá feedback aos falantes sobre a precisão e fluência do áudio falado. Leia a documentação [aqui.](how-to-pronunciation-assessment.md)

**Mudança de rutura**
- **JavaScript**: PullAudioOutputStream.read() tem uma alteração do tipo de retorno de uma Promessa Interna a uma Promessa JavaScript Nativa.

**Correções de bugs**
- **Tudo**: Regressão fixa 1.13 em `SetServiceProperty` que valores com determinados caracteres especiais foram ignorados.
- **C#**: Amostras fixas da consola do Windows no Visual Studio 2019 que não encontrem DLLs nativos.
- **C.**: Colisão fixa com gestão da memória se o fluxo for utilizado como `KeywordRecognizer` entrada.
- **ObjectiveC/Swift**: Colisão fixa com a gestão da memória se o fluxo for utilizado como entrada de reconhecimento.
- **Windows**: Problema de coexistência fixo com BT HFP/A2DP na UWP.
- **JavaScript**: Mapeamento fixo dos IDs de sessão para melhorar a registo e a ajuda nas correlações internas de depurg/serviço.
- **JavaScript**: Correção adicional para `DialogServiceConnector` desativar `ListenOnce` chamadas após a primeira chamada ser feita.
- **JavaScript**: Problema fixo onde a saída do resultado só seria "simples".
- **JavaScript**: Problema de reconhecimento contínuo fixo no Safari no macOS.
- **JavaScript**: Mitigação da carga do CPU para cenário de produção de pedido elevado.
- **JavaScript**: Permitir o acesso aos detalhes do resultado da inscrição do perfil de voz.
- **JavaScript**: Correção adicionada para reconhecimento contínuo em `IntentRecognizer` .
- **C++/C#/Java/Python/Swift/ObjectiveC**: Url incorreto fixo para o austráliaatro e brasileiro em `IntentRecognizer` .
- **C++/C#**: Adicionado `VoiceProfileType` como argumento ao criar um `VoiceProfile` objeto.
- **C++/C#/Java/Python/Swift/ObjectiveC**: Potencial fixo `SPX_INVALID_ARG` ao tentar ler a partir de uma determinada `AudioDataStream` posição.
- **IOS**: Acidente fixo com reconhecimento de voz na Unidade

**Amostras**
- **Objetivo C**: Amostra adicionada para reconhecimento de palavras-chave [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/JavaScript**: A added quickstart for conversation transcription [here (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) and [here (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
- **C++/C#/Java/Python/Swift/ObjectiveC**: Amostra adicionada para avaliação da pronúncia [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin**: Iniciação rápida atualizada para o modelo mais recente do Estúdio Visual [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Problema Conhecido**
- O certificado DigiCert Global Root G2 não é suportado por padrão nos HoloLens 2 e Android 4.4 (KitKat) e precisa de ser adicionado ao sistema para tornar o SDK de fala funcional. O certificado será adicionado às imagens HoloLens 2 OS num futuro próximo. Os clientes android 4.4 precisam de adicionar o certificado atualizado ao sistema.

**Testes abreviados COVID-19:** Devido ao trabalho remoto nas últimas semanas, não podíamos fazer tantos testes de verificação manual como normalmente fazemos. Não fizemos alterações que achamos que podem ter quebrado alguma coisa, e os nossos testes automatizados passaram. No caso improvável de termos perdido algo, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Mantenha-se saudável!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech CLI (também conhecido como SPX): lançamento 2020-outubro
SPX é a interface da linha de comando para utilizar o serviço Azure Speech sem código de escrita. Descarregue a versão mais recente [aqui.](./spx-basics.md) <br>

**Novas funcionalidades**
- `spx csr dataset upload --kind audio|language|acoustic` – criar conjuntos de dados a partir de dados locais, não apenas a partir de URLs.
- `spx csr evaluation create|status|list|update|delete` – comparar novos modelos com a verdade de base/outros modelos.
- `spx * list` – suporta experiência não paged (não requer -- top X --skip X).
- `spx * --http header A=B` – suporte os cabeçalhos personalizados (adicionados para o Office para autenticação personalizada). 
- `spx help` – texto melhorado e cor de texto de back-tick codificado (azul).

## <a name="text-to-speech-2020-september-release"></a>Lançamento de texto-a-discurso 2020-setembro

### <a name="new-features"></a>Novas funcionalidades

* **TTS neurais** 
    * **Estendido para apoiar 18 novas línguas/locais.** São búlgaros, checos, alemães (Áustria), alemães (Suíça), gregos, ingleses (Irlanda), franceses (Suíça), hebraico, croata, húngaro, indonésio, malaio, romeno, eslovaco, esloveno, tamil, telugu e vietnamita. 
    * **Lançou 14 novas vozes para enriquecer a variedade nas línguas existentes.** Consulte [a lista completa de línguas e vozes.](language-support.md#neural-voices)
    * **Novos estilos de fala `en-US` e `zh-CN` vozes.** Jenny, a nova voz em inglês (EUA), suporta chatbot, atendimento ao cliente e estilos de assistente. 10 novos estilos de fala estão disponíveis com a nossa voz zh-CN, XiaoXiao. Além disso, a voz neural XiaoXiao suporta `StyleDegree` a sintonização. Veja [como utilizar os estilos de fala em SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Recipientes: Recipiente Neural TTS lançado em pré-visualização pública com 16 vozes disponíveis em 14 idiomas.** Saiba mais sobre [como implantar recipientes de voz para TTS Neurais](speech-container-howto.md)  

Leia o [anúncio completo das atualizações do TTS para Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Lançamento de texto-a-discurso 2020-agosto

### <a name="new-features"></a>Novas funcionalidades

* **Neural TTS: novo estilo de fala para `en-US` Voz de Aria.** AriaNeural pode soar como um noticiário ao ler notícias. O estilo "newscast-formal" parece mais sério, enquanto o estilo "newscast-casual" é mais descontraído e informal. Veja [como utilizar os estilos de fala em SSML](speech-synthesis-markup.md).

* **Voz Personalizada: é lançada uma nova funcionalidade para verificar automaticamente a qualidade dos dados de treino**. Ao fazer o upload dos seus dados, o sistema irá examinar vários aspetos dos seus dados de áudio e transcrição e corrigir automaticamente ou filtrar problemas para melhorar a qualidade do modelo de voz. Isto cobre o volume do seu áudio, o nível de ruído, a precisão da pronúncia da fala, o alinhamento da fala com o texto normalizado, o silêncio no áudio, além do formato áudio e script. 

* **Criação de Conteúdo sonoro: um conjunto de novas funcionalidades para permitir a afinação de voz e capacidades de gestão de áudio mais potentes.**

    * Pronúncia: a função de afinação de pronúncia é atualizada para o mais recente conjunto de fones de telefone. Pode escolher o elemento de identificação telefônica certo da biblioteca e aperfeiçoar a pronúncia das palavras que selecionou. 

    * Download: A função áudio "Download"/"Export" é melhorada para suportar o áudio gerador por parágrafo. Pode editar conteúdo no mesmo ficheiro/SSML, gerando várias saídas de áudio. A estrutura de ficheiros de "Download" também é refinada. Agora, pode facilmente obter todos os ficheiros áudio numa única pasta. 

    * Estado da tarefa : A experiência de exportação de vários ficheiros é melhorada. Quando exportar vários ficheiros no passado, se um dos ficheiros falhar, toda a tarefa falhará. Mas agora, todos os outros ficheiros serão exportados com sucesso. O relatório de tarefas é enriquecido com informações mais pormenorizadas e estruturadas. Pode verificar os registos de todos os ficheiros e frases falhados agora com o relatório. 

    * Documentação SSML: ligada ao documento SSML para ajudá-lo a verificar as regras de como utilizar todas as funcionalidades de afinação.

* **A API da Lista de Vozes é atualizada para incluir um nome de exibição fácil de utilizar e os estilos de fala suportados para vozes neurais**.

### <a name="general-tts-voice-quality-improvements"></a>Melhorias gerais da qualidade da voz TTS

* Erro de pronúncia reduzido ao nível da palavra % para `ru-RU` (erros reduzidos em 56%) e `sv-SE` (erros reduzidos em 49%)

* Leitura melhorada da palavra polifonia sobre `en-US` vozes neurais em 40%. Exemplos de palavras de polifonia incluem "ler", "viver", "conteúdo", "gravar", "objeto", etc. 

* Melhorou a naturalidade do tom de pergunta em `fr-FR` . MOS (Pontuação média de opinião) ganho: +0,28

* Atualizou os vocoders para as seguintes vozes, com melhorias de fidelidade e aceleração global do desempenho em 40%.

    | Região | Voz |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Rio Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Rio Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Correções de erros

* Corrigiu uma série de bugs com a ferramenta de Criação de Conteúdos Áudio 
    * Problema fixo com auto refrescante. 
    * Problemas fixos com estilos de voz em zh-CN na região do Sudeste Asiático.
    * Problema de estabilidade fixo, incluindo um erro de exportação com a etiqueta 'break' e erros de pontuação.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Novas localidades de discurso a texto: Lançamento 2020-agosto
Discurso-a-texto divulgou 26 novos locais em agosto: 2 línguas europeias `cs-CZ` e `hu-HU` , 5 locais ingleses e 19 locais espanhóis que cobrem a maioria dos países da América do Sul. Abaixo está uma lista dos novos locais. Consulte aqui a lista completa de [línguas.](./language-support.md)

| Região  | Linguagem                          |
|---------|-----------------------------------|
| `cs-CZ` | Checo (República Checa)            | 
| `en-HK` | Inglês (Hong Kong)               | 
| `en-IE` | Inglês (Irlanda)                 | 
| `en-PH` | Inglês (Filipinas)             | 
| `en-SG` | Inglês (Singapura)               | 
| `en-ZA` | Inglês (África do Sul)            | 
| `es-AR` | Espanhol (Argentina)               | 
| `es-BO` | Espanhol (Bolívia)                 | 
| `es-CL` | Espanhol (Chile)                   | 
| `es-CO` | Espanhol (Colômbia)                | 
| `es-CR` | Espanhol (Costa Rica)              | 
| `es-CU` | Espanhol (Cuba)                    | 
| `es-DO` | Espanhol (República Dominicana)      | 
| `es-EC` | Espanhol (Equador)                 | 
| `es-GT` | Espanhol (Guatemala)               | 
| `es-HN` | Espanhol (Honduras)                | 
| `es-NI` | Espanhol (Nicarágua)               | 
| `es-PA` | Espanhol (Panamá)                  | 
| `es-PE` | Espanhol (Peru)                    | 
| `es-PR` | Espanhol (Porto Rico)             | 
| `es-PY` | Espanhol (Paraguai)                | 
| `es-SV` | Espanhol (El Salvador)             | 
| `es-US` | Espanhol (EUA)                     | 
| `es-UY` | Espanhol (Uruguai)                 | 
| `es-VE` | Espanhol (Venezuela)               | 
| `hu-HU` | Húngaro (Hungria)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Discurso SDK 1.13.0: lançamento 2020-julho

> [!NOTE]
> O Speech SDK no Windows depende do Microsoft Visual C++ Redistribuable para Visual Studio 2015, 2017 e 2019. Faça o download e instale a partir [daqui.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Novas funcionalidades**
- **C:** Apoio adicional à transcrição de conversação assíncrona. Consulte a documentação [aqui.](./how-to-async-conversation-transcription.md)  
- **JavaScript**: Suporte adicional de reconhecimento de altifalantes tanto para [o navegador](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) como [ paranode.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript**: Suporte adicional para deteção automática de idiomas/ID de linguagem. Consulte a documentação [aqui.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objectivo-C**: Suporte adicional para [conversações multi-dispositivos](./multi-device-conversation.md) e [transcrição de conversação](./conversation-transcription.md). 
- **Python**: Suporte áudio comprimido adicionado para Python em Windows e Linux. Consulte a documentação [aqui.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Correções de bugs**
- **Tudo**: Corrigiu um problema que fez com que o KeywordRecognizer não avançasse os fluxos após um reconhecimento.
- **Tudo**: Corrigiu um problema que fez com que o fluxo obtido a partir de um KeywordRecognitionResult não contivesse a palavra-chave.
- **Tudo**: Corrigiu um problema que o SendMessageAsync não envia realmente a mensagem pelo fio depois de os utilizadores terminarem de esperar por ela.
- **Tudo**: Fixou uma falha nas APIs de reconhecimento de altifalantes quando os utilizadores ligam para o método VoiceProfileClient:::SpeakerRecEnrollProfileAsync método várias vezes e não esperou que as chamadas terminassem.
- **Tudo:** Ativar fixo o registo de ficheiros nas aulas VoiceProfileClient e SpeakerRecognizer.
- **JavaScript**: Corrigi um [problema](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) com estrangulamento quando o navegador é minimizado.
- **JavaScript**: Corrigiu um [problema](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) com uma fuga de memória nos fluxos.
- **JavaScript**: Caching adicionado para respostas OCSP de NodeJS.
- **Java**: Corrigiu um problema que estava a fazer com que os campos bigInteger voltassem sempre 0.
- **iOS**: Corrigi um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) com a publicação de aplicações baseadas em SDK na Loja de Aplicações iOS.

**Amostras**
- **C++**: Código de amostra adicionado para reconhecimento de [altifalantes aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Testes abreviados COVID-19:** Devido ao trabalho remoto nas últimas semanas, não podíamos fazer tantos testes de verificação manual como normalmente fazemos. Não fizemos alterações que achamos que podem ter quebrado alguma coisa, e os nossos testes automatizados passaram. No caso improvável de termos perdido algo, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Mantenha-se saudável!

## <a name="text-to-speech-2020-july-release"></a>Lançamento de texto-a-discurso 2020-julho

### <a name="new-features"></a>Novas funcionalidades

* **Neural TTS, 15 novas vozes neurais**: As novas vozes adicionadas ao portfólio neural TTS são Salma em `ar-EG` árabe (Egito), Zariyah em `ar-SA` árabe (Arábia Saudita), Alba em `ca-ES` catalão (Espanha), Christel em `da-DK` dinamarquês (Dinamarca), Neerja em `es-IN` inglês (Índia), Noora em `fi-FI` finlandês (Finlândia).), Swara em `hi-IN` Hindi (Índia), Colette em `nl-NL` Holanda (Holanda), Zofia em `pl-PL` polaco (Polónia), Fernanda em `pt-PT` Português (Portugal), Dariya em `ru-RU` russo (Rússia), Hillevi em `sv-SE` sueco (Suécia), Achara em `th-TH` tailandês (Tailândia), HiuGaai em `zh-HK` chinês (cantonês, Tradicional) e HsiaoYu em `zh-TW` chinês (mandarim taiwanês). Verifique todas as [línguas suportadas.](./language-support.md#neural-voices)  

* **Voz Personalizada, testes de voz simplificados com o fluxo de treino para simplificar a experiência** do utilizador : Com a nova funcionalidade de teste, cada voz será automaticamente testada com um conjunto de testes pré-definido otimizado para cada idioma para cobrir cenários gerais e assistentes de voz. Estes conjuntos de testes são cuidadosamente selecionados e testados para incluir casos de uso típicos e fones de telefone no idioma. Além disso, os utilizadores ainda podem selecionar para carregar os seus próprios scripts de teste ao treinar um modelo.

* **Audio Content Creation: é lançado um conjunto de novas funcionalidades para permitir a afinação de voz e capacidades de gestão de áudio mais poderosas**

    * `Pitch`, `rate` e `volume` são melhorados para suportar a sintonização com um valor predefinido, como lento, médio e rápido. Agora é simples para os utilizadores escolherem um valor 'constante' para a sua edição de áudio.

    ![Afinação de áudio](media/release-notes/audio-tuning.png)

    * Os utilizadores podem agora rever o `Audio history` seu ficheiro de trabalho. Com esta funcionalidade, os utilizadores podem facilmente rastrear todo o áudio gerado relacionado com um ficheiro de trabalho. Podem verificar a versão histórica e comparar a qualidade enquanto afinam ao mesmo tempo. 

    ![História do áudio](media/release-notes/audio-history.png)

    * A `Clear` funcionalidade é agora mais flexível. Os utilizadores podem limpar um parâmetro de sintonização específico, mantendo outros parâmetros disponíveis para o conteúdo selecionado.  

    * Um vídeo tutorial foi adicionado na [página de aterragem](https://speech.microsoft.com/audiocontentcreation) para ajudar os utilizadores a começar rapidamente com a afinação de voz TTS e gestão de áudio. 

### <a name="general-tts-voice-quality-improvements"></a>Melhorias gerais da qualidade da voz TTS

* Melhor vocoder TTS para uma maior fidelidade e menor latência.

    * A Elsa atualizou `it-IT` um novo vocoder que alcançou +0,464 CMOS (Comparative Mean Opinion Score) ganho na qualidade de voz, 40% mais rápido na síntese e 30% na redução da latência do primeiro byte. 
    * Atualizado Xiaoxiao `zh-CN` para o novo vocoder com +0148 CMOS ganho para o domínio geral, +0.348 para o estilo de noticiário e +0.195 para o estilo lírico. 

* Modelos `de-DE` atualizados e `ja-JP` de voz para tornar a saída TTS mais natural.
    
    * Atualizado Katja `de-DE` com o mais recente método de modelação de prosody, o ganho de MOS (Mean Opinion Score) é de +0.13. 
    * Nanami atualizado `ja-JP` com um novo modelo de prosody de sotaque de tom, o ganho MOS (Mean Opinion Score) é de +0.19;  

* Melhor precisão da pronúncia ao nível da palavra em cinco línguas.

    | Linguagem | Redução do erro de pronúncia |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Correções de erros

* Leitura cambial
    * Corrigiu a questão com a leitura da moeda para `es-ES` e `es-MX`
     
    | Linguagem | Entrada | Leitura após melhoria |
    |---|---|---|
    | `es-MX` | $1,58 | un peso cincuenta y ocho centavos |
    | `es-ES` | $1,58 | un dólar cincuenta y ocho centavos |

    * Apoio à moeda negativa (como "-325€" ) nos locais seguintes: `en-US` , , , . . `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Melhor leitura de endereço em `pt-PT` .
* Problemas `en-AU` fixos de pronúncia natasha e Libby `en-UK` sobre a palavra "para" e "quatro".  
* Bugs fixos na ferramenta de criação de conteúdo sonoro
    * A pausa adicional e inesperada após a fixação do segundo parágrafo.  
    * A função 'no break' é adicionada de volta de um bug de regressão. 
    * A emissão de atualização aleatória do Estúdio de Fala é corrigida.  

### <a name="samplessdk"></a>Amostras/SDK

* JavaScript: Corre o problema de reprodução no Firefox e o Safari no macOS e iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Discurso SDK 1.12.1: Lançamento 2020-junho
**Discurso CLI (também conhecido como SPX)**
-   Adicionados recursos de pesquisa in-CLI ajudam a pesquisar:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Atualizado para trabalhar com apis de v3.0 de lote e discurso personalizado recentemente implantados:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Novas funcionalidades**
-   **C \# , C++**: Visualização do reconhecimento do altifalante: Esta funcionalidade permite a identificação dos altifalantes (quem está a falar?) e a verificação do altifalante (é o orador que dizem ser?). Comece com uma [visão geral](./speaker-recognition-overview.md), leia o artigo básico de reconhecimento de [orador,](./get-started-speaker-recognition.md)ou os [documentos de referência](/rest/api/speakerrecognition/)da API .

**Correções de bugs**
-   **C \# , C++**: A gravação fixa do microfone não estava a funcionar em 1.12 no reconhecimento do altifalante.
-   **JavaScript**: Correções para Texto-A-Falar no Firefox e Safari no macOS e iOS.
-   Correção para falha de verificação de verificação de aplicações do Windows na transcrição da transcrição da conversação quando utilizar o fluxo de oito canais.
-   Correção para falha de violação de acesso a aplicações do Windows na tradução de conversação de vários dispositivos.

**Amostras**
-   **C#**: [Amostra de código](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) para reconhecimento de altifalantes.
-   **C++**: [Amostra de código](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) para reconhecimento de altifalantes.
-   **Java**: [Amostra de código](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) para reconhecimento de intenções no Android. 

**Testes abreviados COVID-19:** Devido ao trabalho remoto nas últimas semanas, não podíamos fazer tantos testes de verificação manual como normalmente fazemos. Não fizemos alterações que achamos que podem ter quebrado alguma coisa, e os nossos testes automatizados passaram. No caso improvável de termos perdido algo, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Mantenha-se saudável!


## <a name="speech-sdk-1120-2020-may-release"></a>Discurso SDK 1.12.0: Lançamento 2020-maio
**Discurso CLI (Também Conhecido como SPX)**
- **O SPX** é uma nova ferramenta de linha de comando que lhe permite realizar reconhecimento, síntese, tradução, transcrição de lote e gestão de fala personalizada a partir da linha de comando. Utilize-o para testar o Serviço de Fala ou para scriptar as tarefas do Serviço de Fala que precisa de executar. Descarregue a ferramenta e leia a documentação [aqui.](./spx-overview.md)

**Novas funcionalidades**
- **Go**: New Go language support for [speech recognition](./get-started-speech-to-text.md?pivots=programming-language-go) and custom [voice assistant](./quickstarts/voice-assistants.md?pivots=programming-language-go). Crie aqui o seu [ambiente](./quickstarts/setup-platform.md?pivots=programming-language-go)dev. Para obter o código de amostra, consulte a secção amostras abaixo. 
- **JavaScript**: Suporte ao navegador adicionado para texto-a-voz. Consulte a documentação [aqui.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java**: Novos `KeywordRecognizer` objetos e APIs suportados nas plataformas Windows, Android, Linux & iOS. Leia a documentação [aqui.](./custom-keyword-overview.md) Para obter o código de amostra, consulte a secção amostras abaixo. 
- **Java**: Adicione conversa multi-dispositivo com suporte de tradução. Consulte [aqui](/java/api/com.microsoft.cognitiveservices.speech.transcription)o doc de referência.

**Melhorias & Otimizações**
- **JavaScript**: Implementação otimizada do microfone do navegador melhorando a precisão do reconhecimento de voz.
- **Java**: Ligações refactorizadas utilizando a implementação direta do JNI sem SWIG. Esta alteração reduz em 10x o tamanho das ligações para todos os pacotes Java utilizados para Windows, Android, Linux e Mac e facilita o desenvolvimento da implementação do Speech SDK Java.
- **Linux**: [Documentação](./speech-sdk.md?tabs=linux) de suporte atualizada com as últimas notas específicas rhel 7.
- Lógica de ligação melhorada para tentar ligar várias vezes quando ocorrem erros de serviço e de rede.
- Atualizou a página [portal.azure.com](https://portal.azure.com) Speech Quickstart para ajudar os desenvolvedores a dar o próximo passo na jornada do Discurso do Azure.

**Correções de bugs**
- **C#, Java**: Corrigiu um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) com as bibliotecas SDK de carregamento no Linux ARM (ambos 32 bits e 64 bits).
- **C:** Eliminação explícita fixa de alças nativas para traduçãoRecognizer, IntentRecognizer e Conexão.
- **C:** Gestão de vida de entrada de áudio fixa para objeto ConversationTranscriber.
- Corrigiu um problema em que `IntentRecognizer` a razão do resultado não foi corretamente definida ao reconhecer as intenções de frases simples.
- Corrigiu um problema em que a `SpeechRecognitionEventArgs` compensação dos resultados não foi corretamente definida.
- Corrigiu uma condição de corrida onde a SDK estava a tentar enviar uma mensagem de rede antes de abrir a ligação websocket. Foi reprodutível durante `TranslationRecognizer` a adição de participantes.
- Fugas de memória fixas no motor de reconhecimento de palavras-chave.

**Amostras**
- **Ir**: Entradas rápidas adicionais para [reconhecimento](./get-started-speech-to-text.md?pivots=programming-language-go) de voz e [assistente de voz personalizado](./quickstarts/voice-assistants.md?pivots=programming-language-go). Encontre o código de amostra [aqui.](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples) 
- **JavaScript**: Adição de quickstarts para [texto-a-discurso,](./get-started-text-to-speech.md?pivots=programming-language-javascript) [tradução](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)e [reconhecimento de intenções](./get-started-intent-recognition.md?pivots=programming-language-javascript).
- Amostras de reconhecimento de palavras-chave para [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) e [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Testes abreviados COVID-19:** Devido ao trabalho remoto nas últimas semanas, não podíamos fazer tantos testes de verificação manual como normalmente fazemos. Não fizemos alterações que achamos que podem ter quebrado alguma coisa, e os nossos testes automatizados passaram. Se nos escapou alguma coisa, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Mantenha-se saudável!

## <a name="speech-sdk-1110-2020-march-release"></a>Discurso SDK 1.11.0: Lançamento 2020-março
**Novas funcionalidades**
- Linux: Suporte adicional para Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 com [instruções](./how-to-configure-rhel-centos-7.md) sobre como configurar o sistema para A SDK de fala.
- Linux: Suporte adicional para .NET Core C# no Linux ARM32 e ARM64. Leia mais [aqui](./speech-sdk.md?tabs=linux). 
- C#, C++: Adicionado `UtteranceId` em , um ID consistente em todos `ConversationTranscriptionResult` os intermediários e resultado final de reconhecimento de discurso. Detalhes para [C.](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Apoio adicional para `Language ID` . Veja speech_sample.py em [GitHub repo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: Suporte ao formato de entrada de áudio comprimido adicionado na plataforma Windows para todas as aplicações da consola Win32. Detalhes [aqui.](./how-to-use-codec-compressed-audio-input-streams.md) 
- JavaScript: Síntese de discurso de suporte (texto-a-discurso) em NodeJS. Sabia mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Adicione novas API's para permitir a inspeção de todas as mensagens enviadas e recebidas. Sabia mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Correções de bugs**
- C#, C++: Fixo um problema, por isso `SendMessageAsync` agora envia mensagem binária como tipo binário. Detalhes para [C.](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: Corrigiu um problema em que a utilização do `Connection MessageReceived` evento pode causar uma falha se for eliminada antes do `Recognizer` `Connection` objeto. Detalhes para [C.](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: O tamanho do tampão de áudio do microfone diminuiu de 800ms para 100ms para melhorar a latência.
- Android: Corrigiu um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) com o emulador Android x86 no Android Studio.
- JavaScript: Apoio adicional às regiões da China com a `fromSubscription` API. Detalhes [aqui.](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-) 
- JavaScript: Adicione mais informações de erro para falhas de ligação do NodeJS.
        
**Amostras**
- Unidade: A amostra pública de reconhecimento de intenções é fixada, onde a importação de json da LUIS estava a falhar. Detalhes [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: Amostra adicionada para `Language ID` . Detalhes [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Testes abreviados Covid19:** Devido ao trabalho remoto nas últimas semanas, não podíamos fazer tantos testes de verificação manual de dispositivos como normalmente fazemos. Por exemplo, não conseguimos testar a entrada do microfone e a saída do altifalante no Linux, iOS e macOS. Não fizemos nenhuma alteração que achamos que possa ter quebrado qualquer coisa nestas plataformas, e os nossos testes automatizados passaram. No caso improvável de termos perdido algo, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br> Obrigado pelo seu apoio contínuo. Como sempre, por favor publique perguntas ou feedback no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) ou [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Mantenha-se saudável!

## <a name="speech-sdk-1100-2020-february-release"></a>Discurso SDK 1.10.0: Lançamento 2020-fevereiro

**Novas funcionalidades**

 - Adicione pacotes Python para apoiar o novo lançamento 3.8 da Python.
 - Suporte Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Os clientes devem configurar o OpenSSL de acordo com [estas instruções](./how-to-configure-openssl-linux.md).
 - Apoio linux ARM32 para Debian e Ubuntu.
 - O DialogServiceConnector suporta agora um parâmetro opcional de "bot ID" no BotFrameworkConfig. Este parâmetro permite a utilização de múltiplos bots de discurso de linha direta com um único recurso de fala Azure. Sem o parâmetro especificado, o bot predefinido (determinado pela página de configuração do canal de fala de linha direta) será utilizado.
 - DialogServiceConnector tem agora uma propriedade SpeechActivityTemplate. O conteúdo desta cadeia JSON será usado pelo Direct Line Speech para pré-povoar uma grande variedade de campos apoiados em todas as atividades que atingem um bot de Discurso de Linha Direta, incluindo atividades geradas automaticamente em resposta a eventos como o reconhecimento da fala.
 - O TTS utiliza agora a chave de subscrição para a autenticação, reduzindo a primeira latência byte do primeiro resultado da síntese após a criação de um sintetizador.
 - Modelos atualizados de reconhecimento de voz para 19 locais para uma redução média da taxa de erro de palavra de 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Os novos modelos trazem melhorias significativas em vários domínios, incluindo cenários de Ditado, Transcrição Call-Center e Indexação de Vídeo.

**Correções de bugs**

 - Bug fixo onde o Transcrivão de Conversação não aguardava adequadamente nas APIs java 
 - Correção do emulador Android x86 para emissão de Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Adicione falta (Get| Conjunto)Métodos de propriedade para AudioConfig
 - Corrija um bug TTS onde o audioDataStream não poderia ser parado quando a ligação falha
 - Usar um ponto final sem uma região causaria falhas usp para tradutor de conversação
 - A geração de ID em Aplicações Universal Windows agora usa um algoritmo GUID adequadamente único; anteriormente e involuntariamente incumprimentou uma implementação enrugada que muitas vezes produzia colisões sobre grandes conjuntos de interações.
 
 **Amostras**
 
 - Amostra de unidade para usar Speech SDK com [microfone de unidade e streaming de modo push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Outras alterações**

 - [Documentação de configuração OpenSSL atualizada para Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Discurso SDK 1.9.0: lançamento 2020-janeiro

**Novas Funcionalidades**

- Conversação multi-dispositivo: ligue vários dispositivos à mesma conversação baseada em discurso ou texto e traduza opcionalmente as mensagens enviadas entre eles. Saiba mais [neste artigo.](multi-device-conversation.md) 
- Suporte de reconhecimento de palavras-chave adicionado para pacote Android .aar e adicionado suporte para sabores x86 e x64. 
- Objectivo-C: `SendMessage` e `SetMessageProperty` métodos adicionados ao `Connection` objeto. Consulte a documentação [aqui.](/objectivec/cognitive-services/speech/spxconnection)
- TTS C++ api agora suporta como entrada de `std::wstring` texto de síntese, removendo a necessidade de converter um wstring em corda antes de passá-lo para o SDK. Consulte os detalhes [aqui.](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: [ID linguístico](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) e [config linguístico de origem](./how-to-specify-source-language.md?pivots=programming-language-csharp) já estão disponíveis.
- JavaScript: Adicionou uma funcionalidade a `Connection` opor-se para passar mensagens personalizadas do Serviço de Fala como callback `receivedServiceMessage` .
- JavaScript: Suporte adicional `FromHost API` para facilitar a utilização com recipientes on-prem e nuvens soberanas. Consulte a documentação [aqui.](speech-container-howto.md)
- JavaScript: Agora honramos `NODE_TLS_REJECT_UNAUTHORIZED` graças a uma contribuição de [orgads](https://github.com/orgads). Consulte os detalhes [aqui.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Alterações interruptivas**

- `OpenSSL` foi atualizado para a versão 1.1.1b e está estáticamente ligado à biblioteca central do SDK do Discurso para o Linux. Isto pode causar uma rutura se a sua caixa de entrada `OpenSSL` não tiver sido instalada no `/usr/lib/ssl` diretório do sistema. Por favor, consulte a [nossa documentação](how-to-configure-openssl-linux.md) nos docs da Speech SDK para contornar o assunto.
- Alteramos o tipo de dados devolvido para C# `WordLevelTimingResult.Offset` de para permitir o acesso a quando os `int` `long` `WordLevelTimingResults` dados da fala são superiores a 2 minutos.
- `PushAudioInputStream` e `PullAudioInputStream` agora enviar informações de cabeçalho de wav para o Serviço de Fala com base `AudioStreamFormat` em , opcionalmente especificado quando foram criados. Os clientes devem agora utilizar o [formato de entrada de áudio suportado.](how-to-use-audio-input-streams.md) Quaisquer outros formatos receberão resultados de reconhecimento sub-ideais ou poderão causar outros problemas. 

**Correções de bugs**

- Consulte a `OpenSSL` atualização em Breaking changes acima. Corrigimos um acidente intermitente e um problema de desempenho (contenção de bloqueio sob alta carga) em Linux e Java. 
- Java: Fez melhorias no fecho de objetos em cenários de alta concordância.
- Reestruturou o nosso pacote NuGet. Removemos as três cópias de `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` e sob pastas lib, tornando o pacote NuGet menor e mais rápido para descarregar, e adicionamos cabeçalhos necessários para compilar algumas aplicações nativas C++.
- Amostras de arranque rápido fixas [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Estes estavam a sair sem exibir a exceção "microfone não encontrado" no Linux, macOS, Windows.
- Acidente SDK fixo com resultados de reconhecimento de voz longos em certos caminhos de código como [esta amostra](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Erro de implementação fixo da SDK no ambiente da App Web Azure para resolver [este problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Corrigiu um erro TTS ao utilizar uma `<voice>` etiqueta ou `<audio>` etiqueta multi-tag para resolver [este problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Corrigiu um erro TTS 401 quando o SDK é recuperado de suspenso.
- JavaScript: Fixo uma importação circular de dados áudio graças a uma contribuição da [euirim](https://github.com/euirim). 
- JavaScript: suporte adicional para configurar propriedades de serviço, como adicionado em 1.7.
- JavaScript: corrigiu um problema em que um erro de ligação pode resultar em tentativas de reconexão contínuas e mal sucedidas.

**Amostras**

- Aqui a amostra de reconhecimento de palavras-chave adicionada para [Android](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Adicione amostra de TTS para o cenário do servidor [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Adicionou quickstarts de conversação multi-dispositivo para C# e C++ [aqui](quickstarts/multi-device-conversation.md).

**Outras alterações**

- Otimizado tamanho da biblioteca central SDK no Android.
- O SDK em 1.9.0 e para a frente suporta tanto `int` e tipos no campo de versão de assinatura de voz para o Conversation `string` Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Discurso SDK 1.8.0: Lançamento 2019-novembro

**Novas Funcionalidades**

- Adicionou uma `FromHost()` API, para facilitar o uso com recipientes on-prem e nuvens soberanas.
- Deteção automática de linguagem de origem para reconhecimento de voz (em Java e C++)
- `SourceLanguageConfig`Objeto adicionado para reconhecimento de voz, usado para especificar as línguas de origem esperadas (em Java e C++)
- Suporte `KeywordRecognizer` adicional no Windows (UWP), Android e iOS através dos pacotes NuGet e Unidade
- Adicionou conversa remota Java API para fazer transcrição de conversação em lotes assíncronos.

**Alterações interruptivas**

- As funcionalidades de Transcrição de Conversação deslocaram-se sob o espaço de `Microsoft.CognitiveServices.Speech.Transcription` nome.
- Partes dos métodos de Transcrição de Conversação são transferidas para uma nova `Conversation` classe.
- Suporte para 32 bits (ARMv7 e x86) iOS

**Correções de bugs**

- Correção para falha se o local `KeywordRecognizer` for usado sem uma chave de subscrição de serviço de discurso válido

**Amostras**

- Amostra de Xamarin para `KeywordRecognizer`
- Amostra de unidade para `KeywordRecognizer`
- Amostras C++ e Java para Deteção Automática de Idiomas de Origem.

## <a name="speech-sdk-170-2019-september-release"></a>Discurso SDK 1.7.0: Lançamento 2019-setembro

**Novas Funcionalidades**

- Suporte beta adicionado para Xamarin na Universal Windows Platform (UWP), Android e iOS
- Apoio adicional ao iOS para a Unidade
- Suporte `Compressed` de entrada adicionado para ALaw, Mulaw, FLAC no Android, iOS e Linux
- Adicionado `SendMessageAsync` na aula para enviar uma `Connection` mensagem ao serviço
- Adicionado `SetMessageProperty` na classe para definir propriedade de uma `Connection` mensagem
- TTS acrescentou encadernações para Java (JRE e Android), Python, Swift e Objective-C
- O TTS adicionou suporte de reprodução para macOS, iOS e Android.
- Acrescentou informação de "limite de palavras" para TTS.

**Correções de bugs**

- Emissão de construção de IL2CPP fixa sobre Unidade 2019 para Android
- Problema fixo com cabeçalhos mal formados na entrada de ficheiro de wav sendo processado incorretamente
- Problema fixo com UUIDs não sendo único em algumas propriedades de conexão
- Fixados alguns avisos sobre os especificadores de nulidade nas ligações Swift (podem exigir pequenas alterações de código)
- Corrigiu um bug que fez com que as ligações websocket fossem fechadas sem gravidade sob carga de rede
- Corrigiu um problema no Android que, por vezes, resulta em iDs de impressão duplicado usados por `DialogServiceConnector`
- Melhorias na estabilidade das ligações através de interações multi-voltas e o relato de falhas (através de `Canceled` eventos) quando ocorrem com `DialogServiceConnector`
- `DialogServiceConnector` início da sessão irá agora fornecer eventos corretamente, incluindo quando chamar `ListenOnceAsync()` durante um ativo `StartKeywordRecognitionAsync()`
- Abordado um acidente associado a `DialogServiceConnector` atividades que estão a ser recebidas

**Amostras**

- Arranque rápido para Xamarin
- Arranque rápido do CPP atualizado com informações linux ARM64
- Iniciação rápida de unidade atualizada com informações do iOS

## <a name="speech-sdk-160-2019-june-release"></a>Discurso SDK 1.6.0: Lançamento 2019-junho

**Amostras**

- Amostras quickstart para texto para falar sobre uWP e unidade
- Amostra quickstart para Swift no iOS
- Amostras de unidade para Discurso & Reconhecimento e Tradução de Intenções
- Amostras de arranque rápido atualizadas para `DialogServiceConnector`

**Melhorias / Alterações**

- Espaço de nome de diálogo:
  - `SpeechBotConnector` mudou de nome para `DialogServiceConnector`
  - `BotConfig` mudou de nome para `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` foi rempetado para `DialogServiceConfig::FromBotSecret()`
  - Todos os clientes do Direct Line Speech existentes continuam a ser apoiados após o renomeamento
- Atualizar adaptador TTS REST para suporte proxy, conexão persistente
- Melhorar a mensagem de erro quando uma região inválida é passada
- Swift/Objective-C:
  - Melhor relato de erros: Os métodos que podem resultar num erro estão agora presentes em duas versões: Uma que expõe um `NSError` objeto para manipulação de erros e outra que levanta uma exceção. Os primeiros estão expostos a Swift. Esta alteração requer adaptações ao código Swift existente.
  - Melhor manuseamento de eventos

**Correções de bugs**

- Correção para TTS: onde `SpeakTextAsync` o futuro voltou sem esperar até que o áudio tenha concluído a renderização
- Correção para cordas de marechal em C# para permitir suporte linguístico completo
- Correção para problema de aplicação .NET core para carregar biblioteca central com quadro-alvo net461 em amostras
- Correção para problemas ocasionais para implantar bibliotecas nativas na pasta de saída em amostras
- Correção para fecho de tomada web de forma fiável
- Correção para possível acidente enquanto abre uma ligação sob carga pesada no Linux
- Correção para metadados em falta no pacote-quadro para o macOS
- Correção para problemas com `pip install --user` o Windows

## <a name="speech-sdk-151"></a>Discurso SDK 1.5.1

Esta é uma versão de correção de erros e que apenas afeta o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

**Correções de bugs**

- Corrija a partir da subscrição quando utilizado com transcrição de conversação.
- Corrija o bug na deteção de palavras-chave para assistentes de voz.

## <a name="speech-sdk-150-2019-may-release"></a>Discurso SDK 1.5.0: Lançamento 2019-maio

**Novas funcionalidades**

- A deteção de palavras-chave (KWS) já está disponível para Windows e Linux. A funcionalidade KWS pode funcionar com qualquer tipo de microfone, o suporte oficial da KWS, no entanto, está atualmente limitado às matrizes de microfone encontradas no hardware DK Azure Kinect ou no Speech Devices SDK.
- A funcionalidade de sugestão de frase está disponível através do SDK. Para mais informações, consulte [aqui.](./get-started-speech-to-text.md)
- A funcionalidade de transcrição de conversação está disponível através do SDK. Veja [aqui.](./conversation-transcription.md)
- Adicione suporte para assistentes de voz utilizando o canal Direct Line Speech.

**Amostras**

- Amostras adicionadas para novas funcionalidades ou novos serviços apoiados pelo SDK.

**Melhorias / Alterações**

- Adicionou várias propriedades reconhecíveis para ajustar o comportamento do serviço ou os resultados do serviço (como a profanação de máscaras e outras).
- Agora pode configurar o reconhecimento através das propriedades de configuração padrão, mesmo que tenha criado o reconhecedor `FromEndpoint` .
- Objectivo-C: `OutputFormat` a propriedade foi adicionada a `SPXSpeechConfiguration` .
- O SDK agora suporta Debian 9 como uma distribuição Linux.

**Correções de bugs**

- Corrigiu um problema em que o recurso do orador foi destruído demasiado cedo em texto-a-discurso.

## <a name="speech-sdk-142"></a>Discurso SDK 1.4.2

Esta é uma versão de correção de erros e que apenas afeta o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>Discurso SDK 1.4.1

Esta é uma versão apenas para JavaScript. Não foram adicionados recursos. Foram efetuadas as seguintes correções:

- Evite que o pacote web carrene https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Discurso SDK 1.4.0: Lançamento 2019-abril

**Novas funcionalidades**

- O SDK agora suporta o serviço de texto-a-fala como uma versão beta. É suportado no Windows e Linux Desktop de C++ e C#. Para mais informações, consulte a [visão geral do texto para a fala](text-to-speech.md#get-started).
- O SDK suporta agora ficheiros de áudio MP3 e Opus/OGG como ficheiros de entrada de fluxo. Esta funcionalidade está disponível apenas no Linux a partir de C++ e C# e está atualmente em beta (mais detalhes [aqui).](how-to-use-codec-compressed-audio-input-streams.md)
- O SDK de Discurso para Java, .NET core, C++ e Objective-C ganhou apoio ao macOS. O suporte Objective-C para o macOS encontra-se atualmente em fase beta.
- iOS: O Discurso SDK para iOS (Objective-C) é agora também publicado como um Cacau.
- JavaScript: Suporte para microfone não predefinido como dispositivo de entrada.
- JavaScript: Suporte proxy para Node.js.

**Amostras**

- Foram adicionadas amostras para a utilização do SDK de discurso com C++ e com Objective-C no macOS.
- Foram adicionadas amostras que demonstram a utilização do serviço de texto-a-fala.

**Melhorias / Alterações**

- Python: Propriedades adicionais de resultados de reconhecimento estão agora expostas através da `properties` propriedade.
- Para um desenvolvimento adicional e suporte ao depuramento, pode redirecionar informações de registo e diagnóstico SDK para um ficheiro de registo (mais detalhes [aqui).](how-to-use-logging.md)
- JavaScript: Melhore o desempenho do processamento de áudio.

**Correções de bugs**

- Mac/iOS: Um bug que levou a uma longa espera quando não foi possível estabelecer uma ligação ao serviço Discurso foi corrigido.
- Python: melhorar o tratamento de erros para os argumentos em chamadas Python.
- JavaScript: Relatório de estado errado fixo para a fala terminou no PedidoS de Sessão.

## <a name="speech-sdk-131-2019-february-refresh"></a>Discurso SDK 1.3.1: 2019-fevereiro refresh

Esta é uma versão de correção de erros e que apenas afeta o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

**Correção de insetos**

- Corrigiu uma fuga de memória ao utilizar a entrada do microfone. A entrada em streaming ou de ficheiro não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>Discurso SDK 1.3.0: Lançamento 2019-fevereiro

**Novas Funcionalidades**

- O Speech SDK suporta a seleção do microfone de entrada através da `AudioConfig` classe. Isto permite-lhe transmitir dados de áudio para o serviço Dea falar a partir de um microfone não padrão. Para obter mais informações, consulte a documentação que descreve a [seleção do dispositivo de entrada de áudio](how-to-select-audio-input-devices.md). Esta funcionalidade ainda não está disponível a partir do JavaScript.
- O Speech SDK agora apoia a Unidade numa versão beta. Fornecer feedback através da secção de emissão no [repositório de amostras GitHub](https://aka.ms/csspeech/samples). Esta versão suporta a Unidade no Windows x86 e x64 (aplicações para desktop ou Universal Windows Platform) e Android (ARM32/64, x86). Mais informações estão disponíveis no nosso [quickstart Da Unidade.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)
- O ficheiro `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em versões anteriores) já não é necessário. A funcionalidade está agora integrada no núcleo SDK.

**Amostras**

Os seguintes novos conteúdos estão disponíveis no nosso [repositório de amostras:](https://aka.ms/csspeech/samples)

- Amostras adicionais para `AudioConfig.FromMicrophoneInput` .
- Amostras adicionais de Python para reconhecimento e tradução de intenções.
- Amostras adicionais para a utilização do `Connection` objeto no iOS.
- Amostras adicionais de Java para tradução com saída de áudio.
- Nova amostra para utilização da API de [Transcrição de Lote](batch-transcription.md)REST .

**Melhorias / Alterações**

- Python
  - Melhores mensagens de verificação de parâmetros e erros em `SpeechConfig` .
  - Adicione suporte para o `Connection` objeto.
  - Suporte para Python de 32 bits (x86) no Windows.
  - O SDK do Discurso para Python está fora de beta.
- iOS
  - O SDK está agora construído contra a versão 12.1 do iOS SDK.
  - O SDK agora suporta as versões iOS 9.2 e posteriores.
  - Melhore a documentação de referência e corrija vários nomes de propriedade.
- JavaScript
  - Adicione suporte para o `Connection` objeto.
  - Adicionar ficheiros de definição de tipo para JavaScript agregado
  - Apoio inicial e implementação de sugestões de frases.
  - Recolha de propriedades de retorno com serviço JSON para reconhecimento
- Os DLLs do Windows contêm agora um recurso de versão.
- Se criar um reconhecimento, `FromEndpoint` pode adicionar parâmetros diretamente ao URL do ponto final. A utilização `FromEndpoint` não pode configurar o reconhecimento através das propriedades de configuração padrão.

**Correções de bugs**

- O nome de utilizador e a palavra-passe proxy vazios não foram manuseados corretamente. Com esta versão, se definir o nome de utilizador proxy e a palavra-passe proxy para uma cadeia vazia, não serão submetidos quando estiver ligado ao representante.
- Os SSSD criados pelo SDK nem sempre foram verdadeiramente aleatórios para algumas &nbsp; línguas/ambientes. Adicionou uma inicialização aleatória do gerador para corrigir este problema.
- Melhorar o manuseamento do token de autorização. Se quiser utilizar um token de autorização, especifique na `SpeechConfig` chave de subscrição e deixe a chave de subscrição vazia. Em seguida, crie o reconhecedor como de costume.
- Em alguns casos, o `Connection` objeto não foi libertado corretamente. Esta questão foi corrigida.
- A amostra JavaScript foi fixada para suportar a produção de áudio para a síntese de tradução também no Safari.

## <a name="speech-sdk-121"></a>Discurso SDK 1.2.1

Esta é uma versão apenas para JavaScript. Não foram adicionados recursos. Foram efetuadas as seguintes correções:

- Fim de fogo do riacho na curva.end, não no discurso.end.
- Fixar bug na bomba de áudio que não tenha programado enviar se o envio atual falhar.
- Fixar reconhecimento contínuo com ficha auth.
- Correção de erros para diferentes pontos finais de reconhecimento/ endpoints.
- Melhorias de documentação.

## <a name="speech-sdk-120-2018-december-release"></a>Discurso SDK 1.2.0: Lançamento 2018-dezembro

**Novas Funcionalidades**

- Python
  - A versão Beta do suporte Python (3.5 ou superior) está disponível com esta versão. Para mais informações, consulte aqui[quickstart-python.md).
- JavaScript
  - O SDK de discurso para JavaScript foi de origem aberta. O código fonte está disponível no [GitHub.](https://github.com/Microsoft/cognitive-services-speech-sdk-js)
  - Agora apoiamos Node.js, mais informações podem ser [encontradas aqui.](./get-started-speech-to-text.md)
  - A restrição de comprimento das sessões de áudio foi removida, a reconexão acontecerá automaticamente sob a capa.
- `Connection` objeto
  - A partir `Recognizer` do, você pode aceder a um `Connection` objeto. Este objeto permite-lhe iniciar explicitamente a ligação de serviço e subscrever eventos de ligação e desconexão.
    (Esta funcionalidade ainda não está disponível a partir de JavaScript e Python.)
- Apoio a Ubuntu 18.04.
- Android
  - Suporte ProGuard ativado durante a geração APK.

**Melhorias**

- Melhorias na utilização interna do fio, reduzindo o número de fios, fechaduras, mutantes.
- Melhor reporte/informação de erros. Em vários casos, as mensagens de erro não foram propagadas até ao fim.
- Dependências de desenvolvimento atualizadas no JavaScript para utilizar módulos atualizados.

**Correções de bugs**

- Fugas de memória fixas devido a uma incompatibilidade do tipo em `RecognizeAsync` .
- Em alguns casos, foram divulgadas exceções.
- Correção de fuga de memória em argumentos de eventos de tradução.
- Corrigiu um problema de bloqueio na reconexão em sessões de longa duração.
- Corrigiu um problema que poderia levar à falta de resultados finais para traduções falhadas.
- C#: Se uma `async` operação não fosse aguardada no fio principal, era possível que o reconhecedor pudesse ser eliminado antes da tarefa async ser concluída.
- Java: Corrigiu um problema que resultou numa queda do VM de Java.
- Objetivo C: Mapeamento fixo do enum; O ReconhecidoIntente foi devolvido em vez `RecognizingIntent` de. .
- JavaScript: Desaprote o formato de saída predefinido para 'simples' em `SpeechConfig` .
- JavaScript: Remover a inconsistência entre as propriedades no objeto config em JavaScript e outros idiomas.

**Amostras**

- Atualizaram e fixaram várias amostras (por exemplo, vozes de saída para tradução, etc.).
- Adicionou Node.js [amostras no repositório de amostras.](https://aka.ms/csspeech/samples)

## <a name="speech-sdk-110"></a>Discurso SDK 1.1.0

**Novas Funcionalidades**

- Suporte para Android x86/x64.
- Suporte proxy: No `SpeechConfig` objeto, pode agora convocar uma função para definir as informações de procuração (nome de anfitrião, porta, nome de utilizador e senha). Esta funcionalidade ainda não se encontra disponível no iOS.
- Código de erro melhorado e mensagens. Se um reconhecimento devolveu um erro, este já `Reason` definiu (em evento cancelado) ou `CancellationDetails` (em resultado de reconhecimento) para `Error` . O evento cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails` . Se o servidor retornar informações adicionais de erro com o erro reportado, estará agora disponível nos novos membros.

**Melhorias**

- Adicionou verificação adicional na configuração do reconhecimento e acrescentou mensagem de erro adicional.
- Melhor manuseamento do silêncio de longa data no meio de um ficheiro áudio.
- Pacote NuGet: para projetos quadros .NET, impede a construção com a configuração AnyCPU.

**Correções de bugs**

- Corrigi várias exceções encontradas nos reconhecedores. Além disso, as exceções são capturadas e convertidas em `Canceled` evento.
- Corrija uma fuga de memória na gestão da propriedade.
- Bug fixo no qual um ficheiro de entrada de áudio pode falhar o reconhecimento.
- Corrigiu um bug onde os eventos poderiam ser recebidos após um evento de paragem de sessão.
- Corrigiu algumas condições de corrida na rosca.
- Corrigiu um problema de compatibilidade do iOS que poderia resultar num acidente.
- Melhorias de estabilidade para suporte ao microfone Android.
- Corrigiu um bug onde um reconhecimento no JavaScript ignoraria a linguagem de reconhecimento.
- Corrigiu um bug que impede a definição do `EndpointId` (em alguns casos) no JavaScript.
- Altere a ordem de parâmetros em AddIntent em JavaScript e adicione a assinatura JavaScript em `AddIntent` falta.

**Amostras**

- Adicione amostras C++ e C# para puxar e empurrar a utilização do fluxo no [repositório](https://aka.ms/csspeech/samples)de amostras .

## <a name="speech-sdk-101"></a>Discurso SDK 1.0.1

Melhorias de fiabilidade e correções de erros:

- Erro fatal potencial corrigido devido à condição de corrida na eliminação do reconhecimento
- Erro fatal potencial fixo quando ocorrem propriedades não estan caducas.
- Adicionou erro adicional e verificação de parâmetros.
- Objectivo-C: Erro fatal fixo causado pelo nome que se sobrepuse no NSString.
- Objectivo-C: Visibilidade ajustada da API
- JavaScript: Fixo em relação aos eventos e às suas cargas.
- Melhorias de documentação.

No nosso [repositório de amostras,](https://aka.ms/csspeech/samples)foi adicionada uma nova amostra para o JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Discurso dos Serviços Cognitivos SDK 1.0.0: Lançamento 2018-setembro

**Novas funcionalidades**

- Apoio ao Objective-C no iOS. Confira o nosso [arranque rápido Objective-C para iOS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone).
- Suporte para JavaScript no browser. Confira o nosso [arranque rápido JavaScript](./get-started-speech-to-text.md).

**Alterações interruptivas**

- Com esta versão, são introduzidas várias alterações de rutura.
  Consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para mais detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Discurso dos Serviços Cognitivos SDK 0.6.0: Lançamento 2018-agosto

**Novas funcionalidades**

- As aplicações UWP construídas com o Speech SDK podem agora passar o Kit de Certificação de Aplicações do Windows (WACK).
  Confira o [arranque rápido da UWP.](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)
- Suporte para .NET Standard 2.0 em Linux (Ubuntu 16.04 x64).
- Experimental: Suporte Java 8 no Windows (64-bit) e Linux (Ubuntu 16.04 x64).
  Confira o [arranque rápido do Java Runtime Environment](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Mudança funcional**

- Exponha informações adicionais sobre erros de ligação.

**Alterações interruptivas**

- Em Java (Android), a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` função já não requer um parâmetro de caminho. Agora o caminho é automaticamente detetado em todas as plataformas suportadas.
- O acessório da propriedade `EndpointUrl` em Java e C# foi removido.

**Correções de bugs**

- Em Java, o resultado da síntese áudio no reconhecimento de tradução é agora implementado.
- Corrigiu um erro que poderia causar fios inativos e um número aumentado de tomadas abertas e não utos.
- Corrigiu um problema, onde um reconhecimento de longa duração poderia terminar no meio da transmissão.
- Corrigiu uma condição de corrida no paragem do reconhecimento.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Discurso dos Serviços Cognitivos SDK 0.5.0: Lançamento de 2018-julho

**Novas funcionalidades**

- Suporte plataforma Android (API 23: Android 6.0 Marshmallow ou superior). Confira o arranque rápido do [Android.](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)
- Suporte .NET Standard 2.0 no Windows. Confira o [arranque rápido .NET Core](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: Suporte UWP no Windows (versão 1709 ou posterior).
  - Confira o [arranque rápido da UWP.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)
  - Note que as aplicações UWP construídas com o Speech SDK ainda não passam o Kit de Certificação de Aplicações do Windows (WACK).
- Suporte o reconhecimento de longa duração com a reconexão automática.

**Alterações funcionais**

- `StartContinuousRecognitionAsync()` apoia o reconhecimento de longa duração.
- O resultado do reconhecimento contém mais campos. São compensados desde o início e duração áudio (ambos em carrapatos) do texto reconhecido e valores adicionais que representam o estatuto de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout` .
- Autorização de ApoioToken para criar instâncias de fábrica.

**Alterações interruptivas**

- Eventos de reconhecimento: `NoMatch` tipo de evento foi fundido no `Error` evento.
- SpeechOutputFormat em C# foi renomeado `OutputFormat` para permanecer alinhado com C++.
- O tipo de devolução de alguns métodos da `AudioInputStream` interface mudou ligeiramente:
  - Em Java, o `read` método regressa agora em vez `long` `int` de.
  - Em C#, o `Read` método agora retorna `uint` em vez de `int` .
  - Em C++, os `Read` métodos e `GetFormat` métodos regressam agora `size_t` em vez de `int` .
- C++: As instâncias de fluxos de entrada de áudio agora só podem ser passadas como um `shared_ptr` .

**Correções de bugs**

- Valores de retorno incorretos fixos no resultado quando `RecognizeAsync()` o tempo de 200 horas for esgotado.
- A dependência das bibliotecas de fundações de media no Windows foi removida. O SDK agora usa APIs de áudio core.
- Correção de documentação: Adicionou uma página [de regiões](regions.md) para descrever as regiões apoiadas.

**Problema conhecido**

- O Speech SDK para Android não reporta os resultados da síntese da fala para tradução. Esta questão será corrigida no próximo lançamento.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Discurso dos Serviços Cognitivos SDK 0.4.0: Lançamento 2018-junho

**Alterações funcionais**

- AudioInputStream

  Um reconhecedor agora pode consumir um fluxo como fonte de áudio. Para mais informações, consulte o guia de [como fazer.](how-to-use-audio-input-streams.md)

- Formato de saída detalhado

  Quando criar um `SpeechRecognizer` formato , pode solicitar ou `Detailed` `Simple` produzir. Contém `DetailedSpeechRecognitionResult` uma pontuação de confiança, texto reconhecido, forma lexical crua, forma normalizada e forma normalizada com profanação mascarada.

**Mudança de rutura**

- Mudou para `SpeechRecognitionResult.Text` de `SpeechRecognitionResult.RecognizedText` C#.

**Correções de bugs**

- Corrigiu um possível problema de retorno na camada USP durante o encerramento.
- Se um reconhecimento consumiu um ficheiro de entrada de áudio, manteve-se agarrado ao cabo do ficheiro por mais tempo do que o necessário.
- Removi vários impasses entre a bomba de mensagem e o reconhecedor.
- Dispare um `NoMatch` resultado quando a resposta do serviço estiver fora do prazo.
- As bibliotecas da fundação dos media no Windows estão carregadas. Esta biblioteca é necessária apenas para a entrada do microfone.
- A velocidade de carregamento para dados áudio está limitada a cerca do dobro da velocidade de áudio original.
- No Windows, os conjuntos C# .NET agora são fortes nomeados.
- Correção de documentação: `Region` é necessária informação para criar um reconhecimento.

Foram adicionadas mais amostras e estão constantemente a ser atualizadas. Para obter o mais recente conjunto de amostras, consulte as [amostras do SDK do Discurso GitHub.](https://aka.ms/csspeech/samples)

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Discurso dos Serviços Cognitivos SDK 0.2.12733: Lançamento 2018-maio

Este lançamento é o primeiro lançamento público do Discurso dos Serviços Cognitivos SDK.
