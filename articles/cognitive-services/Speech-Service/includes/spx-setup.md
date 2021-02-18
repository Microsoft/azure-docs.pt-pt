---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: b985399eb89ae7bbf73d280775157207498f0080
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100645559"
---
## <a name="download-and-install"></a>Transferir e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estes passos para instalar o CLI do discurso no Windows:

1. No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. A instalação desta situação pela primeira vez pode exigir um reinício.
1. Instale [.NET Core 3.1 SDK](/dotnet/core/install/linux).
2. Instale o CLI do discurso utilizando o NuGet introduzindo este comando:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0
   ```
Escreva `spx` para ver ajuda para o CLI do discurso.

> [!NOTE]
> Como alternativa ao NuGet, pode descarregar e extrair o [arquivo zip](https://aka.ms/speech/spx-zips.zip)do Speech CLI, encontrar e extrair a sua plataforma do `spx-zips` diretório e adicionar o caminho à variável PATH do `spx` seu sistema. 


### <a name="font-limitations"></a>Limitações de fontes

No Windows, o CLI do Discurso só pode mostrar os tipos de letra disponíveis para o pedido de comando no computador local.
[O Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) suporta todas as fontes produzidas interativamente pelo Speech CLI.

Se você des output para um ficheiro, um editor de texto como o Notepad ou um navegador web como o Microsoft Edge também pode mostrar todos os tipos de letra.

#### <a name="linux-install"></a>[Instalação Linux](#tab/linuxinstall)

As seguintes distribuições linux são suportadas para arquiteturas x64 usando o Discurso CLI:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> Arquiteturas adicionais são apoiadas pelo Discurso SDK (não o Discurso CLI). Para mais informações, consulte [Sobre o SDK de discurso.](../speech-sdk.md)

Siga estes passos para instalar o CLI da fala no Linux num CPU x64:

1. Instalar [.NET Core 3.1](/dotnet/core/install/linux).
2. Instale o CLI do discurso utilizando o NuGet introduzindo este comando:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Escreva `spx` para ver ajuda para o CLI do discurso.

> [!NOTE]
> Como alternativa ao NuGet, pode baixar as binários no [arquivo zip,](https://aka.ms/speech/spx-zips.zip)extrair `spx-netcore-30-linux-x64.zip` para um novo `~/spx` diretório, `sudo chmod +r+x spx` digitar no binário e adicionar o `~/spx` caminho à variável do seu sistema PATH.


#### <a name="docker-install-windows-linux-macos"></a>[Instalação do Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Siga estes passos para instalar o CLI da fala num recipiente Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Instalar o <span class="docon docon-navigate-external x-hidden-focus"></span> Docker Desktop</a> para a sua plataforma se ainda não estiver instalada.
2. Num novo comando ou terminal, digite este comando:
   ```console   
   docker pull msftspeech/spx
   ```
3. Digite este comando. Deve consultar informações de ajuda para o Discurso CLI:
   ```console 
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

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

No Linux ou no macOS, os seus comandos serão parecidos com a amostra abaixo. `ABSOLUTE_PATH`Substitua-o pelo caminho absoluto do seu diretório montado. Este caminho foi devolvido pelo `pwd` comando na secção anterior. 

Se executar este comando antes de definir a sua chave e região, terá um erro dizendo-lhe para definir a sua chave e região:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Para utilizar o `spx` comando instalado num recipiente, insira sempre o comando completo acima indicado, seguido dos parâmetros do seu pedido.
Por exemplo, no Windows, este comando define a sua chave:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config --set @key SUBSCRIPTION-KEY
```

Para uma interação mais alargada com a ferramenta da linha de comando, pode iniciar um recipiente com uma casca de pancada interativa adicionando um parâmetro de ponto de entrada.
No Windows, insira este comando para iniciar um contentor que expõe uma interface de linha de comando interativa onde pode introduzir `spx` vários comandos:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Não é possível utilizar o microfone do computador quando executar o Speech CLI dentro de um recipiente Docker. No entanto, pode ler e guardar ficheiros áudio no seu diretório montado local. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Criar config de subscrição

Para começar a utilizar o CLI do Discurso, tem de introduzir a tecla de subscrição do Discurso e o identificador da região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../overview.md#try-the-speech-service-for-free).
Assim que tiver a chave de subscrição e o identificador da região (ex. `eastus`, `westus` executar os seguintes comandos.

```console
spx config --set @key SUBSCRIPTION-KEY
spx config --set @region REGION
```

A autenticação por subscrição está agora armazenada para futuros pedidos SPX. Se precisar de remover qualquer um destes valores armazenados, corra `spx config @region --clear` ou `spx config @key --clear` .
