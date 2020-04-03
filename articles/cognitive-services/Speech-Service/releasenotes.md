---
title: Notas de lançamento - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: Um registo de registo de lançamentos, melhorias, correções de bugs e questões conhecidas.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607407"
---
# <a name="release-notes"></a>Notas de versão
## <a name="speech-sdk-1110-2020-march-release"></a>Discurso SDK 1.11.0: Lançamento de 2020-março

**Novas funcionalidades**

- Linux: Suporte adicionado para Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 com [instruções](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) sobre como configurar o sistema para Speech SDK.
- Linux: Suporte adicionado para .NET Core C# em Linux ARM32 e ARM64. Leia mais [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux) 
- C#, C++: `UtteranceId` `ConversationTranscriptionResult`Adicionado , uma identificação consistente em todos os intermediários e resultado final do reconhecimento da fala. Detalhes para [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet) [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Apoio `Language ID`acrescido para . Por favor, consulte speech_sample.py no [Repo GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: Suporte de formato de entrada de áudio comprimido adicionado na plataforma Windows para todas as aplicações de consola win32. Detalhes [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams) 
- JavaScript: Apoiar a síntese da fala (texto-a-fala) no NodeJS. Sabia mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Adicione novos API's para permitir a inspeção de todas as mensagens enviadas e recebidas. Sabia mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Correções de erros**

- C#, C++: Corrigiu `SendMessageAsync` um problema, pelo que agora envia a mensagem binária como tipo binário. Detalhes para [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: Fixar um `Connection MessageReceived` problema em `Recognizer` que a `Connection` utilização do evento pode causar acidente se for eliminado antes do objeto. Detalhes para [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet) [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: O tamanho do tampão áudio do microfone diminuiu de 800ms para 100ms para melhorar a latência.
- Android: Corrigiu um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) com o emulador Android x86 no Android Studio.
- JavaScript: Suporte adicional para regiões `fromSubscription` na China com a API. Detalhes [aqui.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-) 
- JavaScript: Adicione mais informações de erro para falhas de ligação a partir do NodeJS.
        
**Amostras**

