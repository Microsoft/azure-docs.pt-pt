---
title: Informações sobre o SDK - serviços de voz de voz
titleSuffix: Azure Cognitive Services
description: A conversão de voz Software Development Kit (SDK) dá-seus aplicativos nativo acesso para as funções do serviço de voz, tornando mais fácil desenvolver software. Este artigo fornece detalhes adicionais sobre o SDK para Windows, Linux e Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 1d81426853a9a8e28fa04e34fada3ce64798ccfd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958155"
---
# <a name="about-the-speech-sdk"></a>Informações sobre o SDK de voz

A conversão de voz Software Development Kit (SDK) dá-seus aplicativos nativo acesso para as funções do serviço de voz, tornando mais fácil desenvolver software. Atualmente, o SDK fornece acesso ao **conversão de voz em texto**, **tradução por voz**, e **reconhecimento da intenção**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtenha o SDK

### <a name="windows"></a>Windows

Para Windows, oferecemos suporte os idiomas seguintes:

* C#(UWP e .NET), C++: Pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de voz. O pacote inclui bibliotecas de cliente de 32 bits e 64 bits e bibliotecas de gerenciadas (.NET). O SDK pode ser instalado no Visual Studio com o NuGet. Procure **Microsoft.CognitiveServices.Speech**.

* Java: Pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de voz, que suporta apenas o Windows x64. No projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referência `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` como uma dependência.

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, suportamos apenas o Ubuntu 16.04 e 18.04 num PC (x86 ou x64 para desenvolvimento em C++ e x64 para .NET Core, Java e Python).

Certifique-se de que tem o compilador necessário e as bibliotecas instaladas ao executar os seguintes comandos de shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

* C#: Pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de voz. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.3.1" />
  ```

* Java: Pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de voz. No projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referência `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1` como uma dependência.

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

O SDK de Java para Android é empacotado como uma [AAR (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as permissões necessárias de Android e as bibliotecas necessárias. Ele está hospedado num repositório Maven na `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.3.1`.

Para consumir o pacote do seu projeto do Android Studio, efetue as seguintes alterações:

* No ficheiro de gradle de nível de projeto, adicione o seguinte para o `repository` secção:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No ficheiro de gradle de nível de módulo, adicione o seguinte para o `dependencies` secção:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.3.1'
  ```

O SDK do Java também é parte dos [SDK de dispositivos de voz](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
