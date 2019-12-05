---
title: Notas de versão – serviço de fala
titleSuffix: Azure Cognitive Services
description: Veja um log em execução de versões de recursos, melhorias, correções de bugs e problemas conhecidos do serviço de fala.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: 74b34a1d65f6beb097a649658c508da6ff48f1ac
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816597"
---
# <a name="release-notes"></a>Notas de versão

## <a name="speech-sdk-180-2019-november-release"></a>SDK de fala 1.8.0:2019 – versão de novembro

**Novos recursos**

- Adicionada uma API FromHost () para facilitar o uso com contêineres locais e nuvens soberanas.
- Detecção de Idioma de origem automática adicionada para reconhecimento de fala (em C++Java e)
- Adicionado o objeto SourceLanguageConfig para reconhecimento de fala, usado para especificar os idiomas de origem esperados (em Java e C++)
- Adicionado suporte a KeywordRecognizer no Windows (UWP), Android e iOS por meio dos pacotes NuGet e Unity
- API Java de conversa remota adicionada para fazer a transcrição de conversa em lotes assíncronos.

**Alterações recentes**

- Funcionalidades de transistores de conversa movidas no namespace Microsoft. Cognitivaservices. Speech. transcrição.
- Parte dos métodos de transistores de conversa são movidos para a nova classe de conversa.
- Suporte removido para iOS de 32 bits (ARMv7 e x86)

**Correções de erros**

- Correção de falha se KeywordRecognizer local for usado sem uma chave de assinatura de serviço de fala válida

**Amostras**

- Exemplo do Xamarin para KeywordRecognizer
- Exemplo de Unity para KeywordRecognizer
- C++e exemplos de Java para Detecção de Idioma de origem automática.

## <a name="speech-sdk-170-2019-september-release"></a>SDK de fala 1.7.0:2019 – versão de setembro

**Novos recursos**

- Adicionado suporte beta para Xamarin em Plataforma Universal do Windows (UWP), Android e iOS
- Adicionado suporte do iOS para o Unity
- Adição de suporte de entrada compactada para ALaw, mulaw, FLAC no Android, iOS e Linux
- Adicionado SendMessageAsync na classe de conexão para enviar uma mensagem para o serviço
- Setmessageproperty adicionado na classe de conexão para definir a propriedade de uma mensagem
- Ligações adicionadas a TTS para Java (JRE e Android), Python, Swift e Objective-C
- Suporte à reprodução de TTS adicionado para macOS, iOS e Android.
- Informações de "limite de palavras" adicionadas para TTS.

**Correções de erros**

- Correção do problema de compilação do IL2CPP no Unity 2019 para Android
- Corrigido o problema com cabeçalhos malformados na entrada do arquivo WAV que está sendo processada incorretamente
- Corrigido o problema com UUIDs que não são exclusivos em algumas propriedades de conexão
- Correção de alguns avisos sobre especificadores de nulidade nas associações Swift (pode exigir pequenas alterações de código)
- Correção de um bug que fazia com que as conexões WebSocket fosse fechadas de acordo com a carga de rede
- Correção de um problema no Android que às vezes resulta em IDs de impressão duplicadas usadas pelo DialogServiceConnector
- Melhorias na estabilidade de conexões entre interativações de várias transformações e o relatório de falhas (por meio de eventos cancelados) quando ocorrem com DialogServiceConnector
- A sessão DialogServiceConnector começará agora a fornecer eventos, inclusive ao chamar ListenOnceAsync () durante um StartKeywordRecognitionAsync ativo ()
- Foi corrigido uma falha associada às atividades DialogServiceConnector que estão sendo recebidas

**Amostras**

- Início rápido para Xamarin
- Atualizado o início rápido do CPP com as informações de ARM64 do Linux
- Guia de início rápido do Unity atualizado com informações do iOS

## <a name="speech-sdk-160-2019-june-release"></a>SDK de fala 1.6.0:2019 – versão de junho

**Amostras**

- Exemplos de início rápido para texto para fala sobre UWP e Unity
- Exemplo de início rápido para Swift no iOS
- Amostras de Unity para & de fala Reconhecimento de intenção e tradução
- Exemplos de guia de início rápido atualizados para DialogServiceConnector

