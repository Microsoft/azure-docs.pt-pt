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
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: f6446a2dbe3f111c9c617e78a0bdd98f6cea9153
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468773"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de fala

O kit de desenvolvimento de software de fala (SDK) dá aos seus aplicativos acesso às funções dos serviços de fala, facilitando o desenvolvimento de software habilitado para fala. Atualmente, os SDKs fornecem acesso a voz **-para-texto**, conversão de **texto em fala**, **tradução de fala**, **reconhecimento de intenção**e **canal de fala de linha direta da estrutura de bot**. Uma visão geral sobre os recursos e as plataformas com suporte pode ser encontrada na [página de entrada](https://aka.ms/csspeech)de documentação.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obter o SDK

### <a name="windows"></a>Windows

Para o Windows, damos suporte aos seguintes idiomas:

* C#(UWP e .NET), C++: você pode referenciar e usar a versão mais recente do nosso pacote NUGET do SDK de fala. O pacote inclui bibliotecas de cliente de 32 bits e de 64 bits e bibliotecas gerenciadas (.NET). O SDK pode ser instalado no Visual Studio usando o NuGet. Procure **Microsoft. cognitivaservices. Speech**.

* Java: você pode referenciar e usar a versão mais recente do nosso pacote do Speech SDK Maven, que dá suporte apenas ao Windows x64. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referencie `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` como uma dependência.

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, damos suporte apenas ao Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9 nas seguintes arquiteturas de destino:
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

* C#: Você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de fala. Para fazer referência ao SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de fala. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referencie `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` como uma dependência.

* C++: Baixe o SDK como um [pacote. tar](https://aka.ms/csspeech/linuxbinary) e descompacte os arquivos em um diretório de sua escolha. A tabela a seguir mostra a estrutura de pastas do SDK:

  |Caminho|Descrição|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Notificações de terceiros|
  |`include`|Arquivos de cabeçalho para C eC++|
  |`lib/x64`|Biblioteca x64 nativa para vinculação com seu aplicativo|
  |`lib/x86`|Biblioteca x86 nativa para vinculação com seu aplicativo|

  Para criar um aplicativo, copie ou mova os binários (e bibliotecas) necessários para o ambiente de desenvolvimento. Inclua-os conforme necessário em seu processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [aar (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões do Android necessárias. Ele é hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Para consumir o pacote do seu projeto Android Studio, faça as seguintes alterações:

* No arquivo Build. gradle de nível de projeto, adicione o seguinte à seção `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No arquivo Build. gradle de nível de módulo, adicione o seguinte à seção `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

O SDK do Java também faz parte do [SDK dos dispositivos de fala](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer a fala noC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
