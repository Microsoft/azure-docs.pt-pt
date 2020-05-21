---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6a38590d2511afaac6bd9213f53a8c69d0264eb4
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714697"
---
## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é uma assinatura do Discurso Azure. Consulte o [guia](../get-started.md#new-resource) sobre a criação de uma nova subscrição se ainda não tiver uma.

## <a name="download-and-install"></a>Transferir e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estes passos para instalar a ferramenta SPX no Windows:

1. Instale [.NET Quadro 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) ou [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)da ferramenta SPX e, em seguida, extrai-lo.
3. Vá ao diretório raiz `spx-zips` que extraiu do download e extrai o subdiretório de que necessita ( `spx-net471` para .NET Framework 4.7, ou `spx-netcore-win-x64` para .NET Core 3.0 num CPU x64).

No pedido de comando, mude o diretório para este local e, em seguida, digite `spx` para ver ajuda para a ferramenta SPX.

#### <a name="linux-install"></a>[Instalação de Linux](#tab/linuxinstall)

Siga estes passos para instalar a ferramenta SPX no Linux num CPU x64:

1. Instalar [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)da ferramenta SPX e, em seguida, extrai-lo.
3. Vá ao diretório `spx-zips` raiz que extraiu do download, e extrai para `spx-netcore-30-linux-x64` um novo `~/spx` diretório.
4. Num terminal, digite estes comandos:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Digite `spx` para ver ajuda para a ferramenta SPX.

***

## <a name="create-subscription-config"></a>Criar config de assinatura

Para começar a utilizar o SPX, primeiro precisa de introduzir a sua chave de subscrição do Discurso e informações da região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região. Uma vez que tenha a sua chave de subscrição e identificador de região (ex. `eastus`, `westus` executar os seguintes comandos.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

A autenticação por subscrição está agora armazenada para futuros pedidos spx. Se precisar remover qualquer um destes valores armazenados, corra `spx config @region --clear` ou `spx config @key --clear` .
