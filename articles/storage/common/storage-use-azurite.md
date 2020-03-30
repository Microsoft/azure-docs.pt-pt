---
title: Utilize o emulador Azurite para o desenvolvimento local do armazenamento azure
description: O emulador de código aberto Azurite (pré-visualização) proporciona um ambiente local gratuito para testar as suas aplicações de armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029919"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Utilize o emulador Azurite para desenvolvimento e teste de armazenamento azure local (pré-visualização)

O emulador de código aberto Azurite 3.2 (pré-visualização) proporciona um ambiente local gratuito para testar as suas aplicações de armazenamento de blob Azure e fila. Quando estiver satisfeito com a forma como a sua aplicação está a funcionar localmente, mude para a utilização de uma conta de Armazenamento Azure na nuvem. O emulador fornece suporte cross-platform no Windows, Linux e MacOS. O Azurite v3 suporta APIs implementadas pelo serviço Azure Blob.

Azurite é a futura plataforma de emulador de armazenamento. Azurite substitui o [Emulador de Armazenamento Azure.](storage-use-emulator.md) A Azurite continuará a ser atualizada para suportar as versões mais recentes das APIs de Armazenamento Azure.

Existem várias formas diferentes de instalar e executar o Azurite no seu sistema local:

  1. [Instale e execute a extensão do Código do Estúdio Visual Azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instale e execute o Azurite utilizando o NPM](#install-and-run-azurite-by-using-npm)
  1. [Instale e execute a imagem Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Clone, construa e gere azurite do repositório GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instale e execute a extensão do Código do Estúdio Visual Azurite

Dentro do Código do Estúdio Visual, selecione o painel **DE EXTENSÕES** e procure *azurite* nas **EXTENSÕES:MARKETPLACE**.

![Mercado de extensões de código de estúdio visual](media/storage-use-azurite/azurite-vs-code-extension.png)

Em alternativa, navegue para o mercado de [extensão do Código VS](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) no seu navegador. Selecione o botão **Instalar** para abrir o Código do Estúdio Visual e vá diretamente para a página de extensão Azurite.

Pode iniciar ou fechar rapidamente o Azurite clicando no **[Serviço De Blob Azurite]** ou **[Serviço de Fila Azurite]** na barra de estado do Código VS ou emitindo os seguintes comandos na paleta de comando vs Código. Para abrir a paleta de comando, prima **F1** no Código VS.

A extensão suporta os seguintes comandos do Código do Estúdio Visual:

   * **Azurite: Iniciar** - Iniciar todos os serviços azurite
   * **Azurite: Fechar** - Fechar todos os serviços azurite
   * **Azurite: Clean** - Redefinir todos os serviços azurite persistem dados de persistência
   * **Azurite: Start Blob Service** - Start blob service
   * **Azurite: Serviço de Blob Close** - Serviço de blob de fecho
   * **Azurite: Clean Blob Service** - Serviço de blob limpo
   * **Azurite: Iniciar o serviço de fila** - Iniciar o serviço de fila
   * **Azurite: Serviço de Fila de Encerramento** - Serviço de fila de encerramento
   * **Azurite: Serviço** de fila limpa - Serviço de fila limpa

Para configurar o Azurite dentro do Código do Estúdio Visual, selecione o painel de extensões. Selecione o ícone **Gerir** (engrenagem) para **Azurite**. **Selecione Configurar definições**de extensão .

![Definições de extensão de configuração azurite](media/storage-use-azurite/azurite-configure-extension-settings.png)

As seguintes definições são suportadas:

   * **Azurite: Blob Host** - O ponto final de escuta do serviço Blob. A definição predefinida é de 127.0.0.1.
   * **Azurite: Blob Port** - A porta de escuta do serviço Blob. A porta padrão é 10000.
   * **Azurite: Debug** - Saída o registo de depuração para o canal Azurite. O valor predefinido é **falso**.
   * **Azurite: Localização** - O caminho de localização do espaço de trabalho. O padrão é a pasta de funcionamento do Código do Estúdio Visual.
   * **Azurite: Anfitrião** de fila - O ponto final do serviço de fila. A definição predefinida é de 127.0.0.1.
   * **Azurite: Porta de fila** - A porta de escuta do serviço de fila. A porta padrão é 10001.
   * **Azurite: Modo silencioso** desativa o registo de acesso. O valor predefinido é **falso**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instale e execute o Azurite utilizando o NPM

Este método de instalação requer que tenha a [versão Node.js 8.0 ou posteriormente](https://nodejs.org) instalada. **npm** é a ferramenta de gestão de pacotes incluída em todas as instalações do Node.js. Depois de instalar o Node.js, execute o seguinte comando **npm** para instalar o Azurite.

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
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Especifique a localização do espaço de trabalho:**

No exemplo seguinte, `-v c:/azurite:/data` o parâmetro especifica *c:/azurite* como o Azurite persistiu na localização dos dados. O diretório, *c:/azurite,* deve ser criado antes de executar o comando Docker.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Executar apenas o serviço de blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Definir todos os parâmetros Azurite:**

Este exemplo mostra como definir todos os parâmetros da linha de comando. Todos os parâmetros abaixo devem ser colocados numa única linha de comando.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Consulte [as opções da linha de comando](#command-line-options) para obter mais informações sobre a configuração do Azurite no arranque.

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
> A Azurite não pode ser executada a partir da linha de comando se apenas tiver instalado a extensão do Código do Estúdio Visual. Em vez disso, utilize a paleta de comando vs Código. Para mais informações, consulte Instalar e executar a extensão do Código do [Estúdio Visual Azurite](#install-and-run-the-azurite-visual-studio-code-extension).

Para começar imediatamente com a linha de comando, crie um diretório chamado **c:\azurite,** em seguida, lançar Azurite emitindo o seguinte comando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Este comando diz à Azurite para armazenar todos os dados num determinado diretório, **c:\azurite**. Se a opção **de localização** for omitida, utilizará o atual diretório de trabalho.

## <a name="command-line-options"></a>Opções da linha de comandos

Esta secção detalha os interruptores da linha de comando disponíveis ao lançar o Azurite. Todos os interruptores da linha de comando são opcionais.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

O **d** é um atalho para **--debug,** **-l** switch é um atalho para **--localização**, **-s** é um atalho para **--silencioso**, e **-h** é um atalho para **--ajuda**.

### <a name="blob-listening-host"></a>Hospedeiro de escuta blob

**Opcional** Por predefinição, a Azurite ouvirá 127.0.0.1 como servidor local. Utilize o interruptor **--blobHost** para definir o endereço para os seus requisitos.

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

**Opcional** Por padrão, a Azurite irá ouvir o serviço Blob na porta 10000. Utilize o interruptor **--blobPort** para especificar a porta de audição que necessita.

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

**Opcional** Por predefinição, a Azurite ouvirá 127.0.0.1 como servidor local. Utilize o interruptor **--filaHost** para definir o endereço para os seus requisitos.

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

**Opcional** Por padrão, a Azurite irá ouvir o serviço de fila na porta 10001. Utilize o interruptor **--queuePort** para especificar a porta de audição que necessita.

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

**Opcional** Azurite armazena dados no disco local durante a execução. Utilize o interruptor **de localização** para especificar um caminho como localização do espaço de trabalho. Por predefinição, o diretório de trabalho do processo atual será utilizado.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Registo de acesso

**Opcional** Por predefinição, o registo de acesso é apresentado na janela da consola. Desative o visor do registo de acesso utilizando o interruptor **silencioso.**

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Registo de depuração

**Opcional** O registo de depuração inclui informações detalhadas sobre cada pedido e rastreio de pilha de exceção. Ative o registo de depuração fornecendo um caminho de ficheiro local válido para o interruptor **--depuração.**

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Modo solto

**Opcional** Por predefinição, a Azurite aplica um modo rigoroso para bloquear cabeçalhos e parâmetros de pedido não suportados. Desative o modo rigoroso utilizando o interruptor **--solto.**

```console
azurite --loose
```

Note o interruptor de atalho 'L' maiúsculo:

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autorização para ferramentas e SDKs

Ligue-se à Azurite a partir de SDKs ou ferramentas de armazenamento Azure, como [o Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)utilizando qualquer estratégia de autenticação. É necessária autenticação. A Azurite apoia a autorização com a Chave Partilhada e assinaturas de acesso partilhada (SAS). Azurite também apoia o acesso anónimo a contentores públicos.

### <a name="well-known-storage-account-and-key"></a>Conta de armazenamento bem conhecida e chave

Pode utilizar o seguinte nome de conta e chave com a Azurite. Esta é a mesma conta bem conhecida e chave usada pelo legado emulador de armazenamento Azure.

* Nome da conta:`devstoreaccount1`
* Chave da conta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Além da autenticação SharedKey, a Azurite suporta a autenticação de conta e serviço SAS. O acesso anónimo também está disponível quando um contentor está definido para permitir o acesso do público.

### <a name="connection-string"></a>Cadeia de ligação

A forma mais fácil de ligar ao Azurite a partir da sua aplicação é configurar uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho *UseDevelopmentStorage=true*. Aqui está um exemplo de uma cadeia de ligação num ficheiro *app.config:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Para mais informações, consulte as cordas de ligação de [armazenamento Configure Azure](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Contas e chaves de armazenamento personalizados

A Azurite suporta nomes e chaves `AZURITE_ACCOUNTS` de conta de `account1:key1[:key2];account2:key1[:key2];...`armazenamento personalizado, definindo a variável ambiental no seguinte formato: .

Por exemplo, utilize uma conta de armazenamento personalizada que tenha uma chave:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Ou utilizar várias contas de armazenamento com 2 chaves cada:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

A Azurite atualiza os nomes e chaves de conta personalizadas da variável ambiental a cada minuto por padrão. Com esta funcionalidade, pode rodar dinamicamente a chave da conta ou adicionar novas contas de armazenamento sem reiniciar o Azurite.

> [!NOTE]
> A `devstoreaccount1` conta de armazenamento predefinida é desativada quando define contas de armazenamento personalizadas.

> [!NOTE]
> Atualize a cadeia de ligação em conformidade quando utilizar nomes e chaves de conta personalizadas.

> [!NOTE]
> Utilize `export` a palavra-chave para definir variáveis ambientais num ambiente Linux, use `set` no Windows.

### <a name="storage-explorer"></a>Explorador de Armazenamento

No Azure Storage Explorer, ligue-se ao Azurite clicando no ícone **'Conta Adicionar',** em seguida, selecione **Ligar a um emulador local** e clique em **Ligar**.

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

A Azurite não é um serviço de armazenamento escalável e não suporta um grande número de clientes simultâneos. Não há garantia de desempenho. A Azurite destina-se a fins de desenvolvimento e teste.

### <a name="error-handling"></a>Processamento de erros

A Azurite está alinhada com a lógica de manipulação de erros do Armazenamento Azure, mas há diferenças. Por exemplo, as mensagens de erro podem ser diferentes, enquanto os códigos de estado de erro se alinham.

### <a name="ra-grs"></a>RA-GRS

A Azurite suporta a replicação georedundantde de acesso de leitura (RA-GRS). Para os recursos de armazenamento, aceda à localização secundária através de despesas **-secundárias** ao nome da conta. Por exemplo, o seguinte endereço pode ser utilizado para aceder a uma bolha utilizando o secundário apenas de leitura em Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite é de código aberto

As contribuições e sugestões para o Azurite são bem-vindas. Vá à página de [projeto siogite gitHub](https://github.com/Azure/Azurite/projects) ou [problemas gitHub](https://github.com/Azure/Azurite/issues) para marcos e itens de trabalho que estamos rastreando para as próximas funcionalidades e correções de bugs. Os itens de trabalho detalhados também são rastreados no GitHub.

## <a name="next-steps"></a>Passos seguintes

* [Utilize o emulador de armazenamento Azure para documentos](storage-use-emulator.md) de desenvolvimento e teste do legado emulador de armazenamento Azure, que está a ser substituído pela Azurite.
* [Configurar as cordas](storage-configure-connection-string.md) de ligação de armazenamento Azure explica como montar uma cadeia de ligação Azure STorage válida.