**Melhorias/alterações**

- Namespace de caixa de diálogo:
  - SpeechBotConnector foi renomeado para DialogServiceConnector
  - BotConfig foi renomeado para DialogServiceConfig
  - BotConfig:: FromChannelSecret () foi remapeado para DialogServiceConfig:: FromBotSecret ()
  - Todos os clientes de fala de linha direta existentes continuam com suporte após a renomeação
- Atualizar o adaptador REST TTS para dar suporte a proxy, conexão persistente
- Melhorar a mensagem de erro quando uma região inválida é passada
- Swift/Objective-C:
  - Melhoria no relatório de erros: os métodos que podem resultar em um erro agora estão presentes em duas versões: uma que expõe um objeto `NSError` para tratamento de erros e outra que gera uma exceção. O primeiro é exposto a Swift. Essa alteração requer adaptações para o código Swift existente.
  - Manipulação de eventos aprimorada

**Correções de erros**

- Correção para TTS: em que SpeakTextAsync futuro retornou sem esperar até que o áudio tenha concluído a renderização
- Correção para o marshaling de C# cadeias de caracteres no para habilitar o suporte a idiomas completos
- Correção do problema do aplicativo .NET Core para carregar a biblioteca principal com a estrutura de destino net461 em exemplos
- Correção de problemas ocasionais para implantar bibliotecas nativas na pasta de saída em exemplos
- Correção de fechamento de soquete da Web confiável
- Correção de uma possível falha ao abrir uma conexão sob carga muito pesada no Linux
- Correção de metadados ausentes no pacote de estrutura para macOS
- Correção de problemas com o `pip install --user` no Windows

## <a name="speech-sdk-151"></a>1\.5.1 SDK de fala

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

**Correções de erros**

- Corrigir o FromSubscription quando usado com a transcrição de conversa.
- Correção de bug na palavra-chave para assistentes de voz.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-maio de lançamento

**Novos recursos**

- A palavra-chave (KWS) está disponível agora para Windows e Linux. A funcionalidade do KWS pode funcionar com qualquer tipo de microfone, o suporte oficial do KWS, no entanto, está limitado atualmente às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK dos dispositivos de fala.
- A funcionalidade de dica de frase está disponível por meio do SDK. Para obter mais informações, consulte [aqui](how-to-phrase-lists.md).
- A funcionalidade de transcrição de conversa está disponível por meio do SDK. Consulte [aqui](conversation-transcription-service.md).
- Adicione suporte para assistentes de voz usando o canal de fala de linha direta.

**Amostras**

- Foram adicionadas amostras para novos recursos ou novos serviços com suporte no SDK.

**Melhorias/alterações**

- Adicionada várias propriedades de reconhecedor para ajustar o comportamento do serviço ou os resultados do serviço (como mascarar profanação e outros).
- Agora você pode configurar o reconhecedor por meio das propriedades de configuração padrão, mesmo que você tenha criado o reconhecedor `FromEndpoint`.
- Objective-C: `OutputFormat` Propriedade foi adicionada a SPXSpeechConfiguration.
- O SDK agora dá suporte a Debian 9 como uma distribuição do Linux.

**Correções de erros**

- Corrigido um problema em que o recurso do orador foi destruido muito cedo em texto para fala.

## <a name="speech-sdk-142"></a>SDK 1.4.2 de fala

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>1\.4.1 SDK de fala

Esta é uma versão somente para JavaScript. Nenhum recurso foi adicionado. As seguintes correções foram feitas:

- Impedir que o pacote da Web carregue HTTPS-proxy-Agent.

## <a name="speech-sdk-140-2019-april-release"></a>SDK de fala 1.4.0:2019 – versão de abril

**Novos recursos**

