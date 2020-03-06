---
title: Sobre o Discurso SDK - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: O Kit de Desenvolvimento de Software de Fala (SDK) dá às suas aplicações acesso nativo às funções do serviço Speech, facilitando o desenvolvimento de software. Este artigo fornece detalhes adicionais sobre o SDK para Windows, Linux e Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331098"
---
# <a name="about-the-speech-sdk"></a>Acerca do SDK de Voz

A conversão de voz Software Development Kit (SDK) proporciona o acesso de aplicações para as funções do serviço de voz, tornando-o mais fácil de desenvolver software habilitado para fala. Atualmente, os SDKs dão acesso ao **discurso-a-texto,** **texto-a-fala,** **tradução da fala,** **reconhecimento de intenções**e canal de **discurso de linha direta do Bot Framework.**

Pode capturar facilmente áudio a partir de um microfone, ler a partir de um fluxo ou aceder a ficheiros áudio a partir do armazenamento com o Speech SDK. O Speech SDK suporta WAV/PCM 16-bit, 16 kHz/8 kHz, áudio de canal único para reconhecimento de voz. São suportados formatos áudio adicionais utilizando o [ponto final do REST da fala-a-texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou o serviço de [transcrição](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)do lote .

Uma visão geral sobre as capacidades e plataformas suportadas pode ser encontrada na página de [entrada](https://aka.ms/csspeech)da documentação .

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtenha o SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> O SDK de Speech suporta versões posteriores do Windows 10 ou posteriores. As versões anteriores do Windows não são **suportadas**.

Para Windows, oferecemos suporte os idiomas seguintes:

* C# (UWP e .NET), C++: pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de voz. O pacote inclui bibliotecas de cliente de 32 bits e 64 bits e bibliotecas de gerenciadas (.NET). O SDK pode ser instalado no Estúdio Visual utilizando NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de voz, que suporta apenas o Windows x64. No seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` de referência como dependência.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Atualmente, apenas apoiamos Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8, e CentOS 8 nas seguintes arquiteturas-alvo:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) para C++ o desenvolvimento
> - x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) para Java
> - x64 para .NET Core e Python

Certifique-se de que tem as bibliotecas necessárias instaladas executando os seguintes comandos de concha:

em Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Em Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

No RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: Pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de voz. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de voz. No seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` de referência como dependência.

* C++: Descarregue o SDK como um [pacote .tar](https://aka.ms/csspeech/linuxbinary) e desembale os ficheiros num diretório à sua escolha. A tabela seguinte mostra a estrutura da pasta SDK:

  |Caminho|Descrição|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Notificações de terceiros|
  |`include`|Arquivos de cabeçalho para C e C++|
  |`lib/x64`|X64 nativo biblioteca para a ligação com a sua aplicação|
  |`lib/x86`|X86 nativo biblioteca para a ligação com a sua aplicação|

  Para criar uma aplicação, copiar ou mover os binários necessários (e as bibliotecas) em seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

# <a name="android"></a>[Android](#tab/android)

O Java SDK para Android está embalado como [Um Ar (Biblioteca Android),](https://developer.android.com/studio/projects/android-library)que inclui as bibliotecas necessárias e as permissões necessárias para android. Está hospedado num repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Para consumir o pacote do seu projeto do Android Studio, efetue as seguintes alterações:

* No ficheiro build.gradle ao nível do projeto, adicione o seguinte à secção `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No ficheiro build.gradle de nível de módulo, adicione o seguinte à secção `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

O Java SDK também faz parte do [SDK dispositivos](speech-devices-sdk.md)de fala.

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer o discurso emC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
