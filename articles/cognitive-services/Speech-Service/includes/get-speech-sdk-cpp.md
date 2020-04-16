---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399966"
---
:::row:::
    :::column span="3":::
        O C++ Speech SDK está disponível no Windows, Linux e macOS. Para mais informações, consulte <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pacote C++ NuGet

O C++ Speech SDK pode ser instalado a `Install-Package` partir do Gestor de **Pacotes** com o seguinte comando.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Binários C++ e ficheiros de cabeçalho

Em alternativa, o C++ Speech SDK pode ser instalado a partir de binários. Baixe o SDK como um <a href="https://aka.ms/csspeech/linuxbinary" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> pacote .tar</a> e desembala os ficheiros num diretório à sua escolha. Os conteúdos deste pacote (que incluem ficheiros de cabeçalho para arquiteturas-alvo x86 e x64) são estruturados da seguinte forma:

  | Caminho                   | Descrição                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licença                                              |
  | `ThirdPartyNotices.md` | Notificações de terceiros                                  |
  | `include`              | Ficheiros de cabeçalho para C++                                 |
  | `lib/x64`              | Biblioteca nativa x64 para ligar com a sua aplicação |
  | `lib/x86`              | Biblioteca nativa x86 para ligação com a sua aplicação |

  Para criar uma aplicação, copiar ou mover os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento. Inclua-os conforme necessário no seu processo de construção.

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux e macOS quickstart C++ código fonte<span class="docon docon-navigate-external x-hidden-focus"></span></a>