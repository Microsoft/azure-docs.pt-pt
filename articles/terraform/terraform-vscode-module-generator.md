---
title: Tutorial-criar um modelo de base do Terraform no Azure usando o Yeoman
description: Saiba como criar um modelo de base do Terraform no Azure usando o Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba81d0ee797fd879fdadc3a6b25ca8f310383f61
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159164"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Tutorial: criar um modelo de base do Terraform no Azure usando o Yeoman

Neste tutorial, você aprenderá a usar a combinação de [Terraform](/azure/terraform/) e [Yeoman](https://yeoman.io/). Terraform é uma ferramenta para a criação de infraestrutura no Azure. O Yeoman facilita a criação de módulos Terraform.

Neste artigo, você aprenderá a executar as seguintes tarefas:
> [!div class="checklist"]
> * Crie um modelo de Terraform de base usando o gerador de módulo Yeoman.
> * Teste o modelo Terraform usando dois métodos diferentes.
> * Execute o módulo Terraform usando um arquivo do Docker.
> * Execute o módulo Terraform nativamente no Azure Cloud Shell.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Visual Studio Code**: [Baixe Visual Studio Code](https://code.visualstudio.com/download) para sua plataforma.
- **Terraform**: [Instale o Terraform](/azure/virtual-machines/linux/terraform-install-configure ) para executar o módulo criado pelo Yeoman.
- **Docker**: [Instale o Docker](https://www.docker.com/get-started) para executar o módulo criado pelo gerador Yeoman.
- **Linguagem de programação go**: [instalar o go](https://golang.org/) como casos de teste gerados pelo Yeoman é o código que usa o idioma go.

>[!NOTE]
>A maioria dos procedimentos neste tutorial envolve a interface de linha de comando. As etapas descritas se aplicam a todos os sistemas operacionais e ferramentas de linha de comando. Para os exemplos, o PowerShell foi escolhido para o ambiente local e o Git bash para o ambiente do Cloud Shell.

## <a name="prepare-your-environment"></a>Preparar o ambiente

### <a name="install-nodejs"></a>Instalar o Node.js

Para utilizar o Terraform no Cloud Shell, tem de [instalar o Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Para verificar se o Node.js está instalado, abra uma janela do terminal e introduza `node --version`.

### <a name="install-yeoman"></a>Instalar o Yeoman

Execute o seguinte comando:

```bash
npm install -g yo
```

![Instalar o Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Instalar o modelo Yeoman para o módulo do Terraform

Execute o seguinte comando:

```bash
npm install -g generator-az-terra-module
```

![Instalar o generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Para verificar se o Yeoman está instalado, execute o seguinte comando:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Criar um diretório para o módulo gerado pelo Yeoman

O modelo Yeoman gera arquivos no diretório atual. Assim, terá de criar um diretório.

Este diretório vazio tem de ser colocado em $GOPATH/src. Para obter mais informações sobre esse caminho, consulte o artigo [Setting GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Navegue até o diretório pai do qual criar um novo diretório.

1. Execute o comando a seguir substituindo o espaço reservado. Para este exemplo, é usado um nome de diretório de `GeneratorDocSample`.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navegue até o novo diretório:

    ```bash
    cd <new-directory-name>
    ```

    ![Navegar para o novo diretório](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Criar um modelo de módulo base

1. Execute o seguinte comando:

    ```bash
    yo az-terra-module
    ```

1. Siga as instruções no ecrã para fornecer as seguintes informações:

    - **Nome do projeto do módulo Terraform** -um valor de `doc-sample-module` é usado para o exemplo.

        ![Nome do projeto](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Pretende incluir o ficheiro de imagem do Docker?** -Insira `y`. Se você selecionar `n`, o código de módulo gerado dará suporte somente para execução no modo nativo.

        ![Incluir o ficheiro de imagem do Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Liste o conteúdo do diretório para exibir os arquivos resultantes criados:

    ```bash
    ls
    ```

    ![Listar os ficheiros criados](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Rever o código do módulo gerado

1. Abra o Visual Studio Code.

1. Na barra de menus, selecione **Ficheiro > Abrir Pasta** e selecione a pasta criada.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Os arquivos a seguir foram criados pelo gerador de módulo Yeoman. Para obter mais informações sobre esses arquivos e seu uso, consulte [Terratest em módulos Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

- `main.tf`-define um módulo chamado `random-shuffle`. A entrada é uma `string_list`. O resultado é a contagem das permutas.
- `variables.tf`-define as variáveis de entrada e saída usadas pelo módulo.
- `outputs.tf`-define o que o módulo gera. Aqui, é o valor retornado por `random_shuffle`, que é um módulo interno Terraform.
- `Rakefile`-define as etapas de compilação. Estes passos incluem:
    - `build` – valida a formatação do arquivo main.tf.
    - `unit`-o esqueleto do módulo gerado não inclui o código para um teste de unidade. Se pretender especificar um cenário de teste de unidades, terá de adicionar esse código aqui.
    - `e2e`-executa um teste de ponta a ponta do módulo.
- `test`
    - Os casos de teste são escritos em Go.
    - Todos os códigos de teste são testes ponto a ponto.
    - Os testes de ponta a ponta tentam provisionar todos os itens definidos em `fixture`. Os resultados no arquivo de `template_output.go` são comparados com os valores predefinidos esperados.
    - `Gopkg.lock` e `Gopkg.toml`: define as dependências. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testar o novo módulo do Terraform usando um arquivo do Docker

Esta seção mostra como testar um módulo do Terraform usando um arquivo do Docker.

>[!NOTE]
>Este exemplo executa o módulo localmente; Não no Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Confirmar que o Docker está instalado e em execução

Numa linha de comandos, introduza `docker version`.

![Versão do Docker](media/terraform-vscode-module-generator/ymg-docker-version.png)

O resultado confirma que o Docker está instalado.

Para confirmar que o Docker está mesmo em execução, introduza `docker info`.

![Informações do Docker](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Configurar um contentor do Docker

1. Numa linha de comandos, introduza

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    A mensagem **Criado com êxito** será apresentada.

    ![Mensagem indicando uma compilação bem-sucedida](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. No prompt de comando, digite `docker image ls` para ver o módulo criado `terra-mod-example` listado.

    ![Lista que contém o novo módulo](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Introduza `docker run -it terra-mod-example /bin/sh`. Depois de executar o comando `docker run`, você estará no ambiente do Docker. Nesse ponto, você pode descobrir o arquivo usando o comando `ls`.

    ![Lista de arquivos no Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Compilar o módulo

1. Execute o seguinte comando:

    ```bash
    bundle install
    ```

1. Execute o seguinte comando:

    ```bash
    rake build
    ```

    ![Compilação Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Executar o teste de ponta a ponta

1. Execute o seguinte comando:

    ```bash
    rake e2e
    ```

1. Após alguns momentos, a mensagem **PASS** será apresentada.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Insira `exit` para concluir o teste e sair do ambiente do Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Usar o gerador de Yeoman para criar e testar um módulo no Cloud Shell

Nesta seção, o gerador de Yeoman é usado para criar e testar um módulo no Cloud Shell. Usar Cloud Shell em vez de usar um arquivo do Docker simplifica muito o processo. Usando Cloud Shell, os seguintes produtos são pré-instalados:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Iniciar uma sessão de Cloud Shell

1. Inicie uma sessão de Azure Cloud Shell por meio de [portal do Azure](https://portal.azure.com/), [shell.Azure.com](https://shell.azure.com)ou [aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. **A página Bem-vindo ao Azure cloud Shell** é aberta. Selecione **bash (Linux)** .

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Se ainda não tiver configurado uma conta de armazenamento do Azure, aparecerá o seguinte ecrã. Selecione **Create storage** (Criar armazenamento).

    ![Não tem armazenamento montado](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que selecionou anteriormente e apresenta informações para a unidade de cloud que acabou de criar.

    ![A sua unidade de cloud foi criada](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Preparar um diretório para manter o módulo Terraform

1. Neste ponto, Cloud Shell já terá configurado GOPATH em suas variáveis de ambiente para você. Para ver o caminho, insira `go env`.

1. Crie o diretório $GOPATH, se ainda não existir um: Insira `mkdir ~/go`.

1. Crie um diretório dentro do diretório $GOPATH. Esse diretório é usado para manter os diferentes diretórios de projeto criados neste exemplo. 

    ```bash
    mkdir ~/go/src
    ```

1. Crie um diretório para manter o seu módulo Terraform substituindo o espaço reservado. Para este exemplo, é usado um nome de diretório de `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navegue até o diretório do módulo: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Criar e testar seu módulo Terraform

1. Execute o comando a seguir e siga as instruções. Quando for perguntado se você deseja criar os arquivos do Docker, insira `N`.

    ```bash
    yo az-terra-module
    ```

1. Execute o seguinte comando para instalar as dependências:

    ```bash
    bundle install
    ```

1. Execute o seguinte comando para compilar o módulo:

    ```bash
    rake build
    ```

    ![Compilação Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Execute o seguinte comando para executar o texto:

    ```bash
    rake e2e
    ```

    ![Resultados de aprovação de teste](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale e use a extensão de Visual Studio Code Terraform do Azure](/azure/terraform/terraform-vscode-extension).