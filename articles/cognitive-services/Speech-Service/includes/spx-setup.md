---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544226"
---
## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é uma assinatura do Azure Speech. Consulte o [guia](../get-started.md#new-resource) sobre a criação de uma nova subscrição se ainda não tiver uma.

## <a name="download-and-install"></a>Transferir e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estes passos para instalar o CLI do discurso no Windows:

1. Instalar ou [.Net Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) ou [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI e, em seguida, extraia-o.
3. Vá ao diretório de raiz `spx-zips` que extraiu do download e extraa o subdiretor de que necessita ( `spx-net471` para .NET Framework 4.7, ou `spx-netcore-win-x64` para .NET Core 3.0 num CPU x64).

No pedido de comando, altere o diretório para este local e, em seguida, escreva `spx` para ver ajuda para o CLI do discurso.

> [!NOTE]
> No Windows, o CLI do Discurso só pode mostrar os tipos de letra disponíveis para o pedido de comando no computador local.
> [O Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) suporta todas as fontes produzidas interativamente pelo Speech CLI.
> Se você des output para um ficheiro, um editor de texto como o Notepad ou um navegador web como o Microsoft Edge também pode mostrar todos os tipos de letra.

> [!NOTE]
> Powershell não verifica o diretório local quando procura um comando. Em Powershell, altere o diretório para a localização `spx` e ligue para a ferramenta entrando `.\spx` .
> Se adicionar este diretório ao seu caminho, o pedido de comando Powershell e Windows encontrará `spx` a partir de qualquer diretório sem incluir o `.\` prefixo.

#### <a name="linux-install"></a>[Instalação Linux](#tab/linuxinstall)

Siga estes passos para instalar o CLI da fala no Linux num CPU x64:

1. Instalar [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI e, em seguida, extraia-o.
3. Vá ao diretório de raiz `spx-zips` que extraiu do download, e extraia `spx-netcore-30-linux-x64` para um novo `~/spx` diretório.
4. Num terminal, digite estes comandos:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Escreva `spx` para ver ajuda para o CLI do discurso.

***

## <a name="create-subscription-config"></a>Criar config de subscrição

Para começar a utilizar o CLI do Discurso, primeiro tem de introduzir a sua chave de subscrição do Discurso e informações da região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador da região. Assim que tiver a chave de subscrição e o identificador da região (ex. `eastus`, `westus` executar os seguintes comandos.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

A autenticação por subscrição está agora armazenada para futuros pedidos SPX. Se precisar de remover qualquer um destes valores armazenados, corra `spx config @region --clear` ou `spx config @key --clear` .