- Unidade: A amostra pública de reconhecimento de intenção é fixada, onde a importação de JSON estava a falhar. Detalhes [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: Amostra `Language ID`adicionada para . Detalhes [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Testes abreviados Covid19**

Devido ao trabalho remoto nas últimas semanas, não podíamos fazer tantos testes de verificação manual de dispositivos como normalmente fazemos. Um exemplo disso é testar a entrada do microfone e a saída do altifalante no Linux, iOS e macOS. Não fizemos nenhuma alteração que achamos que pode ter quebrado alguma coisa nestas plataformas, e os nossos testes automatizados passaram. No caso improvável de nos ter escapado alguma coisa, por favor, avise-nos no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen)<br> Obrigado pelo seu apoio contínuo. Como sempre, por favor publique perguntas ou feedback no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) ou [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Mantenha-se saudável!

## <a name="speech-sdk-1100-2020-february-release"></a>Discurso SDK 1.10.0: Lançamento de 2020-fevereiro

**Novas funcionalidades**

 - Adicionou pacotes Python para apoiar o novo lançamento 3.8 da Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 suporte (C++, C#, Java, Python).
   > [!NOTE] 
   > Os clientes devem configurar o OpenSSL de acordo com [estas instruções](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Suporte Linux ARM32 para Debian e Ubuntu.
 - DialogServiceConnector suporta agora um parâmetro opcional "bot ID" no BotFrameworkConfig. Este parâmetro permite a utilização de vários bots de discurso de linha direta com um único recurso de fala Azure. Sem o parâmetro especificado, será utilizado o bot predefinido (conforme determinado pela página de configuração do canal Direct Line Speech).
 - DialogServiceConnector agora tem uma propriedade SpeechActivityTemplate. O conteúdo desta corda JSON será usado pelo Direct Line Speech para pré-povoar uma grande variedade de campos apoiados em todas as atividades que atingem um bot de Discurso de Linha Direta, incluindo atividades geradas automaticamente em resposta a eventos como reconhecimento de fala.
 - O TTS utiliza agora a chave de subscrição para autenticação, reduzindo a primeira latência byte do primeiro resultado de síntese após a criação de um sintetizador.
 - Modelos atualizados de reconhecimento da fala para 19 locais para uma redução média da taxa de erro de palavra de 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Os novos modelos trazem melhorias significativas em vários domínios, incluindo Dictação, Transcrição de Call-Center e cenários de Indexação de Vídeo.

**Correções de erros**

 - Bug fixo onde O Transcritodor de Conversação não aguardava corretamente em APIs JAVA 
 - Correção de emulador Android x86 para problema de Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Adicionar desaparecido (Get] Conjunto)Métodos de propriedade para AudioConfig
 - Corrija um bug TTS onde o áudioDataStream não pudesse ser interrompido quando a ligação falha
 - Usar um ponto final sem uma região causaria falhas de USP para tradutor de conversação
 - A geração de ID em Aplicações Universais do Windows agora usa um algoritmo GUID adequadamente único; que anteriormente e involuntariamente não parauma implementação de stubbed que muitas vezes produziu colisões sobre grandes conjuntos de interações.
 
 **Amostras**
 
 - Amostra de unidade para usar O SDK do Discurso com [microfone de unidade e streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) de modo push

**Outras alterações**

 - [Documentação de configuração OpenSSL atualizada para Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Discurso SDK 1.9.0: lançamento de 2020-janeiro

**Novas Funcionalidades**

- Conversação multi-dispositivo: ligue vários dispositivos à mesma conversa ção ou conversação baseada em texto e traduza opcionalmente mensagens enviadas entre eles. Saiba mais [neste artigo.](multi-device-conversation.md) 
- Suporte de reconhecimento de palavras-chave adicionado para android .aar pacote e suporte adicional para sabores x86 e x64. 
- Objetivo C: `SendMessage` `SetMessageProperty` e métodos adicionados ao `Connection` objeto. Consulte a documentação [aqui.](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)
- TTS C++ api `std::wstring` suporta agora como entrada de texto de síntese, removendo a necessidade de converter um fio em corda antes de passá-lo para o SDK. Veja os detalhes [aqui.](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: [Id linguístico](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) e [config linguagem fonte](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) estão agora disponíveis.
- JavaScript: Adicionei `Connection` uma funcionalidade para se opor a `receivedServiceMessage`passar por mensagens personalizadas do Serviço de Discurso como chamada .
- JavaScript: Suporte `FromHost API` adicional para facilitar o uso com recipientes on-prem e nuvens soberanas. Consulte a documentação [aqui.](speech-container-howto.md)
- JavaScript: Agora `NODE_TLS_REJECT_UNAUTHORIZED` honramos graças a uma contribuição de [orgads](https://github.com/orgads). Veja os detalhes [aqui.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Quebrar alterações**

- `OpenSSL`foi atualizado para a versão 1.1.1b e está está está ligado estáticamente à biblioteca central do Speech SDK para o Linux. Isto pode causar uma rutura `OpenSSL` se a sua `/usr/lib/ssl` caixa de entrada não tiver sido instalada no diretório do sistema. Por favor, verifique a [nossa documentação](how-to-configure-openssl-linux.md) em documentos do Speech SDK para resolver o problema.
- Mudamos o tipo de dados `WordLevelTimingResult.Offset` `int` devolvido `long` para C# `WordLevelTimingResults` para permitir o acesso ao quando os dados da fala são superiores a 2 minutos.
- `PushAudioInputStream`e `PullAudioInputStream` agora envie informações de cabeçalho ondulada para o Serviço de Fala com base em `AudioStreamFormat`, opcionalmente especificado quando foram criados. Os clientes devem agora utilizar o formato de entrada de [áudio suportado](how-to-use-audio-input-streams.md). Quaisquer outros formatos obterão resultados de reconhecimento sub-ideais ou poderão causar outros problemas. 

**Correções de erros**

- Consulte `OpenSSL` a atualização em alterações de Breaking acima. Corrigimos um acidente intermitente e um problema de desempenho (lock contention sob alta carga) em Linux e Java. 
- Java: Fez melhorias para o encerramento de objetos em cenários de alta condivisa.
- Reestruturou o nosso pacote NuGet. Removemos as três `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` cópias de e sob pastas lib, tornando o pacote NuGet menor e mais rápido para descarregar, e adicionamos cabeçalhos necessários para compilar algumas aplicações nativas C++.
- Amostras de arranque rápido fixas [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Estes estavam a sair sem exibir a exceção "microfone não encontrado" no Linux, MacOS, Windows.
- Crash SDK fixo com resultados de reconhecimento de fala longo saque em certos caminhos de código como [esta amostra](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Erro de implementação de SDK fixo no ambiente da Web App Azure para resolver [este problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Corrigiu um erro TTS ao utilizar várias `<voice>` etiquetas ou `<audio>` etiquetas para resolver este problema do [cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Corrigiu um erro TTS 401 quando o SDK é recuperado de suspenso.
- JavaScript: Fixou uma importação circular de dados áudio graças a uma contribuição da [euirim](https://github.com/euirim). 
- JavaScript: suporte adicional para definir propriedades de serviço, conforme adicionado em 1.7.
- JavaScript: corrigiu um problema em que um erro de ligação poderia resultar em tentativas contínuas e mal sucedidas de religação da websocket.

**Amostras**

- Adicionou [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)a amostra de reconhecimento de palavras-chave para Android .
- Adicionei a amostra TTS para o cenário do servidor [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Adicionou quickstarts de conversação multi-dispositivo para C# e C++ [aqui](quickstarts/multi-device-conversation.md).

**Outras alterações**

- Tamanho otimizado da biblioteca sdk no Android.
- SDK em 1.9.0 e em `int` `string` frente suporta tanto como tipos no campo de versão de assinatura de voz para Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Discurso SDK 1.8.0: lançamento de 2019-novembro

**Novas Funcionalidades**

- Adicionou `FromHost()` uma API, para facilitar o uso com contentores on-prem e nuvens soberanas.
- Deteção automática de linguagem de origem para reconhecimento da fala (em Java e C++)
- Objeto `SourceLanguageConfig` adicionado para reconhecimento da fala, usado para especificar línguas de origem esperadas (em Java e C++)
- Suporte `KeywordRecognizer` adicional no Windows (UWP), Android e iOS através dos pacotes NuGet e Unity
- Acrescentou a API de Conversa Remota java para fazer Transcrição de Conversação em lotes assíncronos.

**Quebrar alterações**

- Funcionalidades De Transcrição de `Microsoft.CognitiveServices.Speech.Transcription`Conversação movidas sob espaço de nome.
- Parte dos métodos de Transcrição `Conversation` de Conversa são transferidos para uma nova classe.
- Apoio deixado para 32 bits (ARMv7 e x86) iOS

**Correções de erros**

- Correção para acidente `KeywordRecognizer` se local for usado sem uma chave de subscrição de serviço de Fala válida

**Amostras**

- Amostra de Xamarin para`KeywordRecognizer`
- Amostra de unidade para`KeywordRecognizer`
- Amostras C++ e Java para Deteção Automática de Línguas De Origem.

## <a name="speech-sdk-170-2019-september-release"></a>Discurso SDK 1.7.0: lançamento de 2019-setembro

**Novas Funcionalidades**

- Suporte beta adicionado para Xamarin na Universal Windows Platform (UWP), Android e iOS
- Apoio adicional do iOS à Unidade
- Suporte `Compressed` adicional para entrada para ALaw, Mulaw, FLAC em Android, iOS e Linux
- Adicionado `SendMessageAsync` `Connection` na aula para enviar uma mensagem ao serviço
- Adicionado `SetMessageProperty` `Connection` na classe para definir propriedade de uma mensagem
- TTS acrescentou ligações para Java (Jre e Android), Python, Swift e Objective-C
- TTS adicionou suporte de reprodução para macOS, iOS e Android.
- Acrescentou informação de "limite de palavras" para tTS.

**Correções de erros**

- Problema fixo da IL2CPP na Unidade 2019 para Android
- Problema fixo com cabeçalhos mal formados na entrada de ficheiro ondulado sendo processado incorretamente
- Problema fixo com UUIDs não sendo único em algumas propriedades de conexão
- Corrigiu algumas advertências sobre os especificadores de nulidade nas ligações Swift (pode exigir pequenas alterações de código)
- Fixou um bug que fez com que as ligações de websocket fossem fechadas de forma graciosa sob carga de rede
- Corrigiu um problema no Android que por vezes resulta em iDs de impressão duplicados usados por`DialogServiceConnector`
- Melhorias na estabilidade das ligações através de interações multi-turn e o relato de falhas (através `Canceled` de eventos) quando ocorrem com`DialogServiceConnector`
- `DialogServiceConnector`início da sessão irá agora fornecer `ListenOnceAsync()` eventos corretamente, incluindo quando chamar durante um ativo`StartKeywordRecognitionAsync()`
- Abordou um acidente `DialogServiceConnector` associado a atividades a serem recebidas

**Amostras**

- Quickstart para Xamarin
- CPP Atualizado Quickstart com informações de Linux ARM64
- Unidade atualizada acelera com informação do iOS

## <a name="speech-sdk-160-2019-june-release"></a>Discurso SDK 1.6.0: Lançamento de 2019-junho

**Amostras**

- Amostras de quickstart para texto a falar sobre UWP e Unidade
- Amostra de quickstart para Swift no iOS
- Amostras de unidade para o reconhecimento e tradução de intenções de & do discurso
- Amostras de arranque rápido atualizadas para`DialogServiceConnector`

**Melhorias / Alterações**

- Espaço de nome de diálogo:
  - `SpeechBotConnector` mudou de nome para `DialogServiceConnector`
  - `BotConfig` mudou de nome para `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`foi remapeado para`DialogServiceConfig::FromBotSecret()`
  - Todos os clientes de Discurso de Linha Direta existentes continuam a ser apoiados após o renome
- Atualização Adaptador TTS REST para apoiar proxy, conexão persistente
- Melhorar a mensagem de erro quando uma região inválida é aprovada
- Swift/Objectivo-C:
  - Relatório de erro melhorado: Métodos que podem resultar num erro estão `NSError` agora presentes em duas versões: uma que expõe um objeto para manipulação de erros, e outra que levanta uma exceção. Os primeiros estão expostos a Swift. Esta alteração requer adaptações ao código Swift existente.
  - Melhor tratamento de eventos

**Correções de erros**

- Correção para TTS: onde `SpeakTextAsync` o futuro voltou sem esperar até que o áudio esteja concluído
- Correção para marejar cordas em C# para permitir o suporte linguístico completo
- Correção para o problema da aplicação central .NET para carregar biblioteca principal com quadro-alvo net461 em amostras
- Correção para problemas ocasionais para implantar bibliotecas nativas na pasta de saída em amostras
- Correção para fecho de tomada web de forma fiável
- Correção para possível colisão ao abrir uma ligação sob carga muito pesada em Linux
- Correção para metadados em falta no pacote-quadro para o macOS
- Corrigir problemas `pip install --user` com o Windows

## <a name="speech-sdk-151"></a>Discurso SDK 1.5.1

Trata-se de uma libertação de correção de erros e que afeta apenas o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

**Correções de erros**

- Fixe a Subscrição quando utilizado com transcrição de conversação.
- Fixe o bug na localização das palavras-chave para assistentes de voz.

## <a name="speech-sdk-150-2019-may-release"></a>Discurso SDK 1.5.0: lançamento de 2019-maio

**Novas funcionalidades**

- A spotting de palavras-chave (KWS) já está disponível para Windows e Linux. A funcionalidade KWS pode funcionar com qualquer tipo de microfone, suporte oficial da KWS, no entanto, está atualmente limitado às matrizes de microfone encontradas no hardware DK Azure Kinect ou no SDK de Dispositivos de Fala.
- A funcionalidade de dica de frases está disponível através do SDK. Para mais informações, consulte [aqui.](how-to-phrase-lists.md)
- A funcionalidade de transcrição de conversas está disponível através do SDK. Veja [aqui.](conversation-transcription-service.md)
- Adicione suporte para assistentes de voz utilizando o canal Direct Line Speech.

**Amostras**

- Adicionaram amostras para novas funcionalidades ou novos serviços suportados pelo SDK.

**Melhorias / Alterações**

- Adicionou várias propriedades de reconhecimento para ajustar o comportamento do serviço ou os resultados do serviço (como mascarar palavrões e outros).
- Agora pode configurar o reconhecível através das propriedades de configuração padrão, mesmo que tenha criado o reconhecível `FromEndpoint`.
- Objetivo C: `OutputFormat` a propriedade `SPXSpeechConfiguration`foi adicionada a .
- O SDK agora suporta debian 9 como uma distribuição Linux.

**Correções de erros**

- Corrigiu um problema em que o recurso do orador foi destrucada demasiado cedo no texto-a-discurso.

## <a name="speech-sdk-142"></a>Discurso SDK 1.4.2

Trata-se de uma libertação de correção de erros e que afeta apenas o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>Discurso SDK 1.4.1

Este é um lançamento apenas javaScript. Não foram adicionadas características. Foram feitas as seguintes correções:

- Evite que o pacote web carregue https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Discurso SDK 1.4.0: Lançamento de 2019-abril

**Novas funcionalidades**

- O SDK agora suporta o serviço de texto-a-fala como uma versão beta. É suportado no Windows e Linux Desktop de C++ e C#. Para mais informações, consulte a visão geral do [texto para a fala](text-to-speech.md#get-started).
- O SDK suporta agora ficheiros de áudio MP3 e Opus/OGG como ficheiros de entrada de fluxo. Esta funcionalidade está disponível apenas no Linux de C++ e C# e encontra-se atualmente em versão beta (mais detalhes [aqui).](how-to-use-codec-compressed-audio-input-streams.md)
- O SDK de Discurso para Java, .NET core, C++ e Objective-C ganharam apoio macOS. O suporte objectivo-C para o macOS encontra-se atualmente em versão beta.
- iOS: O SDK de Fala para iOS (Objectivo-C) também é publicado como Um CocoaPod.
- JavaScript: Suporte para microfone não predefinido como dispositivo de entrada.
- JavaScript: Suporte proxy para Node.js.

**Amostras**

- Foram adicionadas amostras para a utilização do SDK de Fala com C++ e com o Objectivo-C no macOS.
- Foram adicionadas amostras que demonstram a utilização do serviço de texto-a-fala.

**Melhorias / Alterações**

- Python: Propriedades adicionais dos resultados `properties` do reconhecimento são agora expostas através da propriedade.
- Para desenvolvimento adicional e suporte a depuração, pode redirecionar informações de registo sdK e diagnóstico para um ficheiro de registo (mais detalhes [aqui).](how-to-use-logging.md)
- JavaScript: Melhorar o desempenho do processamento de áudio.

**Correções de erros**

- Mac/iOS: Foi fixado um bug que levou a uma longa espera quando não foi possível estabelecer uma ligação ao serviço de Fala.
- Python: melhorar o manuseamento de erros para os argumentos em callbacks Python.
- JavaScript: Relatórios estatais corrigidos para o discurso terminaram no RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Discurso SDK 1.3.1: 2019-fevereiro

Trata-se de uma libertação de correção de erros e que afeta apenas o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

**Correção de insetos**

- Fixou uma fuga de memória ao utilizar a entrada do microfone. A entrada baseada no fluxo ou no ficheiro não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>Discurso SDK 1.3.0: Lançamento de 2019-fevereiro

**Novas Funcionalidades**

- O Speech SDK apoia a seleção `AudioConfig` do microfone de entrada através da aula. Isto permite-lhe transmitir dados áudio para o serviço Defala a partir de um microfone não predefinido. Para mais informações, consulte a documentação que descreve a [seleção](how-to-select-audio-input-devices.md)do dispositivo de entrada áudio . Esta funcionalidade ainda não está disponível no JavaScript.
- O Speech SDK agora apoia a Unidade numa versão beta. Forneça feedback através da secção de emissão no [repositório de amostras GitHub](https://aka.ms/csspeech/samples). Esta versão suporta a Unidade no Windows x86 e x64 (aplicações de desktop ou Universal Windows Platform) e Android (ARM32/64, x86). Mais informações estão disponíveis no nosso [Arranque de Unidade.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)
- O `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` ficheiro (enviado em lançamentos anteriores) já não é necessário. A funcionalidade está agora integrada no Núcleo SDK.

**Amostras**

Os seguintes novos conteúdos estão disponíveis no nosso [repositório de amostras:](https://aka.ms/csspeech/samples)

- Amostras `AudioConfig.FromMicrophoneInput`adicionais para .
- Amostras adicionais de Python para reconhecimento de intenções e tradução.
- Amostras adicionais `Connection` para a utilização do objeto no iOS.
- Amostras adicionais de Java para tradução com saída de áudio.
- Nova amostra para utilização da API DE API de [Transcrição](batch-transcription.md)do Lote .

**Melhorias / Alterações**

- Python
  - Melhor verificação de parâmetros `SpeechConfig`e mensagens de erro em .
  - Adicione suporte `Connection` para o objeto.
  - Suporte para Python de 32 bits (x86) no Windows.
  - O Discurso SDK para Python está fora de beta.
- iOS
  - O SDK é agora construído contra a versão 12.1 do iOS SDK.
  - O SDK suporta agora as versões iOS 9.2 e posteriormente.
  - Melhore a documentação de referência e corrija vários nomes de propriedade.
- JavaScript
  - Adicione suporte `Connection` para o objeto.
  - Adicione ficheiros de definição de tipo para JavaScript agregado
  - Apoio inicial e implementação para frases.
  - Devolução de propriedades com serviço JSON para reconhecimento
- Os DLLs do Windows contêm agora um recurso de versão.
- Se criar um `FromEndpoint` rereconhecimento, pode adicionar parâmetros diretamente ao URL do ponto final. Usando `FromEndpoint` não é possível configurar o reconhecível através das propriedades de configuração padrão.

**Correções de erros**

- O nome de utilizador de procuração vazio e a palavra-passe proxy não foram manuseados corretamente. Com esta versão, se definir o nome de utilizador proxy e a palavra-passe proxy para uma cadeia vazia, não serão submetidos quando ligarem ao proxy.
- O SessionId's criado pelo SDK nem sempre&nbsp;foi verdadeiramente aleatório para algumas línguas/ambientes. Adicionou uma inicialização aleatória do gerador para corrigir este problema.
- Melhorar o manuseamento do símbolo de autorização. Se pretender utilizar um símbolo de autorização, especifique no `SpeechConfig` e deixe a chave de subscrição vazia. Então crie o reconhecível como de costume.
- Em alguns `Connection` casos, o objeto não foi libertado corretamente. Esta questão foi corrigida.
- A amostra JavaScript foi fixada para suportar a saída de áudio para síntese de tradução também no Safari.

## <a name="speech-sdk-121"></a>Discurso SDK 1.2.1

Este é um lançamento apenas javaScript. Não foram adicionadas características. Foram feitas as seguintes correções:

- Fim do fogo no turn.end, não no discurso.end.
- Corrija o bug na bomba de áudio que não agende o próximo envio se o envio atual falhar.
- Corrija o reconhecimento contínuo com símbolo auth.
- Correção de bugs para diferentes pontos de reconhecimento/pontos finais.
- Melhorias na documentação.

## <a name="speech-sdk-120-2018-december-release"></a>Discurso SDK 1.2.0: lançamento de 2018-dezembro

**Novas Funcionalidades**

- Python
  - A versão Beta do suporte Python (3.5 ou superior) está disponível com esta versão. Para mais informações, consulte aqui(quickstart-python.md).
- JavaScript
  - O SDK de Discurso para JavaScript foi de código aberto. O código fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Apoiamos agora o Node.js, mais informações podem ser encontradas [aqui.](quickstart-js-node.md)
  - A restrição de comprimento para sessões de áudio foi removida, a reconexão acontecerá automaticamente sob a capa.
- `Connection`objeto
  - A `Recognizer`partir do, `Connection` pode aceder a um objeto. Este objeto permite-lhe iniciar explicitamente a ligação de serviço e subscrever para ligar e desligar eventos.
    (Esta funcionalidade ainda não está disponível no JavaScript e Python.)
- Suporte para Ubuntu 18.04.
- Android
  - Suporte ProGuard ativado durante a geração APK.

**Melhorias**

- Melhorias na utilização interna do fio, reduzindo o número de fios, fechaduras, mutexes.
- Relatório/informação melhorado de erro. Em vários casos, as mensagens de erro não foram propagadas até ao fim.
- Dependências de desenvolvimento atualizadas no JavaScript para usar módulos atualizados.

**Correções de erros**

- Fugas de memória fixas `RecognizeAsync`devido a uma incompatibilidade de tipo em .
- Em alguns casos, foram divulgadas exceções.
- Fixação da fuga de memória nos argumentos do evento de tradução.
- Corrigiu um problema de bloqueio na religação em sessões de longa duração.
- Corrigiu um problema que poderia levar à falta de resultadofinal para traduções falhadas.
- C#: Se `async` uma operação não fosse aguardada no fio principal, era possível que o reemitante pudesse ser eliminado antes de a tarefa de asincronização estar concluída.
- Java: Corrigiu um problema que resultou num acidente do VM java.
- Objetivo C: Mapeamento de enum fixo; RecognizedIntent foi devolvido `RecognizingIntent`em vez de .
- JavaScript: Desloque o formato de saída predefinido para 'simples' em `SpeechConfig`.
- JavaScript: Remover a inconsistência entre as propriedades do objeto de config no JavaScript e outros idiomas.

**Amostras**

- Atualizadas e fixas várias amostras (por exemplo, vozes de saída para tradução, etc.).
- As amostras de Node.js adicionadas no [repositório](https://aka.ms/csspeech/samples)da amostra .

## <a name="speech-sdk-110"></a>Discurso SDK 1.1.0

**Novas Funcionalidades**

- Suporte para Android x86/x64.
- Suporte proxy: `SpeechConfig` No objeto, pode agora ligar para uma função para definir as informações de procuração (nome de anfitrião, porta, nome de utilizador e senha). Esta funcionalidade ainda não se encontra disponível no iOS.
- Código de erro melhorado e mensagens. Se um reconhecimento devolveu um `Reason` erro, este já `CancellationDetails` definiu (em `Error`evento cancelado) ou (em resultado de reconhecimento) para . O evento cancelado agora contém `ErrorCode` dois `ErrorDetails`membros adicionais, e . Se o servidor devolver informações adicionais de erro com o erro reportado, estará agora disponível nos novos membros.

**Melhorias**

- Adicionou uma verificação adicional na configuração do reconhecedor e acrescentou uma mensagem de erro adicional.
- Melhor manuseamento do silêncio de longa data no meio de um ficheiro áudio.
- Pacote NuGet: para projetos .NET Framework, impede a construção com configuração AnyCPU.

**Correções de erros**

- Corrigiu várias exceções encontradas nos reconhecedores. Além disso, as exceções `Canceled` são apanhadas e convertidas em evento.
- Corrija uma fuga de memória na gestão de propriedades.
- Bug fixo no qual um ficheiro de entrada de áudio pode colidir com o reconhecedor.
- Fixou um bug onde os eventos poderiam ser recebidos após um evento de paragem de sessão.
- Corrigiu algumas condições de corrida na linha.
- Corrigiu um problema de compatibilidade iOS que poderia resultar num acidente.
- Melhorias de estabilidade para suporte ao microfone Android.
- Fixou um bug onde um reconhecível no JavaScript ignoraria a linguagem de reconhecimento.
- Fixou um bug `EndpointId` impedindo a definição (em alguns casos) no JavaScript.
- Alterou a ordem do parâmetro em AddIntent no JavaScript e acrescentou a assinatura JavaScript em falta. `AddIntent`

**Amostras**

- Adicione amostras C++ e C# para puxar e empurrar o uso do fluxo no [repositório](https://aka.ms/csspeech/samples)da amostra .

## <a name="speech-sdk-101"></a>Discurso SDK 1.0.1

Melhorias de fiabilidade e correções de bugs:

- Erro fatal potencial fixo devido à condição de corrida na eliminação do reconhecimento
- Erro fatal potencial fixo em caso de propriedades desmontadas.
- Adicione um erro adicional e verificação de parâmetros.
- Objectivo-C: Erro fatal fixo causado por um nome dominante no NSString.
- Objectivo-C: Visibilidade ajustada da API
- JavaScript: Fixo em relação a eventos e suas cargas.
- Melhorias na documentação.

No nosso [repositório de amostras,](https://aka.ms/csspeech/samples)foi adicionada uma nova amostra para o JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Discurso dos Serviços Cognitivos SDK 1.0.0: Lançamento de 2018 a setembro

**Novas funcionalidades**

- Apoio ao Objectivo-C no iOS. Confira o nosso [início rápido objective-C para iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Suporte para JavaScript no navegador. Confira o nosso [JavaScript quickstart](quickstart-js-browser.md).

**Quebrar alterações**

- Com esta libertação, são introduzidas várias alterações de rutura.
  Consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Discurso dos Serviços Cognitivos SDK 0.6.0: Lançamento de 2018-agosto

**Novas funcionalidades**

- As aplicações UWP construídas com o SDK de Discurso podem agora passar no Kit de Certificação de Aplicações do Windows (WACK).
  Confira o início rápido do [UWP.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)
- Suporte para .NET Standard 2.0 em Linux (Ubuntu 16.04 x64).
- Experimental: Suporte Java 8 no Windows (64-bit) e Linux (Ubuntu 16.04 x64).
  Confira o [Java Runtime Environment quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Mudança funcional**

- Exponha informações adicionais sobre erros de erro sobre erros de ligação.

**Quebrar alterações**

- Em Java (Android), a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` função já não requer um parâmetro de percurso. Agora o caminho é automaticamente detetado em todas as plataformas suportadas.
- O acessório da propriedade `EndpointUrl` em Java e C# foi removido.

**Correções de erros**

- Em Java, o resultado da síntese áudio no reconhecimento de tradução é implementado agora.
- Fixou um inseto que poderia causar fios inativos e um número aumentado de tomadas abertas e não utilizadas.
- Corrigiu um problema, em que um reconhecimento de longa duração poderia terminar no meio da transmissão.
- Fixou uma condição de corrida na paragem do reconhecível.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Discurso dos Serviços Cognitivos SDK 0.5.0: Lançamento de 2018 a julho

**Novas funcionalidades**

- Suporte a plataforma Android (API 23: Android 6.0 Marshmallow ou superior). Confira o arranque rápido do [Android.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)
- Suporte .NET Standard 2.0 no Windows. Confira o [arranque rápido do Núcleo .NET](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: Suporte uWP no Windows (versão 1709 ou posterior).
  - Confira o início rápido do [UWP.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)
  - Nota: As aplicações UWP construídas com o Speech SDK ainda não passam no Kit de Certificação de Aplicações do Windows (WACK).
- Apoie o reconhecimento de longo prazo com a reconexão automática.

**Alterações funcionais**

- `StartContinuousRecognitionAsync()`apoia o reconhecimento de longa duração.
- O resultado do reconhecimento contém mais campos. São compensados a partir do início e duração áudio (ambos em carrapatos) do `InitialSilenceTimeout` texto `InitialBabbleTimeout`reconhecido e valores adicionais que representam o estatuto de reconhecimento, por exemplo, e .
- Apoiar autorizaçõesToken para criar instâncias de fábrica.

**Quebrar alterações**

- Eventos `NoMatch` de reconhecimento: o `Error` tipo de evento foi fundido no evento.
- SpeechOutputFormat em C# foi `OutputFormat` renomeado para se manter alinhado com C++.
- O tipo de retorno `AudioInputStream` de alguns métodos da interface mudou ligeiramente:
  - Em Java, `read` o `long` método `int`regressa agora em vez de .
  - Em C#, `Read` o `uint` método `int`agora retorna em vez de .
  - Em C++, `Read` `GetFormat` os e `size_t` os `int`métodos voltam agora em vez de .
- C++: As instâncias de fluxos de entrada `shared_ptr`áudio agora podem ser passadas apenas como a .

**Correções de erros**

- Valores de retorno incorretos fixos no resultado quando `RecognizeAsync()` as horas fora.
- A dependência das bibliotecas de fundações de mídia no Windows foi removida. O SDK agora usa APIs core audio.
- Correção de documentação: Acrescentou uma página de [regiões](regions.md) para descrever as regiões apoiadas.

**Edição conhecida**

- O SDK de Discurso para Android não reporta resultados da síntese da fala para tradução. Esta emissão será corrigida no próximo lançamento.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Discurso dos Serviços Cognitivos SDK 0.4.0: Lançamento de 2018-junho

**Alterações funcionais**

- AudioInputStream

  Um reconhecível agora pode consumir um fluxo como a fonte de áudio. Para mais informações, consulte o que [fazer do guia.](how-to-use-audio-input-streams.md)

- Formato de saída detalhado

  Quando criar `SpeechRecognizer`um , `Detailed` pode `Simple` solicitar ou formato de saída. A `DetailedSpeechRecognitionResult` pontuação de confiança, texto reconhecido, forma lexical crua, forma normalizada e forma normalizada com profanação mascarada.

**Mudança de rutura**

- Mudou `SpeechRecognitionResult.Text` `SpeechRecognitionResult.RecognizedText` para a partir de C#.

**Correções de erros**

- Corrigiu um possível problema de chamada na camada USP durante o encerramento.
- Se um reconhecedor consumisse um ficheiro de entrada de áudio, manteve-se agarrado ao cabo do ficheiro mais tempo do que o necessário.
- Removeu vários impasses entre a bomba de mensagem e o reconhecível.
- Dispare `NoMatch` um resultado quando a resposta do serviço é cronometrada.
- As bibliotecas da fundação de mídia no Windows estão a ser carregadas de atraso. Esta biblioteca é necessária apenas para a entrada do microfone.
- A velocidade de upload para dados de áudio limita-se a cerca do dobro da velocidade de áudio original.
- No Windows, os conjuntos C# .NET são agora fortemente nomeados.
- Correção de `Region` documentação: é necessária informação para criar um reconhecedor.

Foram adicionadas mais amostras e estão constantemente a ser atualizadas. Para o mais recente conjunto de amostras, consulte o [repositório GitHub do Speech SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Discurso dos Serviços Cognitivos SDK 0.2.12733: Lançamento de 2018-maio

Este lançamento é o primeiro lançamento público de pré-visualização do SDK de Serviços Cognitivos.