- O SDK agora dá suporte ao serviço de conversão de texto em fala como uma versão beta. Ele tem suporte na área de trabalho do Windows C++ e C#do Linux de e. Para obter mais informações, consulte a [visão geral de conversão de texto em fala](text-to-speech.md#get-started-with-text-to-speech).
- O SDK agora dá suporte a arquivos de áudio MP3 e Opus/OGG como arquivos de entrada de fluxo. Esse recurso está disponível apenas no Linux a C++ partir C# do e no, no momento, está em beta (mais detalhes [aqui](how-to-use-codec-compressed-audio-input-streams.md)).
- O SDK de fala para Java, .NET Core C++ e Objective-C ganhou suporte para MacOS. O suporte a Objective-C para macOS está atualmente em beta.
- iOS: o SDK de fala para iOS (Objective-C) agora também é publicado como um CocoaPod.
- JavaScript: suporte para microfone não padrão como um dispositivo de entrada.
- JavaScript: suporte de proxy para node. js.

**Amostras**

- Foram adicionados exemplos de uso do SDK C++ de fala com e com o Objective-C no MacOS.
- Exemplos que demonstram o uso do serviço de conversão de texto em fala foram adicionados.

**Melhorias/alterações**

- Python: as propriedades adicionais dos resultados de reconhecimento agora são expostas por meio da propriedade `properties`.
- Para obter suporte adicional para desenvolvimento e depuração, você pode redirecionar as informações de log e diagnóstico do SDK para um arquivo de log (mais detalhes [aqui](how-to-use-logging.md)).
- JavaScript: melhorar o desempenho de processamento de áudio.

**Correções de erros**

- Mac/iOS: um bug que levou a uma longa espera quando uma conexão com o serviço de fala não pôde ser estabelecida foi corrigida.
- Python: melhorar o tratamento de erros para argumentos em retornos de chamada do Python.
- JavaScript: o relatório de estado incorreto corrigido para a fala terminou em RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>SDK de fala 1.3.1:2019 – atualização de fevereiro

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

**Correção de bug**

- Foi corrigido um vazamento de memória ao usar a entrada do microfone. Baseada em fluxo ou entrada de arquivo não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>SDK de fala 1.3.0:2019 – versão de fevereiro

**Novos recursos**

