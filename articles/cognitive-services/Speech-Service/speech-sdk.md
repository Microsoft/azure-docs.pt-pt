---
title: Sobre o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O kit de desenvolvimento de software de fala (SDK) dá a seus aplicativos acesso nativo às funções do serviço de fala, facilitando o desenvolvimento de software. Este artigo fornece detalhes adicionais sobre o SDK para Windows, Linux e Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: wolfma
ms.openlocfilehash: ea879cbfc3b9b1d1a627add52f26a473aca53cdf
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759880"
---
# <a name="about-the-speech-sdk"></a>Acerca do SDK de Voz

A conversão de voz Software Development Kit (SDK) proporciona o acesso de aplicações para as funções do serviço de voz, tornando-o mais fácil de desenvolver software habilitado para fala. Atualmente, os SDKs fornecem acesso a voz **-para-texto**, conversão de **texto em fala**, **tradução de fala**, **reconhecimento de intenção**e **canal de fala de linha direta da estrutura de bot**.

Você pode facilmente capturar áudio de um microfone, ler de um fluxo ou acessar arquivos de áudio do armazenamento com o SDK de fala. O SDK de Voz suporta áudio de canal único WAV/PCM 16 bits, 16 kHz/8 kHz para reconhecimento de voz. Formatos de áudio adicionais têm suporte usando o [ponto de extremidade REST de fala para texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou o [serviço de transcrição do lote](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

Uma visão geral sobre os recursos e as plataformas com suporte pode ser encontrada na [página de entrada](https://aka.ms/csspeech)de documentação.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtenha o SDK

### <a name="windows"></a>Windows

> [!WARNING]
> O SDK de Speech suporta versões posteriores do Windows 10 ou posteriores. As versões anteriores do Windows não são **suportadas**.

Para Windows, oferecemos suporte os idiomas seguintes:

* C# (UWP e .NET), C++: pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de voz. O pacote inclui bibliotecas de cliente de 32 bits e 64 bits e bibliotecas de gerenciadas (.NET). O SDK pode ser instalado no Visual Studio usando NuGet, [Microsoft. cognitivaservices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de voz, que suporta apenas o Windows x64. No projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referência `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` como uma dependência.

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, há suporte apenas para Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9 nas seguintes arquiteturas de destino:
> - x86, x64 e ARM64 para C++ desenvolvimento
> - x64 e ARM64 para Java
> - x64 para .NET Core e Python

Verifique se você tem as bibliotecas necessárias instaladas executando os seguintes comandos de Shell:

No Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: Pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de voz. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de voz. No projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referência `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` como uma dependência.

* C++: Transferir o SDK como um [pacote. tar](https://aka.ms/csspeech/linuxbinary) e Descompacte os arquivos num diretório à sua escolha. A tabela seguinte mostra a estrutura da pasta SDK:

  |Caminho|Descrição|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Notificações de terceiros|
  |`include`|Arquivos de cabeçalho para C e C++|
  |`lib/x64`|X64 nativo biblioteca para a ligação com a sua aplicação|
  |`lib/x86`|X86 nativo biblioteca para a ligação com a sua aplicação|

  Para criar uma aplicação, copiar ou mover os binários necessários (e as bibliotecas) em seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [aar (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões do Android necessárias. Ele está hospedado num repositório Maven na `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Para consumir o pacote do seu projeto do Android Studio, efetue as seguintes alterações:

* No ficheiro de gradle de nível de projeto, adicione o seguinte para o `repository` secção:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No ficheiro de gradle de nível de módulo, adicione o seguinte para o `dependencies` secção:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

O SDK do Java também é parte dos [SDK de dispositivos de voz](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
