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
ms.openlocfilehash: c5e12812a4305493be2cdc234946796b21dd26d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558931"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de fala

O kit de desenvolvimento de software de fala (SDK) dá aos seus aplicativos acesso às funções dos serviços de fala, facilitando o desenvolvimento de software habilitado para fala. Atualmente, os SDKs fornecem acesso a voz **-para-texto**, conversão de **texto em fala**, **tradução de fala**, **reconhecimento de intenção**e **canal de fala de linha direta da estrutura de bot**. Uma visão geral sobre os recursos e as plataformas com suporte pode ser encontrada na [página de entrada](https://aka.ms/csspeech)de documentação.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtenha o SDK

### <a name="windows"></a>Windows

Para Windows, oferecemos suporte os idiomas seguintes:

* C#(UWP e .NET), C++: Você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de fala. O pacote inclui bibliotecas de cliente de 32 bits e 64 bits e bibliotecas de gerenciadas (.NET). O SDK pode ser instalado no Visual Studio com o NuGet. Procure **Microsoft.CognitiveServices.Speech**.

* Java: Você pode referenciar e usar a versão mais recente do nosso pacote do Speech SDK Maven, que dá suporte apenas ao Windows x64. No projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referência `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` como uma dependência.

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, damos suporte apenas ao Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9 em um PC (x86 ou x64 para C++ desenvolvimento e x64 para .NET Core, Java e Python).

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

* C#: Você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de fala. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.6.0" />
  ```

* Java: Você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de fala. No projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referência `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` como uma dependência.

* C++: Baixe o SDK como um [pacote. tar](https://aka.ms/csspeech/linuxbinary) e descompacte os arquivos em um diretório de sua escolha. A tabela seguinte mostra a estrutura da pasta SDK:

  |Caminho|Descrição|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Notificações de terceiros|
  |`include`|Arquivos de cabeçalho para C e C++|
  |`lib/x64`|X64 nativo biblioteca para a ligação com a sua aplicação|
  |`lib/x86`|X86 nativo biblioteca para a ligação com a sua aplicação|

  Para criar uma aplicação, copiar ou mover os binários necessários (e as bibliotecas) em seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [aar (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões do Android necessárias. Ele está hospedado num repositório Maven na `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`.

Para consumir o pacote do seu projeto do Android Studio, efetue as seguintes alterações:

* No ficheiro de gradle de nível de projeto, adicione o seguinte para o `repository` secção:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No ficheiro de gradle de nível de módulo, adicione o seguinte para o `dependencies` secção:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.6.0'
  ```

O SDK do Java também é parte dos [SDK de dispositivos de voz](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