- O SDK de fala dá suporte à seleção do microfone de entrada por meio da classe AudioConfig. Isso permite que você transmita dados de áudio para o serviço de fala de um microfone não padrão. Para obter mais informações, consulte a documentação que descreve a [seleção de dispositivo de entrada de áudio](how-to-select-audio-input-devices.md). Esse recurso ainda não está disponível no JavaScript.
- O SDK de fala agora dá suporte a Unity em uma versão beta. Forneça comentários na seção de problemas no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples). Esta versão dá suporte a Unity no Windows x86 e x64 (aplicativos de área de trabalho ou Plataforma Universal do Windows) e Android (ARM32/64, x86). Mais informações estão disponíveis em nosso guia de [início rápido do Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- O arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada ao SDK do Core.

**Amostras**

O novo conteúdo a seguir está disponível em nosso [repositório de exemplo](https://aka.ms/csspeech/samples):

- Exemplos adicionais para AudioConfig. FromMicrophoneInput.
- Exemplos adicionais do Python para reconhecimento e tradução de intenção.
- Exemplos adicionais para usar o objeto de conexão no iOS.
- Exemplos de Java adicionais para tradução com saída de áudio.
- Novo exemplo para uso da [API REST de transcrição do lote](batch-transcription.md).

**Melhorias/alterações**

- Python
  - Verificação de parâmetro e mensagens de erro aprimoradas em SpeechConfig.
  - Adicione suporte para o objeto de conexão.
  - Suporte para Python de 32 bits (x86) no Windows.
  - O SDK de fala para Python está fora de beta.
- iOS
  - O SDK agora é criado em relação à versão 12,1 do SDK do iOS.
  - O SDK agora dá suporte às versões 9,2 e posteriores do iOS.
  - Melhore a documentação de referência e corrija vários nomes de propriedade.
- JavaScript
  - Adicione suporte para o objeto de conexão.
  - Adicionar arquivos de definição de tipo para JavaScript agrupado
  - Suporte inicial e implementação para dicas de frase.
  - Retornar a coleção de propriedades com o serviço JSON para reconhecimento
- As DLLs do Windows agora contêm um recurso de versão.
- Se você criar um reconhecedor `FromEndpoint` poderá adicionar parâmetros diretamente à URL do ponto de extremidade. Usando `FromEndpoint` você não pode configurar o reconhecedor por meio das propriedades de configuração padrão.

**Correções de erros**

- O nome de usuário do proxy vazio e a senha do proxy não foram tratados corretamente. Com esta versão, se você definir o nome de usuário do proxy e a senha do proxy para uma cadeia de caracteres vazia, elas não serão enviadas ao se conectar ao proxy.
- A SessionId criada pelo SDK nem sempre era verdadeiramente aleatória para alguns idiomas&nbsp;/ambientes. Adicionada a inicialização aleatória do gerador para corrigir esse problema.
- Melhorar o tratamento do token de autorização. Se você quiser usar um token de autorização, especifique no SpeechConfig e deixe a chave de assinatura vazia. Em seguida, crie o reconhecedor como de costume.
- Em alguns casos, o objeto de conexão não foi liberado corretamente. Este problema foi corrigido.
- O exemplo de JavaScript foi corrigido para dar suporte à saída de áudio para síntese de tradução também no Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Esta é uma versão somente para JavaScript. Nenhum recurso foi adicionado. As seguintes correções foram feitas:

- Fim do fogo do fluxo em virar. end, não em Speech. end.
- Corrija o bug no bombeamento de áudio que não agendou o próximo envio se o envio atual falhar.
- Corrija o reconhecimento contínuo com o token de autenticação.
- Correção de bug para diferentes reconhecedor/pontos de extremidade.
- Melhorias de documentação.

## <a name="speech-sdk-120-2018-december-release"></a>SDK de fala 1.2.0:2018 – versão de dezembro

**Novos recursos**

- Python
  - A versão beta do suporte do Python (3,5 e superior) está disponível nesta versão. Para obter mais informações, consulte aqui] (início rápido-python.md).
- JavaScript
  - O SDK de fala para JavaScript foi aberto de código-fonte. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Agora há suporte para node. js, mais informações podem ser encontradas [aqui](quickstart-js-node.md).
  - A restrição de comprimento para sessões de áudio foi removida, a reconexão ocorrerá automaticamente sob a cobertura.
- Objeto de conexão
  - No reconhecedor, você pode acessar um objeto de conexão. Esse objeto permite que você inicie explicitamente a conexão de serviço e assine eventos de conexão e desconexão.
    (Esse recurso ainda não está disponível no JavaScript e no Python.)
- Suporte para Ubuntu 18, 4.
- Android
  - Suporte ao PROGuard habilitado durante a geração de APK.

**Melhorias**

- Melhorias no uso de thread interno, reduzindo o número de threads, bloqueios, mutexes.
- Relatório de erros/informações aprimorados. Em vários casos, as mensagens de erro não foram propagadas até o fim.
- Dependências de desenvolvimento atualizadas em JavaScript para usar módulos atualizados.

**Correções de erros**

- Correção de vazamentos de memória devido a uma incompatibilidade de tipo em RecognizeAsync.
- Em alguns casos, as exceções estavam sendo vazadas.
- Corrigindo o vazamento de memória em argumentos de evento de tradução.
- Corrigido um problema de bloqueio na reconexão em sessões de longa execução.
- Correção de um problema que poderia levar a um resultado final ausente para traduções com falha.
- C#: Se uma operação assíncrona não foi aguardada no thread principal, era possível que o reconhecedor pudesse ser descartado antes de a tarefa assíncrona ser concluída.
- Java: Corrigido um problema que resulta em uma falha da VM Java.
- Objective-C: mapeamento de enumeração fixo; RecognizedIntent foi retornado em vez de RecognizingIntent.
- JavaScript: defina o formato de saída padrão como "simples" em SpeechConfig.
- JavaScript: removendo a inconsistência entre as propriedades no objeto de configuração em JavaScript e em outras linguagens.

**Amostras**

- Atualização e correção de vários exemplos (por exemplo, vozes de saída para tradução, etc.).
- Exemplos de Node. js adicionados no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Novos recursos**

- Suporte para Android x86/x64.
- Suporte a proxy: no objeto SpeechConfig, agora você pode chamar uma função para definir as informações de proxy (nome do host, porta, nome de usuário e senha). Esta funcionalidade ainda não está disponível no iOS.
- Código de erro melhoradas e mensagens. Se um reconhecimento devolveu um erro, isso já definido `Reason` (no evento foi cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento foi cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor devolveu informações de erro adicionais com o erro comunicado, agora estará disponível em novos membros.

**Melhorias**

- Adicionada a verificação adicional na configuração do reconhecedor e mensagem de erro adicionais foram adicionados.
- Processamento melhorado de longa data silêncio no meio de um arquivo de áudio.
- Pacote NuGet: para projetos do .NET Framework, ele impede a criação com a configuração de AnyCPU.

**Correções de erros**

- Corrigido várias exceções encontradas no reconhecedores. Além disso, as exceções são capturadas e convertidas em evento cancelado.
- Corrigi um vazamento de memória na gestão de propriedade.
- Foi corrigido o erro em que um ficheiro de entrada de áudio poderá provocar falhas o reconhecedor.
- Foi corrigido um erro em que os eventos poderiam ser recebidos após um evento de paragem de sessão.
- Corrigido algumas condições de corrida em threading.
- Corrigido problema de compatibilidade que pode resultar numa falha de um iOS.
- Melhorias de estabilidade para o suporte de microfone Android.
- Foi corrigido um erro em que um reconhecedor em JavaScript ignorará o idioma de reconhecimento.
- Foi corrigido um erro que definir o EndpointId (em alguns casos) em JavaScript.
- Ordem de parâmetro alterados na AddIntent em JavaScript e assinatura de AddIntent JavaScript em falta adicionada.

**Amostras**

- Adicionado o C++ e C# samplea para a utilização de fluxo de push e pull no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>SDK de fala 1.0.1

Melhorias de confiabilidade e correções de erros:

- Correção do erro fatal potencial devido a condição de corrida em reconhecedor descarte
- Erro fatal potencial fixo em caso de propriedades não definidas.
- Foram adicionados erros adicionais e verificação de parâmetros.
- Foi corrigido Objective-c: possível erro fatal causado por nome substituindo no NSString.
- Objective-c: ajustado visibilidade da API
- JavaScript: Corrigido em relação a eventos e respetivos payloads.
- Melhorias de documentação.

No nosso [repositório de exemplo](https://aka.ms/csspeech/samples), foi adicionada uma nova amostra para JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de voz dos serviços cognitivos 1.0.0: versão de Setembro de 2018

**Novos recursos**

- Suporte para Objective-C no iOS. Confira nosso [início rápido de Objective-C para iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Suporte para JavaScript no browser. Confira nosso [guia de introdução do JavaScript](quickstart-js-browser.md).

**Alterações recentes**

- Com esta versão, várias alterações significativas são introduzidas.
  Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de voz dos serviços cognitivos 0.6.0: versão de Agosto de 2018

**Novos recursos**

- Aplicações do UWP criadas com o SDK de voz, agora podem passar o Kit de certificação de aplicações de Windows (WACK).
  Veja a [guia de introdução do UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
- Experimental: Suportar Java 8 (64-bit) do Windows e Linux (Ubuntu 16.04 x64).
  Veja a [início rápido de Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Alteração funcional**

- Expor informações de detalhes de erro adicionais sobre os erros de ligação.

**Alterações recentes**

- No Java (Android), o `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` função já não necessita de um parâmetro de caminho. Agora o caminho é detetado automaticamente em todas as plataformas suportadas.
- O acessador get da propriedade `EndpointUrl` em Java e c# foi removido.

**Correções de erros**

- No Java, o resultado de síntese de áudio no reconhecedor de tradução é agora implementado.
- Foi corrigido um erro que pode causar threads Inativas e um número maior de soquetes abertos e não utilizados.
- Foi corrigido um problema, onde um reconhecimento de longa execução foi possível terminar no meio da transmissão.
- Fixo, uma condição de corrida em encerramento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de voz dos serviços cognitivos versão 0.5.0: versão de Julho de 2018

**Novos recursos**

- Plataforma Android de suporte (API 23: Android 6.0 Marshmallow ou superior). Veja a [guia de introdução Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Suporta o .NET Standard 2.0 no Windows. Veja a [guia de introdução do .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: Suporte UWP no Windows (versão 1709 ou posterior).
  - Veja a [guia de introdução do UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Nota: As aplicações UWP compiladas com o SDK de voz não ainda passam o Kit de certificação de aplicações de Windows (WACK).
- Suporte a reconhecimento de execução longa com a nova ligação automática.

**Alterações funcionais**

- `StartContinuousRecognitionAsync()` oferece suporte a reconhecimento de execução longa.
- O resultado do reconhecimento contém mais campos. Eles estão deslocamento do início de áudio e a duração (ambos em tiques) do texto reconhecido e valores adicionais que representam o estado de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- AuthorizationToken de suporte para a criação de instâncias de fábrica.

**Alterações recentes**

- Eventos de reconhecimento: tipo de evento NoMatch foi intercalado o evento de erro.
- SpeechOutputFormat na linguagem c# foi mudado para OutputFormat para permanecer alinhado com o C++.
- O tipo de retorno de alguns métodos do `AudioInputStream` interface ligeiramente alterado:
  - No Java, o `read` método agora devolve `long` em vez de `int`.
  - No c#, o `Read` método agora devolve `uint` em vez de `int`.
  - No C++, o `Read` e `GetFormat` métodos agora retorno `size_t` em vez de `int`.
- C++: As instâncias de entrada dos fluxos de áudio agora podem ser transmitidas apenas como um `shared_ptr`.

**Correções de erros**

- Fixo de valores de retorno incorreto no resultado quando `RecognizeAsync()` exceder o tempo limite.
- A dependência de bibliotecas de base de dados no Windows foi removida. O SDK utiliza agora as APIs de áudio de núcleo.
- Correção da documentação: adicionado uma [regiões](regions.md) página para descrever as regiões suportadas.

**Problema conhecido**

- O SDK de voz para Android não reporta os resultados de síntese de fala para a tradução. Este problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de voz dos serviços cognitivos 0.4.0: versão de Junho de 2018

**Alterações funcionais**

- AudioInputStream

  Um reconhecedor agora, pode utilizar um fluxo como a origem de áudio. Para obter mais informações, consulte o relacionados [guia de procedimentos](how-to-use-audio-input-streams.md).

- Formato de saída detalhada

  Quando cria um `SpeechRecognizer`, pode pedir `Detailed` ou `Simple` formato de saída. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, formato léxico não processado, formato normalizado e formato normalizado com linguagem inapropriada mascarada.

**Alteração significativa**

- Alterado para `SpeechRecognitionResult.Text` partir `SpeechRecognitionResult.RecognizedText` em c#.

**Correções de erros**

- Foi corrigido um problema de possíveis de retorno de chamada na camada de USP durante o encerramento.

- Se um reconhecedor consumido um ficheiro de entrada de áudio, ele foi manter o identificador de ficheiro maior do que o necessário.

- Remover vários deadlocks entre o bombardeamento de mensagens e o reconhecedor.

- Acionar um `NoMatch` quando a resposta do serviço é excedida.

- As bibliotecas de base de dados no Windows são carregado de atraso. Esta biblioteca é necessária para apenas a entrada do microfone.

- A velocidade de carregamento de dados de áudio é limitada a sobre duas vezes a velocidade de áudio original.

- No Windows, assemblies do .NET c# agora são strong nomeados.

- Correção da documentação: `Region` informações são necessárias para criar um reconhecedor.

Mais exemplos foram adicionados e estão constantemente a ser atualizados. Para o conjunto mais recente de exemplos, consulte a [repositório de GitHub de exemplos do SDK de voz](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de voz dos serviços cognitivos 0.2.12733: versão de Maio de 2018

Esta versão é a primeira versão de pré-visualização pública do SDK de voz dos serviços cognitivos.
