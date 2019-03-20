---
title: Notas de versão – serviços de voz
titlesuffix: Azure Cognitive Services
description: Ver um registo de execução de lançamentos de funcionalidades, melhoramentos, correções de erros e problemas conhecidos para os serviços de voz do Azure.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 7f54507fdfd21c9402e04eb867710a774f9e6bb3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57856095"
---
# <a name="release-notes"></a>Notas de versão

## <a name="speech-sdk-131-2019-february-refresh"></a>1.3.1 do SDK de voz: Atualização de Fevereiro de 2019

Esta é uma versão de correção de erros e afetar apenas o SDK gerenciado/nativo. Ele não está a afetar a versão de JavaScript do SDK.

**Correção de erros**

* Foi corrigido um vazamento de memória ao utilizar a entrada do microfone. Stream com base ou o ficheiro de entrada não é afetado.

## <a name="speech-sdk-130-2019-february-release"></a>Voz SDK 1.3.0: Versão de Fevereiro de 2019

**Novos recursos**

* O SDK de voz suporta uma seleção de entrada microfone por meio da classe AudioConfig. Isto permite-lhe transmitir dados áudio para os serviços de voz de um microfone de não-padrão. Para obter mais informações, consulte a documentação que descreve [seleção de dispositivo de entrada de áudio](how-to-select-audio-input-devices.md). Isso ainda não está disponível a partir do JavaScript.
* O SDK de voz suporta agora Unity numa versão beta. Envie comentários através a secção de problema a [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples). Esta versão suporta Unity em Windows x86 e x64 (ambiente de trabalho ou aplicativos da plataforma Universal do Windows) e o Android (ARM32/64, x86). Estão disponíveis mais informações no nosso [início rápido do Unity](quickstart-csharp-unity.md).
* O ficheiro `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (lançado em versões anteriores) não é necessária mais. A funcionalidade agora está integrada ao SDK core.


**Amostras**

O seguinte conteúdo novo está disponível no nosso [repositório de exemplo](https://aka.ms/csspeech/samples):

* Obter exemplos adicionais para AudioConfig.FromMicrophoneInput.
* Exemplos adicionais do Python para o reconhecimento da intenção e tradução.
* Obter exemplos adicionais para o uso do objeto de ligação no iOS.
* Exemplos de Java adicionais para a tradução com a saída de áudio.
* Novo exemplo para uso do [API REST do Batch transcrição](batch-transcription.md).

**Melhorias / alterações**

* Python
  * Verificação de parâmetro melhorada e mensagens de erro no SpeechConfig.
  * Adicione suporte para o objeto de ligação.
  * Suporte para Python de 32 bits (x86) no Windows.
  * O SDK de voz para o Python está fora do beta.
* iOS
  * O SDK baseia-se agora contra o 12.1 na versão do SDK do iOS.
  * O SDK suporta agora o iOS versões 9.2 e posteriores.
  * Melhorar a documentação de referência e corrigir vários nomes de propriedade.
* JavaScript
  * Adicione suporte para o objeto de ligação.
  * Adicionar ficheiros de definição de tipo para integrados em JavaScript
  * Suporte inicial e a implementação para sugestões de frase.
  * Devolver a coleção de propriedades com o JSON do serviço para reconhecimento
* DLLs de Windows agora contém um recurso de versão.
* Se criar um reconhecedor `FromEndpoint` pode adicionar parâmetros diretamente para o URL de ponto final. Usando `FromEndpoint` não é possível configurar o reconhecedor através das propriedades de configuração padrão.

**Correções de erros**

* Nome de utilizador de proxy vazio e a palavra-passe do proxy não foram processadas corretamente. Com esta versão, se definir o nome de utilizador de proxy e a palavra-passe do proxy como uma cadeia vazia, eles serão não submetidos ao estabelecer ligação com o proxy.
* SessionId do criado pelo SDK nem sempre foram efetivamente aleatório para alguns idiomas&nbsp;/ ambientes. Adicionada a inicialização de gerador aleatórios para corrigir este problema.
* Aperfeiçoar o tratamento de token de autorização. Se pretender utilizar um token de autorização, especifique o SpeechConfig e deixe em branco a chave de subscrição. Em seguida, crie o reconhecedor como de costume.
* Em alguns casos, a ligação de objeto não foi lançado corretamente. Isto foi corrigido.
* O exemplo de JavaScript foi corrigido para oferecer suporte a saída de áudio para a sintetização de tradução também no Safari.

## <a name="speech-sdk-121"></a>Voz SDK 1.2.1

Esta é uma versão só de JavaScript. Não existem recursos foram adicionados. Foram efetuadas as seguintes correções:

* Disparar o fim do fluxo em turn.end, e não no speech.end.
* Correção do erro bomba de áudio que fez não agenda junto enviar se a falha no envio atual.
* Corrija o reconhecimento contínuo com o token de autenticação.
* Correção de erros para diferente reconhecedor / pontos finais.
* Melhorias de documentação.

## <a name="speech-sdk-120-2018-december-release"></a>Voz SDK 1.2.0: Versão de Dezembro de 2018

**Novos recursos**

* Python
  * A versão Beta do suporte de Python (3.5 e superior) está disponível com esta versão. Para obter mais informações, consulte here](quickstart-python.md).
* JavaScript
  * O SDK de voz para JavaScript foi aberto. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Agora, suportamos node. js, podem encontrar mais informações [aqui](quickstart-js-node.md).
  * A restrição de comprimento para sessões de áudio tiver sido removida, o restabelecimento de ligação acontecerá automaticamente sob a capa.
* Objeto de ligação
  * De que o reconhecedor, pode acessar um objeto de conexão. Este objeto permite-lhe explicitamente iniciar a ligação de serviço e inscrever-se para se ligar e desligar os eventos.
    (Isso ainda não está disponível a partir do JavaScript e Python.)
* Suporte para Ubuntu 18.04.
* Android
  * Suporta ProGuard ativado durante a geração do ficheiro APK.

**Melhorias**

* Melhorias na utilização de thread interno e reduzindo o número de threads, bloqueios, exclusões mútuas.
* Relatórios de erros melhorados / informações. Em vários casos mensagens de erro não tem sido propagadas totalmente horizontalmente.
* Atualizar as dependências de desenvolvimento em JavaScript para utilizar módulos atualizados.

**Correções de erros**

* Vazamentos de memória fixa devido a uma incompatibilidade de tipo na RecognizeAsync.
* Em alguns casos, foram a fuga exceções.
* Corrigir o vazamento de memória nos argumentos do evento de tradução.
* Foi corrigido um problema de bloqueio no restabelecimento de ligação de longa execução sessões.
* Foi corrigido um problema que poderia levar a falta de resultado final para traduções com falha.
* C#: Se uma operação assíncrona não era tão esperada no thread principal, era possível que o reconhecedor poderia ser descartado antes da tarefa assíncrona foi concluída.
* Java: Foi corrigido um problema resulta numa falha da VM de Java.
* Objective-c: Mapeamento de enumeração fixo; RecognizedIntent foi devolvido em vez de RecognizingIntent.
* JavaScript: Conjunto de formato de saída de predefinido para 'simples' SpeechConfig.
* JavaScript: A remover a inconsistência entre as propriedades do objeto de configuração em JavaScript e outras linguagens.

**Amostras**

* Atualizados e corrigidos vários exemplos (por exemplo saída vozes para tradução, etc.).
* Adicionados os exemplos de node. js no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Voz SDK 1.1.0

**Novos recursos**

* Suporte para Android x86/x64.
* Suporte para o proxy: O objeto de SpeechConfig, agora pode chamar uma função para definir as informações de proxy (nome de anfitrião, porta, nome de utilizador e palavra-passe). Esta funcionalidade ainda não está disponível no iOS.
* Código de erro melhoradas e mensagens. Se um reconhecimento devolveu um erro, isso já definido `Reason` (no evento foi cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento foi cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor devolveu informações de erro adicionais com o erro comunicado, agora estará disponível em novos membros.

**Melhorias**

* Adicionada a verificação adicional na configuração do reconhecedor e mensagem de erro adicionais foram adicionados.
* Processamento melhorado de longa data silêncio no meio de um arquivo de áudio.
* Pacote NuGet: para projetos do .NET Framework, ele impede a criação com a configuração de AnyCPU.

**Correções de erros**

* Corrigido várias exceções encontradas no reconhecedores. Além disso, exceções são capturadas e convertidas num evento de cancelado.
* Corrigi um vazamento de memória na gestão de propriedade.
* Foi corrigido o erro em que um ficheiro de entrada de áudio poderá provocar falhas o reconhecedor.
* Foi corrigido um erro em que os eventos poderiam ser recebidos após um evento de paragem de sessão.
* Corrigido algumas condições de corrida em threading.
* Corrigido problema de compatibilidade que pode resultar numa falha de um iOS.
* Melhorias de estabilidade para o suporte de microfone Android.
* Foi corrigido um erro em que um reconhecedor em JavaScript ignorará o idioma de reconhecimento.
* Foi corrigido um erro que definir o EndpointId (em alguns casos) em JavaScript.
* Ordem de parâmetro alterados na AddIntent em JavaScript e assinatura de AddIntent JavaScript em falta adicionada.

**Amostras**

* Adicionado o C++ e C# samplea para a utilização de fluxo de push e pull no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Voz SDK 1.0.1

Melhorias de confiabilidade e correções de erros:

* Correção do erro fatal potencial devido a condição de corrida em reconhecedor descarte
* Erro fatal potencial fixo em caso de propriedades não definidas.
* Foram adicionados erros adicionais e verificação de parâmetros.
* Objective-c: Foi corrigido possível erro fatal causado por nome substituindo no NSString.
* Objective-c: Ajustada visibilidade da API
* JavaScript: Corrigido em relação a eventos e respetivos payloads.
* Melhorias de documentação.

No nosso [repositório de exemplo](https://aka.ms/csspeech/samples), foi adicionada uma nova amostra para JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>1.0.0 do SDK de voz dos serviços cognitivos: Versão de Setembro de 2018

**Novos recursos**

* Suporte para Objective-C no iOS. Confira nosso [início rápido de Objective-C para iOS](quickstart-objectivec-ios.md).
* Suporte para JavaScript no browser. Confira nosso [guia de introdução do JavaScript](quickstart-js-browser.md).

**Alterações recentes**

* Com esta versão, é introduzido um número de alterações de última hora.
  Verifique se existem [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Voz dos serviços cognitivos SDK 0.6.0: Lançamento de Agosto de 2018

**Novos recursos**

* Aplicações do UWP criadas com o SDK de voz, agora podem passar o Kit de certificação de aplicações de Windows (WACK).
  Veja a [guia de introdução do UWP](quickstart-csharp-uwp.md).
* Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
* Experimental: Suportam Java 8 no Windows (64-bit) e o Linux (Ubuntu 16.04 x64).
  Veja a [início rápido de Java Runtime Environment](quickstart-java-jre.md).

**Alteração funcional**

* Expor informações de detalhes de erro adicionais sobre os erros de ligação.

**Alterações recentes**

* No Java (Android), o `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` função já não necessita de um parâmetro de caminho. Agora o caminho é detetado automaticamente em todas as plataformas suportadas.
* O acessador get da propriedade `EndpointUrl` em Java e c# foi removido.

**Correções de erros**

* No Java, o resultado de síntese de áudio no reconhecedor de tradução é agora implementado.
* Foi corrigido um erro que pode causar threads Inativas e um número maior de soquetes abertos e não utilizados.
* Foi corrigido um problema, onde um reconhecimento de longa execução foi possível terminar no meio da transmissão.
* Fixo, uma condição de corrida em encerramento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Voz dos serviços cognitivos SDK versão 0.5.0: Versão de Julho de 2018

**Novos recursos**

* Plataforma Android de suporte (API 23: Android 6.0 Marshmallow ou superior). Veja a [guia de introdução Android](quickstart-java-android.md).
* Suporta o .NET Standard 2.0 no Windows. Veja a [guia de introdução do .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimental: Suporta UWP no Windows (versão 1709 ou posterior).
  * Veja a [guia de introdução do UWP](quickstart-csharp-uwp.md).
  * Nota: Aplicações do UWP criadas com o SDK de voz ainda não passam o Kit de certificação de aplicações de Windows (WACK).
* Suporte a reconhecimento de execução longa com a nova ligação automática.

**Alterações funcionais**

* `StartContinuousRecognitionAsync()` oferece suporte a reconhecimento de execução longa.
* O resultado do reconhecimento contém mais campos. Eles estão deslocamento do início de áudio e a duração (ambos em tiques) do texto reconhecido e valores adicionais que representam o estado de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
* AuthorizationToken de suporte para a criação de instâncias de fábrica.

**Alterações recentes**

* Eventos de reconhecimento: Tipo de evento NoMatch foi mesclado no evento de erro.
* SpeechOutputFormat na linguagem c# foi mudado para OutputFormat para permanecer alinhado com o C++.
* O tipo de retorno de alguns métodos do `AudioInputStream` interface ligeiramente alterado:
   * No Java, o `read` método agora devolve `long` em vez de `int`.
   * No c#, o `Read` método agora devolve `uint` em vez de `int`.
   * No C++, o `Read` e `GetFormat` métodos agora retorno `size_t` em vez de `int`.
* C++: Instâncias de entrada dos fluxos de áudio agora podem ser transmitidas apenas como um `shared_ptr`.

**Correções de erros**

* Fixo de valores de retorno incorreto no resultado quando `RecognizeAsync()` exceder o tempo limite.
* A dependência de bibliotecas de base de dados no Windows foi removida. O SDK utiliza agora as APIs de áudio de núcleo.
* Correção da documentação: Adicionado um [regiões](regions.md) página para descrever as regiões suportadas.

**Problema conhecido**

* O SDK de voz para Android não reporta os resultados de síntese de fala para a tradução. Este problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Voz dos serviços cognitivos SDK 0.4.0: Versão de Junho de 2018

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Voz dos serviços cognitivos SDK 0.2.12733: Versão de Maio de 2018

Esta versão é a primeira versão de pré-visualização pública do SDK de voz dos serviços cognitivos.
