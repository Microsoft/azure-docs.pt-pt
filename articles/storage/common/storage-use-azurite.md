---
title: Use emulador Azurite para o desenvolvimento local do armazenamento Azure
description: O emulador de código aberto Azurite proporciona um ambiente local gratuito para testar as suas aplicações de armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: b5051a5e29f9e34c43963a9a264ee47e2ad7b04a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490786"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Utilize o emulador Azurite para o desenvolvimento local do armazenamento Azure

O emulador de código aberto Azurite proporciona um ambiente local gratuito para testar as suas aplicações de blob E armazenamento de filas Azure. Quando estiver satisfeito com o funcionamento da sua aplicação localmente, mude para a utilização de uma conta de Armazenamento Azure na nuvem. O emulador fornece suporte de plataforma cruzada no Windows, Linux e macOS.

Azurite é a futura plataforma de emulador de armazenamento. Azurite substitui o [Emulador de Armazenamento Azure](storage-use-emulator.md). O Azurite continuará a ser atualizado para suportar as versões mais recentes das APIs de Armazenamento Azure.

Existem várias formas diferentes de instalar e executar a Azurite no seu sistema local:

  1. [Instale e execute a extensão do Código do Estúdio Visual Azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instale e corra a Azurite utilizando o NPM](#install-and-run-azurite-by-using-npm)
  1. [Instale e execute a imagem do Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Clone, construa e corra Azurite do repositório GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instale e execute a extensão do Código do Estúdio Visual Azurite

No Código do Estúdio Visual, selecione o painel **EXTENSÕES** e procure *Azurite* nas **EXTENSÕES:MERCADO**.

![Mercado de extensões de Código de Estúdio Visual](media/storage-use-azurite/azurite-vs-code-extension.png)

Também pode navegar para o [mercado de extensão visual Studio Code](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) no seu navegador. Selecione o botão **Instalar** para abrir o Código do Estúdio Visual e vá diretamente para a página de extensão Azurite.

A extensão suporta os seguintes comandos visual Studio Code. Para abrir a paleta de comando, prima F1 no Código do Estúdio Visual. 

   - **Azurite: Clean** - Repor todos os dados de persistência dos serviços Azurite
   - **Azurite: Clean Blob Service** - Serviço de blob limpo
   - **Azurite: Serviço de fila limpa** - Serviço de fila limpo
   - **Azurite: Fechar** - Fechar todos os serviços da Azurite
   - **Azurite: Close Blob Service** - Serviço de blob de proximidade
   - **Azurite: Serviço de fila de fecho** - Serviço de fila de fecho
   - **Azurite: Início** - Iniciar todos os serviços da Azurite
   - **Azurite: Iniciar serviço blob** - Iniciar serviço blob
   - **Azurite: Iniciar serviço de fila** - Iniciar serviço de fila

Para configurar a Azurite dentro do Código do Estúdio Visual, selecione o painel de extensões. Selecione o ícone **'Gerir'** (engrenagem) para **Azurite**. Selecione **definições de extensão**.

![Azurites configuram definições de extensão](media/storage-use-azurite/azurite-configure-extension-settings.png)

São suportadas as seguintes definições:

   - **Azurite: Blob Host** - O ponto final de escuta do serviço Blob. A definição predefinida é de 127.0.0.1.
   - **Azurite: Blob Port** - A porta de escuta do serviço Blob. A porta padrão é 10.000.
   - **Azurite: Cert** - Caminho para um caminho de arquivo de certificadoS PEM ou PFX de confiança local para ativar o modo HTTPS.
   - **Azurite: Debug** - Desave o log de depurrão para o canal Azurite. O valor predefinido é **falso**.
   - **Azurite: Chave** - Caminho para um ficheiro de chave PEM de confiança local, exigido quando **Azurite: Cert** aponta para um ficheiro PEM.
   - **Azurite: Localização** - O caminho de localização do espaço de trabalho. O padrão é a pasta de trabalho do Código do Estúdio Visual.
   - **Azurite: Solto** - Ativa o modo solto, que ignora os cabeçalhos e parâmetros não suportados.
   - **Azurite: Oauth** - Nível opcional de OAuth.
   - **Azurite: Pwd** - Palavra-passe para ficheiro PFX. Necessário quando **Azurite: Cert** aponta para um ficheiro PFX.
   - **Azurite: Anfitrião de fila** - O ponto final de audição do serviço de fila. A definição predefinida é de 127.0.0.1.
   - **Azurite: Porta de fila** - A porta de escuta do serviço de fila. A porta padrão é 10001.
   - **Azurite: Silent** - Modo silencioso desativa o registo de acesso. O valor predefinido é **falso**.
   - **Azurite: Skip Api Version Check** - Ignore a verificação da versão API do pedido. O valor predefinido é **falso**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instale e corra a Azurite utilizando o NPM

Este método de instalação requer que tenha [Node.js versão 8.0 ou posteriormente](https://nodejs.org) instalada. Node Package Manager (npm) é a ferramenta de gestão de pacotes incluída com todas as Node.js instalação. Depois de instalar Node.js, execute o `npm` seguinte comando para instalar o Azurite.

```console
npm install -g azurite
```

Depois de instalar o Azurite, consulte [Run Azurite a partir de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instale e execute a imagem do Azurite Docker

Utilize [o DockerHub](https://hub.docker.com/) para retirar a [mais recente imagem Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) utilizando o seguinte comando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Executar a imagem de Azurite Docker:**

O seguinte comando corre a imagem de Azurite Docker. O `-p 10000:10000` parâmetro redireciona os pedidos da porta 10000 da máquina hospedeira para a instância docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Especificar a localização do espaço de trabalho:**

No exemplo seguinte, o `-v c:/azurite:/data` parâmetro especifica *c:/azurite* como a localização de dados persistido a Azurite. O diretório, *c:/azurite,* deve ser criado antes de executar o comando Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Executar apenas o serviço blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Para obter mais informações sobre a configuração do Azurite no arranque, consulte [as opções da linha de comando](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Clone, construa e corra Azurite do repositório GitHub

Este método de instalação requer que tenha [o Git](https://git-scm.com/) instalado. Clone o [repositório GitHub](https://github.com/azure/azurite) para o projeto Azurite utilizando o seguinte comando de consola.

```console
git clone https://github.com/Azure/Azurite.git
```

Após a clonagem do código fonte, execute os comandos seguintes da raiz do repo clonado para construir e instalar a Azurite.

```console
npm install
npm run build
npm install -g
```

Depois de instalar e construir a Azurite, consulte [Run Azurite a partir de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Executar Azurite a partir de uma linha de comando

> [!NOTE]
> O Azurite não pode ser executado a partir da linha de comando se apenas instalar a extensão do Código do Estúdio Visual. Em vez disso, utilize a paleta de comando Visual Studio Code. Para obter mais informações, consulte [instalar e executar a extensão Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Para começar imediatamente com a linha de comando, crie um diretório chamado *c:\azurite,* em seguida, lance Azurite emitindo o seguinte comando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Este comando diz à Azurite para armazenar todos os dados num determinado diretório, *c:\azurite*. Se a `--location` opção for omitida, utilizará o diretório de trabalho atual.

## <a name="command-line-options"></a>Opções da linha de comandos

Esta secção detalha os interruptores da linha de comando disponíveis no lançamento do Azurite.

### <a name="help"></a>Ajuda

**Opcional** - Obtenha ajuda de linha de comando utilizando o `-h` ou `--help` comutador.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Anfitrião de escuta blob

**Opcional** - Por padrão, a Azurite ouvirá 127.0.0.1 como servidor local. Utilize o `--blobHost` interruptor para definir o endereço para os seus requisitos.

Aceite apenas pedidos na máquina local:

```console
azurite --blobHost 127.0.0.1
```

Permitir pedidos remotos:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Permitir pedidos remotos pode tornar o seu sistema vulnerável a ataques externos.

### <a name="blob-listening-port-configuration"></a>Configuração da porta de escuta blob

**Opcional** - Por padrão, a Azurite ouvirá o serviço Blob na porta 10000. Utilize o `--blobPort` interruptor para especificar a porta de audição que necessita.

> [!NOTE]
> Depois de utilizar uma porta personalizada, é necessário atualizar a cadeia de ligação ou a configuração correspondente nas suas ferramentas de Armazenamento Azure ou SDKs.

Personalize a porta de escuta do serviço Blob:

```console
azurite --blobPort 8888
```

Deixe o sistema selecionar automaticamente uma porta disponível:

```console
azurite --blobPort 0
```

A porta de utilização é apresentada durante o arranque Azurite.

### <a name="queue-listening-host"></a>Anfitrião de escuta de fila

**Opcional** - Por padrão, a Azurite ouvirá 127.0.0.1 como servidor local. Utilize o `--queueHost` interruptor para definir o endereço para os seus requisitos.

Aceite apenas pedidos na máquina local:

```console
azurite --queueHost 127.0.0.1
```

Permitir pedidos remotos:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Permitir pedidos remotos pode tornar o seu sistema vulnerável a ataques externos.

### <a name="queue-listening-port-configuration"></a>Configuração da porta de escuta de fila

**Opcional** - Por padrão, a Azurite ouvirá o serviço de Fila na porta 10001. Utilize o `--queuePort` interruptor para especificar a porta de audição que necessita.

> [!NOTE]
> Depois de utilizar uma porta personalizada, é necessário atualizar a cadeia de ligação ou a configuração correspondente nas suas ferramentas de Armazenamento Azure ou SDKs.

Personalize a porta de escuta do serviço Queue:

```console
azurite --queuePort 8888
```

Deixe o sistema selecionar automaticamente uma porta disponível:

```console
azurite --queuePort 0
```

A porta de utilização é apresentada durante o arranque Azurite.

### <a name="workspace-path"></a>Caminho do espaço de trabalho

**Opcional** - A Azurite armazena dados para o disco local durante a execução. Utilize o `-l` ou `--location` comutador para especificar um caminho como local de espaço de trabalho. Por predefinição, o diretório de trabalho do processo atual será utilizado. Note o "l" minúsculo.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Registo de acesso

**Opcional** - Por predefinição, o registo de acesso é apresentado na janela da consola. Desative o ecrã do registo de acesso utilizando o `-s` ou `--silent` comutador.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Log de depurg

**Opcional** - O registo de depurg inclui informações detalhadas sobre cada pedido e traço de stack de exceção. Ativar o registo de depuração fornecendo um caminho de ficheiro local válido para o `-d` ou `--debug` comutador.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Modo solto

**Opcional** - Por predefinição, a Azurite aplica um modo rigoroso para bloquear cabeçalhos e parâmetros de pedido não suportados. Desative o modo rígido utilizando o `-L` ou `--loose` o interruptor. Note a capital 'L'.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Versão

**Opcional** - Mostrar o número de versão Azurite instalado utilizando o `-v` ou `--version` comutador.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Configuração do certificado (HTTPS)

**Opcional** - Por padrão, a Azurite utiliza o protocolo HTTP. Ativar o modo HTTPS fornecendo um caminho para um ficheiro de certificado de Privacy Enhanced Mail (.pem) ou [Personal Information Exchange (.pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) para o `--cert` switch.

Quando `--cert` estiver previsto um ficheiro PEM, deve fornecer um interruptor `--key` correspondente.

```console
azurite --cert path/server.pem --key path/key.pem
```

Quando `--cert` estiver previsto um ficheiro PFX, deve fornecer um interruptor `--pwd` correspondente.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Para obter informações detalhadas sobre a criação de ficheiros PEM e PFX, consulte [a configuração HTTPS](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>Configuração de OAuth

**Opcional** - Ativar a autenticação OAuth para a Azurite utilizando o `--oauth` interruptor.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth requer um ponto final HTTPS. Certifique-se de que o HTTPS está ativado fornecendo `--cert` o interruptor juntamente com o `--oauth` interruptor.

O Azurite suporta a autenticação básica especificando o `basic` parâmetro para o `--oauth` interruptor. A Azurite fará a autenticação básica, como validar o token do portador de entrada, verificar o emitente, o público e a caducidade. Azurite não vai verificar a assinatura ou permissões simbólicas.

### <a name="skip-api-version-check"></a>Versão de versão API

**Opcional** - Ao iniciar, a Azurite verifica se a versão API solicitada é válida. O seguinte comando ignora a verificação da versão API:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Autorização de ferramentas e SDKs

Ligue-se ao Azurite a partir de SDKs de armazenamento Azure, como [o Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)utilizando qualquer estratégia de autenticação. É necessária autenticação. A Azurite suporta a autorização com AAuth, Shared Key e assinaturas de acesso compartilhado (SAS). Azurite também apoia o acesso anónimo a contentores públicos.

Se estiver a utilizar os Azure SDKs, inicie a Azurite com as `--oauth basic and --cert --key/--pwd` opções.

### <a name="well-known-storage-account-and-key"></a>Conta de armazenamento bem conhecida e chave

A Azurite aceita a mesma conta e chave bem conhecida usada pelo legado Azure Storage Emulator.

- Nome da conta: `devstoreaccount1`
- Chave da conta: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Contas e chaves de armazenamento personalizado

A Azurite suporta nomes e chaves de conta de armazenamento personalizados, definindo a `AZURITE_ACCOUNTS` variável ambiente no seguinte formato: `account1:key1[:key2];account2:key1[:key2];...` .

Por exemplo, utilize uma conta de armazenamento personalizada que tenha uma chave:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Ou use várias contas de armazenamento com duas chaves cada:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

A azurite atualiza nomes de conta personalizados e chaves do ambiente variáveis a cada minuto por padrão. Com esta funcionalidade, pode rodar dinamicamente a chave da conta ou adicionar novas contas de armazenamento sem reiniciar o Azurite.

> [!NOTE]
> A `devstoreaccount1` conta de armazenamento predefinido é desativada quando define contas de armazenamento personalizadas.

### <a name="connection-strings"></a>Cadeias de ligação

A forma mais fácil de ligar ao Azurite a partir da sua aplicação é configurar uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho *UseDevelopmentStorage=true*. Aqui está um exemplo de uma cadeia de ligação num ficheiro *app.config:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>Cadeias de conexão HTTP

Pode passar as seguintes cordas de ligação para os [Azure SDKs ou ferramentas,](https://aka.ms/azsdk) como O Azure CLI 2.0 ou Storage Explorer.

A cadeia de ligação completa é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Para ligar apenas ao serviço blob, a cadeia de ligação é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Para ligar apenas ao serviço de fila, a cadeia de ligação é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>Cadeias de ligação HTTPS

A cadeia de ligação HTTPS completa é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Para utilizar apenas o serviço blob, a cadeia de ligação HTTPS é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Para utilizar apenas o serviço de fila, a cadeia de ligação HTTPS é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Se usou `dotnet dev-certs` para gerar o seu certificado auto-assinado, utilize o seguinte fio de ligação.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Atualize a cadeia de ligação quando utilizar [contas e teclas de armazenamento personalizados](#custom-storage-accounts-and-keys).

Para obter mais informações, consulte as cordas de [conexão Configure Azure Storage](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>SDKs do Azure

Para utilizar a azurite com os [Azure SDKs,](https://aka.ms/azsdk)utilize opções OAuth e HTTPS:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Em seguida, pode instantaneamente um BlobContainerClient, BlobServiceClient ou BlobClient.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure

Também pode instantaneaizar um QueueClient ou QueueServiceClient.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure

Pode utilizar o Storage Explorer para visualizar os dados armazenados no Azurite.

#### <a name="connect-to-azurite-using-http"></a>Ligue-se ao Azurite utilizando HTTP

No Explorador de Armazenamento, ligue-se a Azurite seguindo estes passos:

 1. Selecione o ícone **'Gerir Contas'**
 1. **Selecione Adicionar uma conta**
 1. Selecione **Anexar a um emulador local**
 1. Selecione **Seguinte**
 1. Edite o campo **de nomes de Exibição** para um nome à sua escolha
 1. Selecione **Next** novamente
 1. Selecione **Connect**

#### <a name="connect-to-azurite-using-https"></a>Ligue-se ao Azurite utilizando HTTPS

Por predefinição, o Storage Explorer não abrirá um ponto final HTTPS que utiliza um certificado auto-assinado. Se estiver a executar a Azurite com HTTPS, provavelmente está a usar um certificado auto-assinado. No Explorador de Armazenamento, importar certificados SSL através do **Edit**  ->  diálogo editar certificados de importação de**certificados.**  ->  **Import Certificates**

##### <a name="import-certificate-to-storage-explorer"></a>Certificado de importação para explorador de armazenamento

1. Encontre o certificado na sua máquina local.
1. No Storage Explorer, vá à **Editar**  ->  **Certificados SSL**  ->  **Certificados de Importação** e importe o seu certificado.

Se não importar um certificado, terá um erro:

`unable to verify the first certificate` ou `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Adicione Azurite através da cadeia de conexão HTTPS

Siga estes passos para adicionar Azurite HTTPS ao Explorador de Armazenamento:

1. Selecione **Toggle Explorer**
1. Selecione **local & anexado**
1. Clique com o botão direito nas **contas de armazenamento** e selecione Connect to **Azure Storage**.
1. Selecione **Utilize uma cadeia de ligação**
1. Selecione **Seguinte**.
1. Introduza um valor no campo **nome do Visor.**
1. Introduza o fio de [ligação HTTPS](#https-connection-strings) a partir da secção anterior deste documento
1. Selecione **Seguinte**
1. Selecione **Connect**

## <a name="workspace-structure"></a>Estrutura do espaço de trabalho

Os seguintes ficheiros e pastas podem ser criados no local do espaço de trabalho ao rubricar a Azurite.

- `__blobstorage__` - Diretório contendo serviço de bolhas Azurite persistia dados binários
- `__queuestorage__` - Diretório contendo serviço de fila Azurite persistido dados binários
- `__azurite_db_blob__.json` - Ficheiro de metadados de serviço de blob azurite
- `__azurite_db_blob_extent__.json` - Ficheiro de metadados de extensão de serviço de blob azurite
- `__azurite_db_queue__.json` - Ficheiro de metadados de serviço de fila Azurite
- `__azurite_db_queue_extent__.json` - Ficheiro de metadados de extensão de serviço de fila Azurite

Para limpar o Azurite, elimine os ficheiros e as pastas acima e reinicie o emulador.

## <a name="differences-between-azurite-and-azure-storage"></a>Diferenças entre Azurite e Azure Storage

Existem diferenças funcionais entre um caso local de Azurite e uma conta de Armazenamento Azure na nuvem.

### <a name="endpoint-and-connection-url"></a>URL de ponto final e ligação

Os pontos finais de serviço para a Azurite são diferentes dos pontos finais de uma conta de Armazenamento Azure. O computador local não faz a resolução de nomes de domínio, exigindo que os pontos finais do Azurite sejam endereços locais.

Quando se dirige a um recurso numa conta de Armazenamento Azure, o nome da conta faz parte do nome de anfitrião URI. O recurso a ser tratado faz parte do caminho uri:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

O seguinte URI é um endereço válido para uma bolha numa conta de Armazenamento Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Uma vez que o computador local não faz a resolução do nome de domínio, o nome da conta faz parte do caminho URI em vez do nome de anfitrião. Utilize o seguinte formato URI para um recurso em Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

O seguinte endereço pode ser utilizado para aceder a uma bolha em Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Escala e performance

A azurite não suporta um grande número de clientes conectados. Não há garantia de desempenho. O Azurite destina-se a fins de desenvolvimento e teste.

### <a name="error-handling"></a>Processamento de erros

O Azurite está alinhado com a lógica de manuseamento de erros do Azure Storage, mas existem diferenças. Por exemplo, as mensagens de erro podem ser diferentes, enquanto os códigos de estado de erro se alinham.

### <a name="ra-grs"></a>RA-GRS

A azurite suporta a replicação geo-redundante de acesso à leitura (RA-GRS). Para obter recursos de armazenamento, aceda à localização secundária através `-secondary` da anexação do nome da conta. Por exemplo, o seguinte endereço pode ser usado para aceder a uma bolha utilizando o secundário apenas de leitura em Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Suporte de mesa

O apoio às mesas em Azurite está atualmente em desenvolvimento e aberto à contribuição! Para obter os últimos progressos, consulte o projeto [da Tabela Azurite V3.](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table)

O suporte para funções duráveis requer tabelas.

## <a name="azurite-is-open-source"></a>Azurite é de código aberto

As contribuições e sugestões para a Azurite são bem-vindas. Vá à página do projeto Azurite [GitHub](https://github.com/Azure/Azurite/projects) ou [aos problemas do GitHub](https://github.com/Azure/Azurite/issues) para marcos e itens de trabalho que estamos a seguir para as próximas funcionalidades e correções de bugs. Itens de trabalho detalhados também são rastreados no GitHub.

## <a name="next-steps"></a>Passos seguintes

- [Utilize o Emulador de Armazenamento Azure para desenvolvimento e teste](storage-use-emulator.md) de documentos o legado Azure Storage Emulator, que está a ser substituído pela Azurite.
- [As cordas de conexão de armazenamento Azure configure](storage-configure-connection-string.md) explicam como montar uma cadeia de ligação de armazenamento Azure válida.
