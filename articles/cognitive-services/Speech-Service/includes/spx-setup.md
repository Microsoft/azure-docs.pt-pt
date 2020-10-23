---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 203ba2988d817e3e396fd8aec3db00eba9832041
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92323009"
---
## <a name="download-and-install"></a>Transferir e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

> [!NOTE]
> No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. A instalação deste deste facto pela primeira vez poderá exigir que reinicie o Windows.

Siga estes passos para instalar o CLI do discurso no Windows:

1. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI e, em seguida, extraia-o.
2. Vá ao diretório de raiz `spx-zips` que extraiu do download e extraa o subdiretor de que necessita ( `spx-net471` para .NET Framework 4.7, ou `spx-netcore-win-x64` para .NET Core 3.0 num CPU x64).

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

#### <a name="docker-install"></a>[Instalação Docker](#tab/dockerinstall)

> [!NOTE]
> <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop para <span class="docon docon-navigate-external x-hidden-focus"></span> a sua plataforma</a> deve ser instalado.

Siga estes passos para instalar o CLI da fala dentro de um recipiente Docker:

1. Num novo comando ou terminal, digite este comando:  `docker pull msftspeech/spx`
2. Digite este comando. Deve consultar informações de ajuda para o Discurso CLI: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Monte um diretório no recipiente

A ferramenta CLI do discurso guarda as definições de configuração como ficheiros e carrega estes ficheiros quando executa qualquer comando (exceto comandos de ajuda).
Ao utilizar o CLI do Discurso dentro de um recipiente Docker, deve montar um diretório local a partir do contentor, para que a ferramenta possa armazenar ou encontrar as definições de configuração, e também para que a ferramenta possa ler ou escrever quaisquer ficheiros necessários pelo comando, como ficheiros áudio de fala.

No Windows, escreva este comando para criar um diretório local CLI pode ser utilizado a partir do recipiente:

`mkdir c:\spx-data`

Ou em Linux ou Mac, digite este comando num terminal para criar um diretório e ver o seu caminho absoluto:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Usará o caminho absoluto quando chamar O CLI do Discurso.

### <a name="run-speech-cli-in-the-container"></a>Executar Discurso CLI no recipiente

Esta documentação mostra o comando CLI do Discurso `spx` utilizado em instalações não-Docker.
Ao ligar para o `spx` comando num recipiente Docker, deve montar um diretório no contentor para o seu sistema de ficheiros onde o CLI do discurso pode armazenar e encontrar valores de configuração e ler e escrever ficheiros.
No Windows, os seus comandos começarão assim:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

No Linux ou Mac, os seus comandos começarão semelhantes a isto:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> `/ABSOLUTE_PATH`Substitua-o pelo caminho absoluto indicado pelo `pwd` comando na secção acima.

Para utilizar o `spx` comando instalado num recipiente, insira sempre o comando completo acima indicado, seguido dos parâmetros do seu pedido.
Por exemplo, no Windows, este comando define a sua chave:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Não é possível utilizar o microfone ou o altifalante do computador quando executar o Speech CLI dentro de um recipiente Docker.
> Para utilizar estes dispositivos, passe ficheiros áudio de e para o Speech CLI para gravação/reprodução fora do recipiente Docker.
> A ferramenta Speech CLI pode aceder ao diretório local que configurar nos degraus acima.

***

## <a name="create-subscription-config"></a>Criar config de subscrição

Para começar a utilizar o CLI do Discurso, primeiro tem de introduzir a sua chave de subscrição do Discurso e informações da região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador da região. Assim que tiver a chave de subscrição e o identificador da região (ex. `eastus`, `westus` executar os seguintes comandos.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

A autenticação por subscrição está agora armazenada para futuros pedidos SPX. Se precisar de remover qualquer um destes valores armazenados, corra `spx config @region --clear` ou `spx config @key --clear` .
