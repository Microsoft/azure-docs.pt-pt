---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c57187ae15e5f15a601edbc74921b3c879abe715
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800682"
---
## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é uma assinatura do Discurso Azure. Consulte o [guia](../get-started.md#new-resource) sobre a criação de uma nova subscrição se ainda não tiver uma.

## <a name="download-and-install"></a>Transferir e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estes passos para instalar o CLI da fala no Windows:

1. Instale [.NET Quadro 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) ou [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI e, em seguida, extrai-lo.
3. Vá ao diretório raiz `spx-zips` que extraiu do download e extrai o subdiretório de que necessita ( `spx-net471` para .NET Framework 4.7, ou `spx-netcore-win-x64` para .NET Core 3.0 num CPU x64).

No pedido de comando, mude de diretório para este local e, em seguida, digite `spx` para ver ajuda para o ClI da fala.

#### <a name="linux-install"></a>[Instalação de Linux](#tab/linuxinstall)

Siga estes passos para instalar o ClI da Fala no Linux num CPU x64:

1. Instalar [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI e, em seguida, extrai-lo.
3. Vá ao diretório `spx-zips` raiz que extraiu do download, e extrai para `spx-netcore-30-linux-x64` um novo `~/spx` diretório.
4. Num terminal, digite estes comandos:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Digite `spx` para ver ajuda para o CLI do discurso.

***

## <a name="create-subscription-config"></a>Criar config de assinatura

Para começar a utilizar o SPEECH CLI, primeiro precisa de introduzir a sua chave de subscrição do Discurso e informações da região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região. Uma vez que tenha a sua chave de subscrição e identificador de região (ex. `eastus`, `westus` executar os seguintes comandos.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

A autenticação por subscrição está agora armazenada para futuros pedidos spx. Se precisar remover qualquer um destes valores armazenados, corra `spx config @region --clear` ou `spx config @key --clear` .
