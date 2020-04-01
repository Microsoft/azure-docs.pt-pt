---
title: Tutorial - Crie um modelo de base terraforme em Azure usando Yeoman
description: Aprenda a criar um modelo de base terraforme em Azure usando Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472150"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Tutorial: Criar um modelo de base terraforme em Azure usando Yeoman

Neste tutorial, você vai aprender a usar a combinação de [Terraform](/azure/terraform/) e [Yeoman.](https://yeoman.io/) A Terraform é uma ferramenta para criar infraestruturas no Azure. Yeoman facilita a criação de módulos Terraform.

Neste artigo, aprende-se a fazer as seguintes tarefas:
> [!div class="checklist"]
> * Crie um modelo terraforme base utilizando o gerador do módulo Yeoman.
> * Teste o modelo Terraform utilizando dois métodos diferentes.
> * Executar o módulo Terraform usando um ficheiro Docker.
> * Executar o módulo Terraform nativamente em Azure Cloud Shell.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Código do Estúdio Visual**: [Descarregue o Código](https://code.visualstudio.com/download) do Estúdio Visual para a sua plataforma.
- **Terraform**: [Instale terraforma](terraform-install-configure.md) para executar o módulo criado por Yeoman.
- **Docker**: [Instale](https://www.docker.com/get-started) o Docker para executar o módulo criado pelo gerador Yeoman.
- **Vá linguagem de programação**: [Instale Go](https://golang.org/) como os casos de teste gerados por Yeoman são código usando a linguagem Go.

>[!NOTE]
>A maioria dos procedimentos neste tutorial envolvem a interface da linha de comando. As etapas descritas aplicam-se a todos os sistemas operativos e ferramentas de linha de comando. Para os exemplos, a PowerShell foi escolhida para o ambiente local e Git Bash para o ambiente de nuvens.

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

Para verificar se yeoman está instalado, execute o seguinte comando:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Crie um diretório para o módulo gerado pelo Yeoman

O modelo do Yeoman gera ficheiros no diretório atual. Assim, terá de criar um diretório.

Este diretório vazio tem de ser colocado em $GOPATH/src. Para mais informações sobre este caminho, consulte o artigo [Definição de GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Navegue para o diretório-mãe a partir do qual crie um novo diretório.

1. Executar o seguinte comando substituindo o espaço reservado. Para este exemplo, é `GeneratorDocSample` utilizado um nome de diretório.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navegue para o novo diretório:

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

    - Projeto do **módulo Terraform** `doc-sample-module` Nome - Um valor é utilizado para o exemplo.

        ![Nome do projeto](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Pretende incluir o ficheiro de imagem do Docker?** - `y`Entrar. Se selecionar, `n`o código do módulo gerado suportará o funcionamento apenas no modo nativo.

        ![Incluir o ficheiro de imagem do Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Enumera rine os conteúdos do diretório para visualizar os ficheiros resultantes que são criados:

    ```bash
    ls
    ```

    ![Listar os ficheiros criados](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Rever o código do módulo gerado

1. Abra o Visual Studio Code.

1. Na barra de menus, selecione **Ficheiro > Abrir Pasta** e selecione a pasta criada.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Os seguintes ficheiros foram criados pelo gerador do módulo Yeoman. Para obter mais informações sobre estes ficheiros e a sua utilização, consulte [terratest em Módulos Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

- `main.tf`- Define um `random-shuffle`módulo chamado . A entrada é `string_list`um . O resultado é a contagem das permutas.
- `variables.tf`- Define as variáveis de entrada e saída utilizadas pelo módulo.
- `outputs.tf`- Define o que o módulo é saída. Aqui, é o valor `random_shuffle`devolvido por , que é um módulo terraforme embutido.
- `Rakefile`- Define os passos de construção. Estes passos incluem:
    - `build`- Valida a formatação do ficheiro main.tf.
    - `unit`- O esqueleto do módulo gerado não inclui código para um teste de unidade. Se pretender especificar um cenário de teste de unidades, terá de adicionar esse código aqui.
    - `e2e`- Executa um teste de ponta a ponta do módulo.
- `test`
    - Os casos de teste são escritos em Go.
    - Todos os códigos de teste são testes ponto a ponto.
    - Os ensaios de ponta a ponta tentam fornecer `fixture`todos os itens definidos em . Os resultados `template_output.go` do ficheiro são comparados com os valores esperados pré-definidos.
    - `Gopkg.lock`e `Gopkg.toml`: Define as dependências. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Teste o seu novo módulo Terraform usando um ficheiro Docker

Esta secção mostra como testar um módulo Terraform usando um ficheiro Docker.

>[!NOTE]
>Este exemplo executa o módulo localmente; não em Azure.

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

    ![Mensagem indicando uma construção bem sucedida](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. A partir do `docker image ls` pedido de comando, entre para ver o módulo criado `terra-mod-example` listado.

    ![Lista contendo o novo módulo](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Introduza `docker run -it terra-mod-example /bin/sh`. Depois de `docker run` comandar o comando, está no ambiente do Docker. Nessa altura, pode descobrir o ficheiro `ls` usando o comando.

    ![Lista de ficheiros em Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Construir o módulo

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

1. Introduza `exit` para completar o teste e saia do ambiente Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Use o gerador Yeoman para criar e testar um módulo em Cloud Shell

Nesta secção, o gerador Yeoman é usado para criar e testar um módulo em Cloud Shell. Usar a Cloud Shell em vez de usar um ficheiro Docker simplifica muito o processo. Utilizando a Cloud Shell, os seguintes produtos estão todos pré-instalados:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Inicie uma sessão de Cloud Shell

1. Inicie uma sessão Azure Cloud Shell através do [portal Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com), ou da [aplicação móvel Azure.](https://azure.microsoft.com/features/azure-portal/mobile-app/)

1. A página **Welcome to Azure Cloud Shell** abre. Selecione **Bash (Linux)**.

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Se ainda não tiver configurado uma conta de armazenamento do Azure, aparecerá o seguinte ecrã. Selecione **Create storage** (Criar armazenamento).

    ![Não tem armazenamento montado](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que selecionou anteriormente e apresenta informações para a unidade de cloud que acabou de criar.

    ![A sua unidade de cloud foi criada](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Prepare um diretório para segurar o seu módulo Terraform

1. Neste ponto, a Cloud Shell já terá configurado gopath nas suas variáveis ambientais para si. Para ver o `go env`caminho, entre.

1. Crie o diretório $GOPATH, se já `mkdir ~/go`não existir: Enter .

1. Crie um diretório dentro do diretório $GOPATH. Este diretório é usado para manter os diferentes diretórios de projetos criados neste exemplo. 

    ```bash
    mkdir ~/go/src
    ```

1. Crie um diretório para manter o módulo Terraform substituindo o espaço reservado. Para este exemplo, é `my-module-name` utilizado um nome de diretório.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navegue para o seu diretório de módulos: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Crie e teste o seu módulo Terraform

1. Executar o seguinte comando e seguir as instruções. Quando lhe perguntamos se quer criar `N`os ficheiros Does, introduza.

    ```bash
    yo az-terra-module
    ```

1. Executar o seguinte comando para instalar as dependências:

    ```bash
    bundle install
    ```

1. Executar o seguinte comando para construir o módulo:

    ```bash
    rake build
    ```

    ![Compilação Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Executar o seguinte comando para executar o texto:

    ```bash
    rake e2e
    ```

    ![Resultados do teste-passe](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale e utilize a extensão do Código do Estúdio Visual Terraform Azure](/azure/terraform/terraform-vscode-extension).