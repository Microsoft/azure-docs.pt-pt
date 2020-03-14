---
title: Notas de lançamento - Serviço de discurso
titleSuffix: Azure Cognitive Services
description: Um registo de registo de lançamentos, melhorias, correções de bugs e questões conhecidas.
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219645"
---
# <a name="release-notes"></a>Notas de versão
## <a name="speech-sdk-1100-2020-february-release"></a>Discurso SDK 1.10.0: Lançamento de 2020-fevereiro

**Novas funcionalidades**
 - Adicionou pacotes Python para apoiar o novo lançamento 3.8 da Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8C++ C#x64 suporte (, Java, Python).
   > [!NOTE] 
   > Os clientes devem configurar o OpenSSL de acordo com [estas instruções](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Suporte Linux ARM32 para Debian e Ubuntu.
 - DialogServiceConnector suporta agora um parâmetro opcional "bot ID" no BotFrameworkConfig. Este parâmetro permite a utilização de vários bots de discurso de linha direta com um único recurso de fala Azure. Sem o parâmetro especificado, será utilizado o bot predefinido (conforme determinado pela página de configuração do canal Direct Line Speech).
 - DialogServiceConnector agora tem uma propriedade SpeechActivityTemplate. O conteúdo desta corda JSON será usado pelo Direct Line Speech para pré-povoar uma grande variedade de campos apoiados em todas as atividades que atingem um bot de Discurso de Linha Direta, incluindo atividades geradas automaticamente em resposta a eventos como reconhecimento de fala.
 - O TTS utiliza agora a chave de subscrição para autenticação, reduzindo a primeira latência byte do primeiro resultado de síntese após a criação de um sintetizador.
 - Modelos atualizados de reconhecimento da fala para 19 locais para uma redução média da taxa de erro de palavra de 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Os novos modelos trazem melhorias significativas em vários domínios, incluindo Dictação, Transcrição de Call-Center e cenários de Indexação de Vídeo.

**Correções de bugs**
 - Bug fixo onde O Transcritodor de Conversação não aguardava corretamente em APIs JAVA 
 - Correção de emulador Android x86 para problema de Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Adicionar desaparecido (Get Conjunto)Métodos de propriedade para AudioConfig
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
- Objetivo C: métodos `SendMessage` e `SetMessageProperty` adicionados ao objeto `Connection`. Consulte a documentação [aqui.](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)
- A TTS C++ api suporta agora `std::wstring` como entrada de texto de síntese, removendo a necessidade de converter um fio em corda antes de passá-lo para o SDK. Veja os detalhes [aqui.](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: [Id linguístico](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) e [config de linguagem fonte](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) já estão disponíveis.
- JavaScript: Adicionei uma funcionalidade a `Connection` objeto para passar mensagens personalizadas do Serviço de Discurso como `receivedServiceMessage`de chamada .
- JavaScript: Suporte adicional para `FromHost API` para facilitar o uso com recipientes on-prem e nuvens soberanas. Consulte a documentação [aqui.](speech-container-howto.md)
- JavaScript: Agora honramos `NODE_TLS_REJECT_UNAUTHORIZED` graças a uma contribuição de [orgads](https://github.com/orgads). Veja os detalhes [aqui.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)


**Quebrar alterações**

- `OpenSSL` has been updated to version 1.1.1b and is statically linked to the Speech SDK core library for Linux. Isto pode causar uma rutura se a sua caixa de entrada `OpenSSL` não tiver sido instalada no diretório `/usr/lib/ssl` do sistema. Por favor, verifique a [nossa documentação](how-to-configure-openssl-linux.md) em documentos do Speech SDK para resolver o problema.
- Mudamos o tipo de C# dados devolvido seleções para `WordLevelTimingResult.Offset` de `int` para `long` para permitir o acesso a `WordLevelTimingResults` quando os dados da fala são superiores a 2 minutos.
- `PushAudioInputStream` e `PullAudioInputStream` agora enviam informações de cabeçalho de acenar para o Serviço de Fala com base em `AudioStreamFormat`, opcionalmente especificadas quando foram criadas. Os clientes devem agora utilizar o formato de entrada de [áudio suportado](how-to-use-audio-input-streams.md). Quaisquer outros formatos obterão resultados de reconhecimento sub-ideais ou poderão causar outros problemas. 


**Correções de bugs**

- Consulte a atualização `OpenSSL` em alterações de Breaking acima. Corrigimos um acidente intermitente e um problema de desempenho (lock contention sob alta carga) em Linux e Java. 
- Java: Fez melhorias para o encerramento de objetos em cenários de alta condivisa.
- Reestruturou o nosso pacote NuGet. Removemos as três cópias de `Microsoft.CognitiveServices.Speech.core.dll` e `Microsoft.CognitiveServices.Speech.extension.kws.dll` em pastas lib, tornando o pacote NuGet mais C++ pequeno e mais rápido para descarregar, e adicionamos cabeçalhos necessários para compilar algumas aplicações nativas.
- Amostras de arranque rápido fixas [aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Estes estavam a sair sem exibir a exceção "microfone não encontrado" no Linux, MacOS, Windows.
- Crash SDK fixo com resultados de reconhecimento de fala longo saque em certos caminhos de código como [esta amostra](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Erro de implementação de SDK fixo no ambiente da Web App Azure para resolver [este problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Corrigiu um erro TTS ao utilizar uma etiqueta de `<voice>` ou `<audio>` etiqueta para resolver [este problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Corrigiu um erro TTS 401 quando o SDK é recuperado de suspenso.
- JavaScript: Fixou uma importação circular de dados áudio graças a uma contribuição da [euirim](https://github.com/euirim). 
- JavaScript: suporte adicional para definir propriedades de serviço, conforme adicionado em 1.7.
- JavaScript: corrigiu um problema em que um erro de ligação poderia resultar em tentativas contínuas e mal sucedidas de religação da websocket.


**Amostras**

- Adicionou [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)a amostra de reconhecimento de palavras-chave para Android .
- Adicionei a amostra TTS para o cenário do servidor [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Adicionei a conversação C# multi-dispositivo saqueia para e C++ [aqui](quickstarts/multi-device-conversation.md).


**Outras alterações**

- Tamanho otimizado da biblioteca sdk no Android.
- SDK em 1.9.0 e em diante suporta tanto `int` como `string` tipos no campo de versão de assinatura de voz para Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Discurso SDK 1.8.0: lançamento de 2019-novembro

**Novas Funcionalidades**

- Adicionou um `FromHost()` API, para facilitar o uso com contentores on-prem e nuvens soberanas.
- Deteção automática de linguagem de origem C++para reconhecimento da fala (em Java e )
- Adicionado `SourceLanguageConfig` objeto para reconhecimento de voz, usado para C++especificar as línguas de origem esperadas (em Java e )
- Acrescentou `KeywordRecognizer` suporte no Windows (UWP), Android e iOS através dos pacotes NuGet e Unity
- Acrescentou a API de Conversa Remota java para fazer Transcrição de Conversação em lotes assíncronos.

**Quebrar alterações**

- As funcionalidades De Transcrição de Conversas moviam-se sob o espaço de nome `Microsoft.CognitiveServices.Speech.Transcription`.
- Parte dos métodos De Transcrição de Conversa são transferidos para a nova aula de `Conversation`.
- Apoio deixado para 32 bits (ARMv7 e x86) iOS

**Correções de bugs**

- Correção para acidente se `KeywordRecognizer` local for usado sem uma chave de subscrição de serviço de Fala válida

**Amostras**

- Amostra de Xamarin para `KeywordRecognizer`
- Amostra de unidade para `KeywordRecognizer`
- C++e amostras de Java para Deteção Automática de Línguas De Origem.

## <a name="speech-sdk-170-2019-september-release"></a>Discurso SDK 1.7.0: lançamento de 2019-setembro

**Novas Funcionalidades**

- Suporte beta adicionado para Xamarin na Universal Windows Platform (UWP), Android e iOS
- Apoio adicional do iOS à Unidade
- Acrescentou `Compressed` suporte de entrada para ALaw, Mulaw, FLAC no Android, iOS e Linux
- Acrescentou `SendMessageAsync` na aula de `Connection` por enviar uma mensagem ao serviço
- `SetMessageProperty` adicionado na classe `Connection` para definir propriedade de uma mensagem
- TTS acrescentou ligações para Java (Jre e Android), Python, Swift e Objective-C
- TTS adicionou suporte de reprodução para macOS, iOS e Android.
- Acrescentou informação de "limite de palavras" para tTS.

**Correções de bugs**

- Problema fixo da IL2CPP na Unidade 2019 para Android
- Problema fixo com cabeçalhos mal formados na entrada de ficheiro ondulado sendo processado incorretamente
- Problema fixo com UUIDs não sendo único em algumas propriedades de conexão
- Corrigiu algumas advertências sobre os especificadores de nulidade nas ligações Swift (pode exigir pequenas alterações de código)
- Fixou um bug que fez com que as ligações de websocket fossem fechadas de forma graciosa sob carga de rede
- Corrigiu um problema no Android que por vezes resulta em iDs de impressão duplicados usados por `DialogServiceConnector`
- Melhorias na estabilidade das ligações através de interações multi-turn e o relato de falhas (através de eventos `Canceled`) quando ocorrem com `DialogServiceConnector`
- `DialogServiceConnector` sessão vai agora proporcionar eventos adequados, incluindo quando ligar para `ListenOnceAsync()` durante um `StartKeywordRecognitionAsync()` ativo
- Abordou um acidente associado a `DialogServiceConnector` atividades a serem recebidas

**Amostras**

- Quickstart para Xamarin
- CPP Atualizado Quickstart com informações de Linux ARM64
- Unidade atualizada acelera com informação do iOS

## <a name="speech-sdk-160-2019-june-release"></a>Discurso SDK 1.6.0: Lançamento de 2019-junho

**Amostras**

- Amostras de quickstart para texto a falar sobre UWP e Unidade
- Amostra de quickstart para Swift no iOS
- Amostras de unidade para reconhecimento e tradução de discursos e intenções
- Amostras de arranque rápido atualizadas para `DialogServiceConnector`

**Melhorias / Alterações**

- Espaço de nome de diálogo:
  - `SpeechBotConnector` mudou de nome para `DialogServiceConnector`
  - `BotConfig` mudou de nome para `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` foi remapeada para `DialogServiceConfig::FromBotSecret()`
  - Todos os clientes de Discurso de Linha Direta existentes continuam a ser apoiados após o renome
- Atualização Adaptador TTS REST para apoiar proxy, conexão persistente
- Melhorar a mensagem de erro quando uma região inválida é aprovada
- Swift/Objectivo-C:
  - Relatório de erro melhorado: Métodos que podem resultar num erro estão agora presentes em duas versões: uma que expõe um objeto `NSError` para manipulação de erros, e outra que levanta uma exceção. Os primeiros estão expostos a Swift. Esta alteração requer adaptações ao código Swift existente.
  - Melhor tratamento de eventos

**Correções de bugs**

- Correção para TTS: onde `SpeakTextAsync` futuro voltou sem esperar até que o áudio esteja concluído
- Correção para a fixação de cordas C# para permitir o suporte linguístico completo
- Correção para o problema da aplicação central .NET para carregar biblioteca principal com quadro-alvo net461 em amostras
- Correção para problemas ocasionais para implantar bibliotecas nativas na pasta de saída em amostras
- Correção para fecho de tomada web de forma fiável
- Correção para possível colisão ao abrir uma ligação sob carga muito pesada em Linux
- Correção para metadados em falta no pacote-quadro para o macOS
- Correção para problemas com `pip install --user` no Windows

## <a name="speech-sdk-151"></a>Discurso SDK 1.5.1

Trata-se de uma libertação de correção de erros e que afeta apenas o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

**Correções de bugs**

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
- Agora pode configurar o reconhecível através das propriedades de configuração padrão, mesmo que tenha criado o reconhecimento `FromEndpoint`.
- Objetivo C: `OutputFormat` imóvel foi adicionado à `SPXSpeechConfiguration`.
- O SDK agora suporta debian 9 como uma distribuição Linux.

**Correções de bugs**

- Corrigiu um problema em que o recurso do orador foi destrucada demasiado cedo no texto-a-discurso.

## <a name="speech-sdk-142"></a>Discurso SDK 1.4.2

Trata-se de uma libertação de correção de erros e que afeta apenas o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>Discurso SDK 1.4.1

Este é um lançamento apenas javaScript. Não foram adicionadas características. Foram feitas as seguintes correções:

- Evite que o pacote web carregue https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Discurso SDK 1.4.0: Lançamento de 2019-abril

**Novas funcionalidades**

- O SDK agora suporta o serviço de texto-a-fala como uma versão beta. É suportado no Windows e C++ Linux Desktop de e C#. Para mais informações, consulte a visão geral do [texto para a fala](text-to-speech.md#get-started).
- O SDK suporta agora ficheiros de áudio MP3 e Opus/OGG como ficheiros de entrada de fluxo. Esta funcionalidade está disponível C++ apenas no Linux de e C# está atualmente em beta (mais detalhes [aqui).](how-to-use-codec-compressed-audio-input-streams.md)
- O SDK de Discurso para C++ Java, o núcleo .NET e o Objective-C ganharam apoio macOS. O suporte objectivo-C para o macOS encontra-se atualmente em versão beta.
- iOS: O SDK de Fala para iOS (Objectivo-C) também é publicado como Um CocoaPod.
- JavaScript: Suporte para microfone não predefinido como dispositivo de entrada.
- JavaScript: Suporte proxy para Node.js.

**Amostras**

- Foram adicionadas amostras para C++ a utilização do SDK da fala com e com o Objectivo-C no macOS.
- Foram adicionadas amostras que demonstram a utilização do serviço de texto-a-fala.

**Melhorias / Alterações**

- Python: Propriedades adicionais dos resultados do reconhecimento são agora expostas através da propriedade `properties`.
- Para desenvolvimento adicional e suporte a depuração, pode redirecionar informações de registo sdK e diagnóstico para um ficheiro de registo (mais detalhes [aqui).](how-to-use-logging.md)
- JavaScript: Melhorar o desempenho do processamento de áudio.

**Correções de bugs**

- Mac/iOS: Foi fixado um bug que levou a uma longa espera quando não foi possível estabelecer uma ligação ao serviço de Fala.
- Python: melhorar o manuseamento de erros para os argumentos em callbacks Python.
- JavaScript: Relatórios estatais corrigidos para o discurso terminaram no RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Discurso SDK 1.3.1: 2019-fevereiro

Trata-se de uma libertação de correção de erros e que afeta apenas o SDK nativo/gerido. Não está a afetar a versão JavaScript do SDK.

**Correção de insetos**

- Fixou uma fuga de memória ao utilizar a entrada do microfone. A entrada baseada no fluxo ou no ficheiro não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>Discurso SDK 1.3.0: Lançamento de 2019-fevereiro

**Novas Funcionalidades**

- O Speech SDK apoia a seleção do microfone de entrada através da aula `AudioConfig`. Isto permite-lhe transmitir dados áudio para o serviço Defala a partir de um microfone não predefinido. Para mais informações, consulte a documentação que descreve a [seleção](how-to-select-audio-input-devices.md)do dispositivo de entrada áudio . Esta funcionalidade ainda não está disponível no JavaScript.
- O Speech SDK agora apoia a Unidade numa versão beta. Forneça feedback através da secção de emissão no [repositório de amostras GitHub](https://aka.ms/csspeech/samples). Esta versão suporta a Unidade no Windows x86 e x64 (aplicações de desktop ou Universal Windows Platform) e Android (ARM32/64, x86). Mais informações estão disponíveis no nosso [Arranque de Unidade.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)
- O ficheiro `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em lançamentos anteriores) já não é necessário. A funcionalidade está agora integrada no Núcleo SDK.

**Amostras**

Os seguintes novos conteúdos estão disponíveis no nosso [repositório de amostras:](https://aka.ms/csspeech/samples)

- Amostras adicionais para `AudioConfig.FromMicrophoneInput`.
- Amostras adicionais de Python para reconhecimento de intenções e tradução.
- Amostras adicionais para a utilização do objeto `Connection` no iOS.
- Amostras adicionais de Java para tradução com saída de áudio.
- Nova amostra para utilização da API DE API de [Transcrição](batch-transcription.md)do Lote .

**Melhorias / Alterações**

- Python
  - Melhores mensagens de verificação e erro de parâmetros em `SpeechConfig`.
  - Adicione suporte para o objeto `Connection`.
  - Suporte para Python de 32 bits (x86) no Windows.
  - O Discurso SDK para Python está fora de beta.
- iOS
  - O SDK é agora construído contra a versão 12.1 do iOS SDK.
  - O SDK suporta agora as versões iOS 9.2 e posteriormente.
  - Melhore a documentação de referência e corrija vários nomes de propriedade.
- JavaScript
  - Adicione suporte para o objeto `Connection`.
  - Adicione ficheiros de definição de tipo para JavaScript agregado
  - Apoio inicial e implementação para frases.
  - Devolução de propriedades com serviço JSON para reconhecimento
- Os DLLs do Windows contêm agora um recurso de versão.
- Se criar um rereconhecimento `FromEndpoint` pode adicionar parâmetros diretamente ao URL do ponto final. Usando `FromEndpoint` não é possível configurar o reconhecível através das propriedades de configuração padrão.

**Correções de bugs**

- O nome de utilizador de procuração vazio e a palavra-passe proxy não foram manuseados corretamente. Com esta versão, se definir o nome de utilizador proxy e a palavra-passe proxy para uma cadeia vazia, não serão submetidos quando ligarem ao proxy.
- O SessionId's criado pelo SDK nem sempre foi verdadeiramente aleatório para algumas línguas&nbsp;/ambientes. Adicionou uma inicialização aleatória do gerador para corrigir este problema.
- Melhorar o manuseamento do símbolo de autorização. Se pretender utilizar um símbolo de autorização, especifique no `SpeechConfig` e deixe a chave de subscrição vazia. Então crie o reconhecível como de costume.
- Em alguns casos, o `Connection` objeto não foi libertado corretamente. Este problema foi corrigido.
- A amostra JavaScript foi fixada para suportar a saída de áudio para síntese de tradução também no Safari.

## <a name="speech-sdk-121"></a>Discurso SDK 1.2.1

Este é um lançamento apenas javaScript. Não foram adicionadas características. Foram feitas as seguintes correções:

- Fim do fogo no turn.end, não no discurso.end.
- Corrija o bug na bomba de áudio que não agende o próximo envio se o envio atual falhar.
- Corrija o reconhecimento contínuo com símbolo auth.
- Correção de bugs para diferentes pontos de reconhecimento/pontos finais.
- Melhorias de documentação.

## <a name="speech-sdk-120-2018-december-release"></a>Discurso SDK 1.2.0: lançamento de 2018-dezembro

**Novas Funcionalidades**

- Python
  - A versão Beta do suporte Python (3.5 ou superior) está disponível com esta versão. Para mais informações, consulte aqui(quickstart-python.md).
- JavaScript
  - O SDK de Discurso para JavaScript foi de código aberto. O código fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Apoiamos agora o Node.js, mais informações podem ser encontradas [aqui.](quickstart-js-node.md)
  - A restrição de comprimento para sessões de áudio foi removida, a reconexão acontecerá automaticamente sob a capa.
- objeto `Connection`
  - A partir do `Recognizer`, pode aceder a um objeto `Connection`. Este objeto permite-lhe iniciar explicitamente a ligação de serviço e subscrever para ligar e desligar eventos.
    (Esta funcionalidade ainda não está disponível no JavaScript e Python.)
- Suporte para Ubuntu 18.04.
- Android
  - Suporte ProGuard ativado durante a geração APK.

**Melhorias**

- Melhorias na utilização interna do fio, reduzindo o número de fios, fechaduras, mutexes.
- Relatório/informação melhorado de erro. Em vários casos, as mensagens de erro não foram propagadas até ao fim.
- Dependências de desenvolvimento atualizadas no JavaScript para usar módulos atualizados.

**Correções de bugs**

- Fugas de memória fixas devido a uma incompatibilidade de tipo em `RecognizeAsync`.
- Em alguns casos, foram divulgadas exceções.
- Fixação da fuga de memória nos argumentos do evento de tradução.
- Corrigiu um problema de bloqueio na religação em sessões de longa duração.
- Corrigiu um problema que poderia levar à falta de resultadofinal para traduções falhadas.
- C#: Se não se aguarda uma operação `async` no fio principal, era possível que o responsalheiro pudesse ser eliminado antes de a tarefa de asincronização estar concluída.
- Java: Corrigiu um problema que resultou num acidente do VM java.
- Objetivo C: Mapeamento de enum fixo; RecognizedIntent foi devolvido em vez de `RecognizingIntent`.
- JavaScript: Desloque o formato de saída predefinido para 'simples' em `SpeechConfig`.
- JavaScript: Remover a inconsistência entre as propriedades do objeto de config no JavaScript e outros idiomas.

**Amostras**

- Atualizadas e fixas várias amostras (por exemplo, vozes de saída para tradução, etc.).
- As amostras de Node.js adicionadas no [repositório](https://aka.ms/csspeech/samples)da amostra .

## <a name="speech-sdk-110"></a>Discurso SDK 1.1.0

**Novas Funcionalidades**

- Suporte para Android x86/x64.
- Suporte proxy: No `SpeechConfig` objeto, pode agora ligar para uma função para definir as informações de procuração (nome de anfitrião, porta, nome de utilizador e palavra-passe). Esta funcionalidade ainda não está disponível no iOS.
- Código de erro melhoradas e mensagens. Se um reconhecimento devolveu um erro, este já definiu `Reason` (em evento cancelado) ou `CancellationDetails` (em resultado de reconhecimento) para `Error`. O evento cancelado conta agora com mais dois membros, `ErrorCode` e `ErrorDetails`. Se o servidor devolveu informações de erro adicionais com o erro comunicado, agora estará disponível em novos membros.

**Melhorias**

- Adicionada a verificação adicional na configuração do reconhecedor e mensagem de erro adicionais foram adicionados.
- Processamento melhorado de longa data silêncio no meio de um arquivo de áudio.
- Pacote NuGet: para projetos do .NET Framework, ele impede a criação com a configuração de AnyCPU.

**Correções de bugs**

- Corrigido várias exceções encontradas no reconhecedores. Além disso, as exceções são capturadas e convertidas em `Canceled` evento.
- Corrigi um vazamento de memória na gestão de propriedade.
- Foi corrigido o erro em que um ficheiro de entrada de áudio poderá provocar falhas o reconhecedor.
- Foi corrigido um erro em que os eventos poderiam ser recebidos após um evento de paragem de sessão.
- Corrigido algumas condições de corrida em threading.
- Corrigido problema de compatibilidade que pode resultar numa falha de um iOS.
- Melhorias de estabilidade para o suporte de microfone Android.
- Foi corrigido um erro em que um reconhecedor em JavaScript ignorará o idioma de reconhecimento.
- Fixou um bug impedindo a definição do `EndpointId` (em alguns casos) no JavaScript.
- Alterou a ordem do parâmetro em AddIntent no JavaScript e acrescentou a falta `AddIntent` assinatura JavaScript.

**Amostras**

- C++ Adicionadas C# e amostras para puxar e empurrar o uso do fluxo no [repositório](https://aka.ms/csspeech/samples)da amostra .

## <a name="speech-sdk-101"></a>Discurso SDK 1.0.1

Melhorias de confiabilidade e correções de erros:

- Correção do erro fatal potencial devido a condição de corrida em reconhecedor descarte
- Erro fatal potencial fixo em caso de propriedades não definidas.
- Foram adicionados erros adicionais e verificação de parâmetros.
- Foi corrigido Objective-c: possível erro fatal causado por nome substituindo no NSString.
- Objective-c: ajustado visibilidade da API
- JavaScript: Corrigido em relação a eventos e respetivos payloads.
- Melhorias de documentação.

No nosso [repositório de amostras,](https://aka.ms/csspeech/samples)foi adicionada uma nova amostra para o JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de voz dos serviços cognitivos 1.0.0: versão de Setembro de 2018

**Novas funcionalidades**

- Suporte para Objective-C no iOS. Confira o nosso [início rápido objective-C para iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Suporte para JavaScript no browser. Confira o nosso [JavaScript quickstart](quickstart-js-browser.md).

**Quebrar alterações**

- Com esta libertação, são introduzidas várias alterações de rutura.
  Consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de voz dos serviços cognitivos 0.6.0: versão de Agosto de 2018

**Novas funcionalidades**

- Aplicações do UWP criadas com o SDK de voz, agora podem passar o Kit de certificação de aplicações de Windows (WACK).
  Confira o início rápido do [UWP.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)
- Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
- Experimental: Suportar Java 8 (64-bit) do Windows e Linux (Ubuntu 16.04 x64).
  Confira o [Java Runtime Environment quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Mudança funcional**

- Expor informações de detalhes de erro adicionais sobre os erros de ligação.

**Quebrar alterações**

- Em Java (Android), a função `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` já não requer um parâmetro de percurso. Agora o caminho é detetado automaticamente em todas as plataformas suportadas.
- O acessório da propriedade `EndpointUrl` em C# Java e foi removido.

**Correções de bugs**

- No Java, o resultado de síntese de áudio no reconhecedor de tradução é agora implementado.
- Foi corrigido um erro que pode causar threads Inativas e um número maior de soquetes abertos e não utilizados.
- Foi corrigido um problema, onde um reconhecimento de longa execução foi possível terminar no meio da transmissão.
- Fixo, uma condição de corrida em encerramento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de voz dos serviços cognitivos versão 0.5.0: versão de Julho de 2018

**Novas funcionalidades**

- Plataforma Android de suporte (API 23: Android 6.0 Marshmallow ou superior). Confira o arranque rápido do [Android.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)
- Suporta o .NET Standard 2.0 no Windows. Confira o [arranque rápido do Núcleo .NET](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: Suporte UWP no Windows (versão 1709 ou posterior).
  - Confira o início rápido do [UWP.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)
  - Nota: As aplicações UWP compiladas com o SDK de voz não ainda passam o Kit de certificação de aplicações de Windows (WACK).
- Suporte a reconhecimento de execução longa com a nova ligação automática.

**Alterações funcionais**

- `StartContinuousRecognitionAsync()` apoia o reconhecimento a longo prazo.
- O resultado do reconhecimento contém mais campos. São compensados a partir do início e duração áudio (ambos em carraças) do texto reconhecido e valores adicionais que representam o estatuto de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- AuthorizationToken de suporte para a criação de instâncias de fábrica.

**Quebrar alterações**

- Eventos de reconhecimento: `NoMatch` tipo de evento foi fundido no evento `Error`.
- SpeechOutputFormat C# in foi renomeado para `OutputFormat` C++para se manter alinhado com .
- O tipo de retorno de alguns métodos da interface `AudioInputStream` mudou ligeiramente:
  - Em Java, o método `read` regressa agora `long` em vez de `int`.
  - Dentro, C#o método `Read` devolve agora `uint` em vez de `int`.
  - Nos C++métodos de `Read` e `GetFormat` agora regressam `size_t` em vez de `int`.
- C++: As instâncias de fluxos de entrada áudio podem agora ser passadas apenas como `shared_ptr`.

**Correções de bugs**

- Valores de retorno incorretos fixos no resultado quando `RecognizeAsync()` vezes fora.
- A dependência de bibliotecas de base de dados no Windows foi removida. O SDK utiliza agora as APIs de áudio de núcleo.
- Correção de documentação: Acrescentou uma página de [regiões](regions.md) para descrever as regiões apoiadas.

**Edição conhecida**

- O SDK de voz para Android não reporta os resultados de síntese de fala para a tradução. Este problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de voz dos serviços cognitivos 0.4.0: versão de Junho de 2018

**Alterações funcionais**

- AudioInputStream

  Um reconhecedor agora, pode utilizar um fluxo como a origem de áudio. Para mais informações, consulte o que [fazer do guia.](how-to-use-audio-input-streams.md)

- Formato de saída detalhada

  Quando criar um `SpeechRecognizer`, pode solicitar `Detailed` ou `Simple` formato de saída. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, forma lexical crua, forma normalizada e forma normalizada com profanação mascarada.

**Mudança de rutura**

- Mudou para `SpeechRecognitionResult.Text` C#de `SpeechRecognitionResult.RecognizedText` em .

**Correções de bugs**

- Foi corrigido um problema de possíveis de retorno de chamada na camada de USP durante o encerramento.
- Se um reconhecedor consumido um ficheiro de entrada de áudio, ele foi manter o identificador de ficheiro maior do que o necessário.
- Remover vários deadlocks entre o bombardeamento de mensagens e o reconhecedor.
- Dispare um resultado `NoMatch` quando a resposta do serviço é cronometrada.
- As bibliotecas de base de dados no Windows são carregado de atraso. Esta biblioteca é necessária para apenas a entrada do microfone.
- A velocidade de carregamento de dados de áudio é limitada a sobre duas vezes a velocidade de áudio original.
- No Windows, assemblies do .NET c# agora são strong nomeados.
- Correção de documentação: `Region` é necessária informação para criar um reconhecedor.

Mais exemplos foram adicionados e estão constantemente a ser atualizados. Para o mais recente conjunto de amostras, consulte o [repositório GitHub do Speech SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de voz dos serviços cognitivos 0.2.12733: versão de Maio de 2018

Esta versão é a primeira versão de pré-visualização pública do SDK de voz dos serviços cognitivos.
