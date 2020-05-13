---
title: Utilize o emulador Azurite para o desenvolvimento local do armazenamento azure
description: O emulador de código aberto Azurite (pré-visualização) proporciona um ambiente local gratuito para testar as suas aplicações de armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/01/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: f4e0bbd546b770b9e81bb9142cdd97e3927db7bd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195949"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Utilize o emulador Azurite para desenvolvimento e teste de armazenamento azure local (pré-visualização)

O emulador de código aberto Azurite 3.2 (pré-visualização) proporciona um ambiente local gratuito para testar as suas aplicações de armazenamento de blob Azure e fila. Quando estiver satisfeito com a forma como a sua aplicação está a funcionar localmente, mude para a utilização de uma conta de Armazenamento Azure na nuvem. O emulador fornece suporte cross-platform no Windows, Linux e macOS. O Azurite v3 suporta APIs implementadas pelo serviço Azure Blob.

Azurite é a futura plataforma de emulador de armazenamento. Azurite substitui o [Emulador de Armazenamento Azure.](storage-use-emulator.md) A Azurite continuará a ser atualizada para suportar as versões mais recentes das APIs de Armazenamento Azure.

Existem várias formas diferentes de instalar e executar o Azurite no seu sistema local:

  1. [Instale e execute a extensão do Código do Estúdio Visual Azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instale e execute o Azurite utilizando o NPM](#install-and-run-azurite-by-using-npm)
  1. [Instale e execute a imagem Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Clone, construa e gere azurite do repositório GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instale e execute a extensão do Código do Estúdio Visual Azurite

Dentro do Código do Estúdio Visual, selecione o painel **DE EXTENSÕES** e procure *azurite* nas **EXTENSÕES:MARKETPLACE**.

![Mercado de extensões de código de estúdio visual](media/storage-use-azurite/azurite-vs-code-extension.png)

Também pode navegar para o mercado de [extensão do Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) no seu navegador. Selecione o botão **Instalar** para abrir o Código do Estúdio Visual e vá diretamente para a página de extensão Azurite.

Pode iniciar ou fechar rapidamente o Azurite na barra de estado do Código do Estúdio Visual. Clique em **[Serviço Azurite Blob]** ou [Serviço de **Fila Azurite]**.

A extensão suporta os seguintes comandos do Código do Estúdio Visual. Para abrir a paleta de comando, prima F1 no Código do Estúdio Visual. 

   - **Azurite: Clean** - Redefinir todos os serviços azurite persistem dados de persistência
   - **Azurite: Clean Blob Service** - Serviço de blob limpo
   - **Azurite: Serviço** de fila limpa - Serviço de fila limpa
   - **Azurite: Fechar** - Fechar todos os serviços azurite
   - **Azurite: Serviço de Blob Close** - Serviço de blob de fecho
   - **Azurite: Serviço de Fila de Encerramento** - Serviço de fila de encerramento
   - **Azurite: Iniciar** - Iniciar todos os serviços azurite
   - **Azurite: Start Blob Service** - Start blob service
   - **Azurite: Iniciar o serviço de fila** - Iniciar o serviço de fila

Para configurar o Azurite dentro do Código do Estúdio Visual, selecione o painel de extensões. Selecione o ícone **Gerir** (engrenagem) para **Azurite**. **Selecione Definições de extensão**.

![Configurar definições de extensão de Azurites](media/storage-use-azurite/azurite-configure-extension-settings.png)

As seguintes definições são suportadas:

   - **Azurite: Blob Host** - O ponto final de escuta do serviço Blob. A definição predefinida é de 127.0.0.1.
   - **Azurite: Blob Port** - A porta de escuta do serviço Blob. A porta padrão é 10000.
   - **Azurite: Cert** - Caminho para um caminho de ficheiro de certificado PEM ou PFX de confiança local para ativar o modo HTTPS.
   - **Azurite: Debug** - Saída o registo de depuração para o canal Azurite. O valor predefinido é **falso**.
   - **Azurite: Chave** - Caminho para um ficheiro chave PEM de confiança local, necessário quando **Azurite: Cert** aponta para um ficheiro PEM.
   - **Azurite: Localização** - O caminho de localização do espaço de trabalho. O padrão é a pasta de funcionamento do Código do Estúdio Visual.
   - **Azurite: Loose** - Enable loose mode, que ignora cabeçalhos e parâmetros não suportados.
   - **Azurite: Oauth** - Nível Opcional de OAuth.
   - **Azurite: Pwd** - Palavra-passe para ficheiro PFX. Necessário quando **Azurite: Cert** aponta para um ficheiro PFX.
   - **Azurite: Anfitrião** de fila - O ponto final do serviço de fila. A definição predefinida é de 127.0.0.1.
   - **Azurite: Porta de fila** - A porta de escuta do serviço de fila. A porta padrão é 10001.
   - **Azurite: Modo silencioso** desativa o registo de acesso. O valor predefinido é **falso**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instale e execute o Azurite utilizando o NPM

Este método de instalação requer que tenha a [versão Node.js 8.0 ou posteriormente](https://nodejs.org) instalada. Node Package Manager (npm) é a ferramenta de gestão de pacotes incluída em todas as instalações do Node.js. Depois de instalar o Node.js, execute o seguinte `npm` comando para instalar o Azurite.

```console
npm install -g azurite
```

Depois de instalar o Azurite, consulte [run Azurite a partir de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instale e execute a imagem Azurite Docker

Utilize o [DockerHub](https://hub.docker.com/) para puxar a [mais recente imagem Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) utilizando o seguinte comando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Executar a imagem Azurite Docker:**

O comando seguinte executa a imagem azurite Docker. O `-p 10000:10000` parâmetro redireciona os pedidos da porta 10000 da máquina hospedeira para a instância Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Especifique a localização do espaço de trabalho:**

No exemplo seguinte, o `-v c:/azurite:/data` parâmetro especifica *c:/azurite* como o Azurite persistiu na localização dos dados. O diretório, *c:/azurite,* deve ser criado antes de executar o comando Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Executar apenas o serviço de blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Para obter mais informações sobre a configuração do Azurite no arranque, consulte [as opções da linha de comando](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Clone, construa e gere azurite do repositório GitHub

Este método de instalação requer que tenha o [Git](https://git-scm.com/) instalado. Clone o [repositório GitHub](https://github.com/azure/azurite) para o projeto Azurite utilizando o seguinte comando de consola.

```console
git clone https://github.com/Azure/Azurite.git
```

Depois de clonar o código fonte, execute os seguintes comandos da raiz do repo clonado para construir e instalar o Azurite.

```console
npm install
npm run build
npm install -g
```

Depois de instalar e construir azurite, consulte [Run Azurite a partir de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Executar Azurite de uma linha de comando

> [!NOTE]
> A Azurite não pode ser executada a partir da linha de comando se apenas tiver instalado a extensão do Código do Estúdio Visual. Em vez disso, utilize a paleta de comando visual Studio Code. Para mais informações, consulte Instalar e executar a extensão do Código do [Estúdio Visual Azurite](#install-and-run-the-azurite-visual-studio-code-extension).

Para começar imediatamente com a linha de comando, crie um diretório chamado *c:\azurite,* em seguida, lançar Azurite emitindo o seguinte comando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Este comando diz à Azurite para armazenar todos os dados num determinado diretório, *c:\azurite*. Se a `--location` opção for omitida, utilizará o atual diretório de trabalho.

## <a name="command-line-options"></a>Opções da linha de comandos

Esta secção detalha os interruptores da linha de comando disponíveis ao lançar o Azurite.

### <a name="help"></a>Ajuda

**Opcional** - Obtenha ajuda na linha de comando utilizando o `-h` interruptor ou o `--help` interruptor.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Hospedeiro de escuta blob

**Opcional** - Por padrão, o Azurite ouvirá 127.0.0.1 como servidor local. Utilize o `--blobHost` interruptor para definir o endereço às suas necessidades.

Aceite pedidos apenas na máquina local:

```console
azurite --blobHost 127.0.0.1
```

Permitir pedidos remotos:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Permitir pedidos remotos pode tornar o seu sistema vulnerável a ataques externos.

### <a name="blob-listening-port-configuration"></a>Configuração da porta de escuta Blob

**Opcional** - Por padrão, a Azurite irá ouvir o serviço Blob na porta 10000. Utilize o interruptor para especificar a porta de `--blobPort` audição de que necessita.

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

A porta em uso é exibida durante a startup Azurite.

### <a name="queue-listening-host"></a>Anfitrião de escuta de fila

**Opcional** - Por padrão, o Azurite ouvirá 127.0.0.1 como servidor local. Utilize o `--queueHost` interruptor para definir o endereço às suas necessidades.

Aceite pedidos apenas na máquina local:

```console
azurite --queueHost 127.0.0.1
```

Permitir pedidos remotos:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Permitir pedidos remotos pode tornar o seu sistema vulnerável a ataques externos.

### <a name="queue-listening-port-configuration"></a>Configuração da porta de escuta da fila

**Opcional** - Por padrão, a Azurite irá ouvir o serviço de fila na porta 10001. Utilize o interruptor para especificar a porta de `--queuePort` audição de que necessita.

> [!NOTE]
> Depois de utilizar uma porta personalizada, é necessário atualizar a cadeia de ligação ou a configuração correspondente nas suas ferramentas de Armazenamento Azure ou SDKs.

Personalize a porta de escuta do serviço fila:

```console
azurite --queuePort 8888
```

Deixe o sistema selecionar automaticamente uma porta disponível:

```console
azurite --queuePort 0
```

A porta em uso é exibida durante a startup Azurite.

### <a name="workspace-path"></a>Caminho do espaço de trabalho

**Opcional** - Azurite armazena dados no disco local durante a execução. Utilize o `-l` ou o interruptor para especificar um caminho como local do espaço de `--location` trabalho. Por predefinição, o diretório de trabalho do processo atual será utilizado. Reparem na minúscula 'l'.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Registo de acesso

**Opcional** - Por padrão, o registo de acesso é apresentado na janela da consola. Desative o visualização do registo de acesso utilizando o `-s` ou `--silent` o interruptor.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Registo de depuração

**Opcional** - O registo de depuração inclui informações detalhadas sobre cada pedido e rastreio de pilha de exceção. Ative o registo de depuração fornecendo um caminho de ficheiro local válido para o `-d` interruptor ou `--debug` comutado.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Modo solto

**Opcional** - Por padrão, a Azurite aplica um modo rigoroso para bloquear cabeçalhos e parâmetros de pedido não suportados. Desative o modo rígido utilizando o `-L` ou `--loose` o interruptor. Reparem no "L" maiúsculo.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Versão

**Opcional** - Visualizar o número da versão Azurite instalado utilizando o `-v` interruptor ou o `--version` interruptor.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Configuração do certificado (HTTPS)

**Opcional** - Por padrão, a Azurite utiliza o protocolo HTTP. Ative o modo HTTPS fornecendo um caminho para um ficheiro de certificado de Correio Melhorado pela Privacidade (.pem) ou [Personal Information Exchange (.pfx)](https://docs.microsoft.com/windows-hardware/drivers/install/personal-information-exchange---pfx--files) para o `--cert` interruptor.

Quando `--cert` for fornecido um ficheiro PEM, deve fornecer um interruptor `--key` correspondente.

```console
azurite --cert path/server.pem --key path/key.pem
```

Quando `--cert` for fornecido um ficheiro PFX, deve fornecer um interruptor `--pwd` correspondente.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Para obter informações detalhadas sobre a criação de ficheiros PEM e PFX, consulte [https Configuração](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>Configuração OAuth

**Opcional** - Ativar a autenticação OAuth para o Azurite utilizando o `--oauth` interruptor.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> O AAuth requer um ponto final HTTPS. Certifique-se de que https está ativado fornecendo `--cert` o interruptor juntamente com o `--oauth` interruptor.

A Azurite suporta a autenticação básica especificando o `basic` parâmetro para o `--oauth` interruptor. Azurite fará a autenticação básica, como validar o token do portador, verificar o emitente, o público e a caducidade. Azurite não verifica a assinatura ou permissões simbólicas.

## <a name="authorization-for-tools-and-sdks"></a>Autorização para ferramentas e SDKs

Ligue-se à Azurite a partir de SDKs ou ferramentas de armazenamento Azure, como [o Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)utilizando qualquer estratégia de autenticação. É necessária autenticação. A Azurite apoia a autorização com a OAuth, Shared Key e assinaturas de acesso partilhada (SAS). Azurite também apoia o acesso anónimo a contentores públicos.

Se estiver a utilizar os SDKs Azure, inicie o Azurite com as `--oauth basic and --cert --key/--pwd` opções.

### <a name="well-known-storage-account-and-key"></a>Conta de armazenamento bem conhecida e chave

Azurite aceita a mesma conta conhecida e chave usada pelo legado emulador de armazenamento Azure.

- Nome da conta:`devstoreaccount1`
- Chave da conta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Contas e chaves de armazenamento personalizados

A Azurite suporta nomes e chaves de conta de armazenamento personalizado, definindo a `AZURITE_ACCOUNTS` variável ambiental no seguinte formato: `account1:key1[:key2];account2:key1[:key2];...` .

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

A Azurite atualiza os nomes e chaves de conta personalizadas da variável ambiental a cada minuto por padrão. Com esta funcionalidade, pode rodar dinamicamente a chave da conta ou adicionar novas contas de armazenamento sem reiniciar o Azurite.

> [!NOTE]
> A conta de armazenamento predefinida `devstoreaccount1` é desativada quando define contas de armazenamento personalizadas.

### <a name="connection-strings"></a>Cadeias de ligação

A forma mais fácil de ligar ao Azurite a partir da sua aplicação é configurar uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho *UseDevelopmentStorage=true*. Aqui está um exemplo de uma cadeia de ligação num ficheiro *app.config:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>Cordas de ligação HTTP

Pode passar as seguintes cordas de ligação aos [SDKs ou ferramentas Azure,](https://aka.ms/azsdk) como o Azure CLI 2.0 ou o Storage Explorer.

A corda de ligação completa é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Para ligar apenas ao serviço de blob, a cadeia de ligação é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Para ligar apenas ao serviço de fila, a cadeia de ligação é:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>Cordas de ligação HTTPS

A cadeia de ligação HTTPS completa é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Para utilizar apenas o serviço de blob, a cadeia de ligação HTTPS é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Para utilizar apenas o serviço de fila, a cadeia de ligação HTTPS é:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Se tiver gerado `dotnet dev-certs` o seu certificado auto-assinado, utilize a seguinte cadeia de ligação.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Atualize a cadeia de ligação quando utilizar [contas e chaves de armazenamento personalizadas](#custom-storage-accounts-and-keys).

Para mais informações, consulte as cordas de ligação de [armazenamento Configure Azure](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>SDKs do Azure

Para utilizar o Azurite com os [SDKs Azure,](https://aka.ms/azsdk)utilize opções OAuth e HTTPS:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Em seguida, pode instantaneamente instantaneamente um BlobContainerClient, BlobServiceClient ou BlobClient.

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

Também pode instantaneamente um QueueClient ou QueueServiceClient.

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

Pode utilizar o Storage Explorer para ver os dados armazenados em Azurite.

#### <a name="connect-to-azurite-using-http"></a>Ligue-se a Azurite usando HTTP

No Storage Explorer, ligue-se ao Azurite seguindo estes passos:

 1. Selecione o ícone **'Gerir contas'**
 1. **Selecione Adicionar uma conta**
 1. Selecione **Anexar a um emulador local**
 1. Selecione **Next**
 1. Editar o campo de **nome display** para um nome à sua escolha
 1. Selecione **Next** novamente
 1. Selecione **Ligar**

#### <a name="connect-to-azurite-using-https"></a>Ligue-se a Azurite usando HTTPS

Por padrão, o Storage Explorer não abrirá um ponto final HTTPS que utilize um certificado auto-assinado. Se estiver a executar a Azurite com HTTPS, é provável que esteja a usar um certificado auto-assinado. No Storage Explorer, importar certificados **Edit**SSL através do diálogo de certificados de importação  ->  **de certificados SSL**de edição.  ->  **Import Certificates**

##### <a name="import-certificate-to-storage-explorer"></a>Certificado de Importação para Explorador de Armazenamento

1. Encontre o certificado na sua máquina local.
1. No Storage Explorer, vá à **Edição**certificados de importação de  ->  **certificados SSL**  ->  **Import Certificates** e importe o seu certificado.

Se não importar um certificado, terá um erro:

`unable to verify the first certificate` ou `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Adicione Azurite via fio de ligação HTTPS

Siga estes passos para adicionar Azurite HTTPS ao Storage Explorer:

1. Selecione **Toggle Explorer**
1. Selecione **& Local Anexado**
1. Clique à direita nas Contas de **Armazenamento** e selecione **Ligar ao Armazenamento Azure**.
1. Selecione **Utilize uma cadeia de ligação**
1. Selecione **Seguinte**.
1. Introduza um valor no campo de **nome display.**
1. Introduza a cadeia de [ligação HTTPS](#https-connection-strings) da secção anterior deste documento
1. Selecione **Next**
1. Selecione **Ligar**

## <a name="workspace-structure"></a>Estrutura do espaço de trabalho

Os seguintes ficheiros e pastas podem ser criados no local do espaço de trabalho ao rubricar o Azurite.

- `__blobstorage__`- Diretório contendo serviço de blob Azurite persistia dados binários
- `__queuestorage__`- O diretório contendo o serviço de fila Azurite persistiu em dados binários
- `__azurite_db_blob__.json`- Ficheiro de metadados de serviço de blob Azurite
- `__azurite_db_blob_extent__.json`- Ficheiro de metadados de serviço de blob Azurite
- `__azurite_db_queue__.json`- Ficheiro de metadados do serviço de fila Azurite
- `__azurite_db_queue_extent__.json`- Ficheiro de metadados de fila azurite

Para limpar o Azurite, elimine ficheiros e pastas acima e reinicie o emulador.

## <a name="differences-between-azurite-and-azure-storage"></a>Diferenças entre armazenamento Azurite e Azure

Existem diferenças funcionais entre um exemplo local de Azurite e uma conta de Armazenamento Azure na nuvem.

### <a name="endpoint-and-connection-url"></a>Endpoint e URL de ligação

Os pontos finais de serviço para a Azurite são diferentes dos pontos finais de uma conta de Armazenamento Azure. O computador local não faz resolução de nomes de domínio, exigindo que os pontos finais de Azurite sejam endereços locais.

Quando se dirige a um recurso numa conta de Armazenamento Azure, o nome da conta faz parte do nome de anfitrião uri. O recurso que está a ser endereçado faz parte do caminho uri:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

O seguinte URI é um endereço válido para uma bolha numa conta de Armazenamento Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Uma vez que o computador local não faz resolução de nome de domínio, o nome da conta faz parte do caminho URI em vez do nome do anfitrião. Utilize o seguinte formato URI para um recurso em Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

O seguinte endereço pode ser utilizado para aceder a uma bolha em Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Escala e desempenho

A Azurite não suporta um grande número de clientes conectados. Não há garantia de desempenho. A Azurite destina-se a fins de desenvolvimento e teste.

### <a name="error-handling"></a>Processamento de erros

A Azurite está alinhada com a lógica de manipulação de erros do Armazenamento Azure, mas há diferenças. Por exemplo, as mensagens de erro podem ser diferentes, enquanto os códigos de estado de erro se alinham.

### <a name="ra-grs"></a>RA-GRS

A Azurite suporta a replicação georedundantde de acesso de leitura (RA-GRS). Para os recursos de armazenamento, aceda à localização secundária através da adesão `-secondary` ao nome da conta. Por exemplo, o seguinte endereço pode ser utilizado para aceder a uma bolha utilizando o secundário apenas de leitura em Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite é de código aberto

As contribuições e sugestões para o Azurite são bem-vindas. Vá à página de [projeto siogite gitHub](https://github.com/Azure/Azurite/projects) ou [problemas gitHub](https://github.com/Azure/Azurite/issues) para marcos e itens de trabalho que estamos rastreando para as próximas funcionalidades e correções de bugs. Os itens de trabalho detalhados também são rastreados no GitHub.

## <a name="next-steps"></a>Passos seguintes

- [Utilize o emulador de armazenamento Azure para documentos](storage-use-emulator.md) de desenvolvimento e teste do legado emulador de armazenamento Azure, que está a ser substituído pela Azurite.
- [Configurar as cordas](storage-configure-connection-string.md) de ligação de armazenamento Azure explica como montar uma cadeia de ligação Azure STorage válida.
