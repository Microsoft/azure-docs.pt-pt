---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: da88b8554d6c3214da9a386613538c237a318f73
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546913"
---
## <a name="download-and-install"></a>Transferir e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estes passos para instalar o CLI do discurso no Windows:

1. No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. A instalação desta situação pela primeira vez pode exigir um reinício.
2. Descarregue o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI e, em seguida, extraia-o.
3. Vá ao diretório onde `spx-zips` extraiu. Esta pasta contém ficheiros de programas para o CLI do Discurso em várias plataformas. 
4. Extrair os ficheiros para a sua plataforma `spx-net471` (para .NET Framework 4.7, ou `spx-netcore-win-x64` para .NET Core 3.0 num CPU x64). Lembre-se que vai fugir `spx` deste diretório.

### <a name="run-the-speech-cli"></a>Executar o Discurso CLI

1. Abra o pedido de comando ou PowerShell e, em seguida, navegue para o diretório onde extraiu o Discurso CLI.  
2. Escreva `spx` para ver os comandos de ajuda para o CLI do discurso.

> [!NOTE]
> Powershell não verifica o diretório local quando procura um comando. Em Powershell, altere o diretório para a localização `spx` e ligue para a ferramenta entrando `.\spx` .
> Se adicionar este diretório ao seu caminho, o pedido de comando Powershell e Windows encontrará `spx` a partir de qualquer diretório sem incluir o `.\` prefixo.

### <a name="font-limitations"></a>Limitações de fontes

No Windows, o CLI do Discurso só pode mostrar os tipos de letra disponíveis para o pedido de comando no computador local.
[O Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) suporta todas as fontes produzidas interativamente pelo Speech CLI.

Se você des output para um ficheiro, um editor de texto como o Notepad ou um navegador web como o Microsoft Edge também pode mostrar todos os tipos de letra.

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

#### <a name="docker-install-windows-linux-macos"></a>[Instalação do Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Siga estes passos para instalar o CLI da fala num recipiente Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Instalar o <span class="docon docon-navigate-external x-hidden-focus"></span> Docker Desktop</a> para a sua plataforma se ainda não estiver instalada.
2. Num novo comando ou terminal, digite este comando:
   ```shell   
   docker pull msftspeech/spx
   ```
3. Digite este comando. Deve consultar informações de ajuda para o Discurso CLI:
   ```shell 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Monte um diretório no recipiente

A ferramenta CLI do discurso guarda as definições de configuração como ficheiros e carrega estes ficheiros quando executa qualquer comando (exceto comandos de ajuda).
Ao utilizar o CLI do Discurso dentro de um recipiente Docker, deve montar um diretório local a partir do contentor, para que a ferramenta possa armazenar ou encontrar as definições de configuração, e também para que a ferramenta possa ler ou escrever quaisquer ficheiros necessários pelo comando, como ficheiros áudio de fala.

No Windows, escreva este comando para criar um diretório local CLI pode ser utilizado a partir do recipiente:

`mkdir c:\spx-data`

Ou no Linux ou macOS, digite este comando num terminal para criar um diretório e ver o seu caminho absoluto:

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

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

No Linux ou no macOS, os seus comandos começarão semelhantes a isto:
```shell   
sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
```

> [!NOTE]
> `/ABSOLUTE_PATH`Substitua-o pelo caminho absoluto indicado pelo `pwd` comando na secção acima.

Para utilizar o `spx` comando instalado num recipiente, insira sempre o comando completo acima indicado, seguido dos parâmetros do seu pedido.
Por exemplo, no Windows, este comando define a sua chave:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

> [!WARNING]
> Não é possível utilizar o microfone do computador quando executar o Speech CLI dentro de um recipiente Docker. No entanto, pode ler e guardar ficheiros áudio no seu diretório montado local. 

### <a name="optional-create-a-command-line-shortcut"></a>Opcional: Criar um atalho de linha de comando

Se estiver a executar o CLI do Discurso a partir de um contentor Docker no Linux ou no macOS, pode criar um atalho. 

Siga estas instruções para criar um atalho:
1. Abra `.bash_profile` com o seu editor de texto favorito. Por exemplo:
   ```shell
   nano ~/.bash_profile
   ```
2. Em seguida, adicione esta função ao seu `.bash_profile` . Certifique-se de atualizar esta função com o caminho correto para o seu diretório montado:
   ```shell   
   spx(){
       sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Obter o seu perfil:
   ```shell
   source ~/.bash_profile
   ```
4. Agora, em vez de `sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx` correr, pode apenas `spx` escrever seguido de argumentos. Por exemplo: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> Se alterar o diretório montado a que o Docker se refere, tem de atualizar a função em `.bash_profile` .

***

## <a name="create-subscription-config"></a>Criar config de subscrição

Para começar a utilizar o CLI do Discurso, tem de introduzir a tecla de subscrição do Discurso e o identificador da região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../overview.md#try-the-speech-service-for-free).
Assim que tiver a chave de subscrição e o identificador da região (ex. `eastus`, `westus` executar os seguintes comandos.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

A autenticação por subscrição está agora armazenada para futuros pedidos SPX. Se precisar de remover qualquer um destes valores armazenados, corra `spx config @region --clear` ou `spx config @key --clear` .
