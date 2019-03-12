---
title: Criar um modelo de base do Terraform no Azure com o Yeoman
description: Saiba como criar um modelo de base do Terraform no Azure com o Yeoman.
services: terraform
ms.service: azure
keywords: terraform, devops, máquina virtual, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 489f81842cca864ad1d55d7bb524fea7af960c65
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775837"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Criar um modelo de base do Terraform no Azure com o Yeoman

O [Terraform](https://docs.microsoft.com/azure/terraform/
) permite criar facilmente infraestruturas no Azure. O [Yeoman](http://yeoman.io/) facilita consideravelmente a tarefa do programador de módulos na criação de módulos do Terraform ao fornecer uma excelente estrutura de *boas práticas*.

Neste artigo, vai aprender a utilizar o gerador de módulos Yeoman para criar um modelo base do Terraform. Em seguida, aprenderá como testar o novo modelo de Terraform através de dois métodos diferentes:

- Execute seu módulo Terraform através de um ficheiro de Docker que criou neste artigo.
- Execute nativamente seu módulo do Terraform no Azure Cloud Shell.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Visual Studio Code**: Usaremos [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) para examinar os ficheiros criados pelo Yeoman gerador. No entanto, pode utilizar qualquer editor de código da sua preferência.
- **Terraform**: Será necessária uma instalação da [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) para executar o módulo criado por Yeoman.
- **Docker**: Usaremos [Docker](https://www.docker.com/get-started) executar o módulo criado pelo Yeoman, gerador. (Se preferir, pode utilizar o Ruby em vez do Docker para executar o módulo de exemplo).
- **Aceda a linguagem de programação**: Será necessária uma instalação da [ir](https://golang.org/) porque os casos de teste gerados por Yeoman são escritos em Go.

>[!NOTE]
>A maioria dos procedimentos neste tutorial envolve entradas de linhas de comandos. Os passos descritos aqui aplicam-se a todos os sistemas operativos e ferramentas de linha de comandos. Nos nossos exemplos, podemos optar por utilizar o PowerShell para o ambiente local e o Git Bash para o ambiente do cloud shell.

## <a name="prepare-your-environment"></a>Preparar o ambiente

### <a name="install-nodejs"></a>Instalar o Node.js

Para utilizar o Terraform no Cloud Shell, tem de [instalar o Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Para verificar se o Node.js está instalado, abra uma janela do terminal e introduza `node --version`.

### <a name="install-yeoman"></a>Instalar o Yeoman

Numa linha de comandos, introduza `npm install -g yo`

![Instalar o Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Instalar o modelo Yeoman para o módulo do Terraform

Numa linha de comandos, introduza `npm install -g generator-az-terra-module`.

![Instalar o generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Para verificar se o Yeoman está instalado, numa janela do terminal, introduza `yo --version`.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Criar uma pasta vazia para conter o módulo gerado pelo Yeoman

O modelo do Yeoman gera ficheiros no **diretório atual**. Assim, terá de criar um diretório.

>[!Note]
>Este diretório vazio tem de ser colocado em $GOPATH/src. Para tal, clique [aqui](https://github.com/golang/go/wiki/SettingGOPATH) para obter as instruções.

Numa linha de comandos:

1. Navegue para o diretório principal que pretende que contenha o novo diretório vazio que vamos criar.
1. Introduza `mkdir <new-directory-name>`.

    >[!NOTE]
    >Substitua <new-directory-name> pelo nome do novo diretório. Neste exemplo, demos ao novo diretório o nome `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navegue para o novo diretório. Para tal, escreva `cd <new directory's name>` e, em seguida, prima **Enter**.

    ![Navegar para o novo diretório](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Para confirmar que este diretório está vazio, introduza `ls`. Não deve haver ficheiros listados no resultado deste comando.

## <a name="create-a-base-module-template"></a>Criar um modelo de módulo base

Numa linha de comandos:

1. Introduza `yo az-terra-module`.

1. Siga as instruções no ecrã para fornecer as seguintes informações:

    - *Nome de projeto do módulo Terraform*

        ![Nome do projeto](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >Neste exemplo, introduzimos `doc-sample-module`.

    - *Pretende incluir o ficheiro de imagem do Docker?*

        ![Incluir o ficheiro de imagem do Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Introduza `y`. Se selecionou **n**, o código gerado módulo irá suportar executado apenas no modo nativo.

3. Introduza `ls` para ver os ficheiros resultantes que são criados.

    ![Listar os ficheiros criados](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Rever o código do módulo gerado

1. Abra o Visual Studio Code.

1. Na barra de menus, selecione **Ficheiro > Abrir Pasta** e selecione a pasta criada.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Vamos ver alguns dos ficheiros que foram criados pelo gerador de módulos Yeoman.

>[!Note]
>Neste artigo, vamos utilizar os ficheiros main.tf, variables.tf e outputs.tf, tal como foram criados pelo gerador de módulos Yeoman. No entanto, ao criar os seus próprios módulos, deverá editar estes ficheiros para acomodar a funcionalidade do seu módulo do Terraform. Para obter uma descrição mais aprofundada destes ficheiros e a sua utilização, veja [Módulos do Terratest em Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.tf

Define um módulo chamado *random-shuffle*. A entrada é uma *string_list*. O resultado é a contagem das permutas.

### <a name="variablestf"></a>variables.tf

Define as variáveis de entrada e de saída utilizadas pelo módulo.

### <a name="outputstf"></a>outputs.tf

Define o resultado do módulo. Aqui, é o valor devolvido por **random_shuffle**, que é um módulo do Terraform incorporado.

### <a name="rakefile"></a>Rakefile

Define os passos de compilação. Estes passos incluem:

- **build**: Valida a formatação do ficheiro main.tf.
- **unit**: A estrutura do módulo gerado não inclui o código para um teste de unidade. Se pretender especificar um cenário de teste de unidades, terá de adicionar esse código aqui.
- **e2e**: Executa um teste de ponto a ponto do módulo.

### <a name="test"></a>test

- Os casos de teste são escritos em Go.
- Todos os códigos de teste são testes ponto a ponto.
- Os testes ponto a ponto tentam utilizar o Terraform para aprovisionar todos os itens definidos em **fixture** e, em seguida, comparar o resultado no código **template_output.go** com os valores esperados predefinidos.
- **Gopkg.LOCK** e **gopkg**: Defina as suas dependências. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testar o seu novo módulo do Terraform através de um ficheiro de Docker

>[!NOTE]
>No nosso exemplo, estamos a executar o módulo como um módulo local, sem mexer realmente no Azure.

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

    ![Criado com êxito](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Na linha de comandos, escreva `docker image ls`.

    Verá o módulo recém-criado *terra-mod-example* listado.

    ![Resultados do repositório](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >O nome do módulo, *terra-mod-example*, foi especificado no comando que introduziu no passo 1, acima.

1. Introduza `docker run -it terra-mod-example /bin/sh`.

    Está agora a executar o Docker e pode listar o ficheiro ao introduzir `ls`.

    ![Listar o ficheiro do Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Criar o módulo

1. Introduza `bundle install`.

    Aguarde a mensagem **Grupo concluído** e, em seguida, continue com o próximo passo.

1. Introduza `rake build`.

    ![Compilação Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Execute o teste de ponto a ponto

1. Introduza `rake e2e`.

1. Após alguns momentos, a mensagem **PASS** será apresentada.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Introduza `exit` para concluir o teste de ponto-a-ponto e sair o ambiente do Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Gerador Yeoman de utilização para criar e testar um módulo no Cloud Shell

Na secção anterior, aprendeu como testar um módulo de Terraform através de um ficheiro de Docker. Nesta secção, irá utilizar o Yeoman gerador para criar e testar um módulo no Cloud Shell.

Utilizar o Cloud Shell em vez de utilizar um ficheiro do Docker bastante simplifica o processo. Utilizar o Cloud Shell:

- Não é necessário instalar node. js
- Não é necessário instalar o Yeoman
- Não é necessário instalar o Terraform

Todos esses itens são previamente instalados no Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Iniciar uma sessão do Cloud Shell

1. Inicie uma sessão do Azure Cloud Shell através do [portal do Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com), ou o [aplicação móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. **O bem-vindo ao Azure Cloud Shell** é aberta a página. Selecione **Bash (Linux)**. (Power Shell não é suportado.)

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Neste exemplo, foi selecionado, Bash (Linux).

1. Se ainda não tiver configurado uma conta de armazenamento do Azure, aparecerá o seguinte ecrã. Selecione **Create storage** (Criar armazenamento).

    ![Não tem armazenamento montado](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que selecionou anteriormente e apresenta informações para a unidade de cloud que acabou de criar.

    ![A sua unidade de cloud foi criada](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Preparar uma pasta para armazenar seu módulo Terraform

1. Neste momento, Cloud Shell irá ter configurado GOPATH nas variáveis do seu ambiente para. Para ver o caminho, introduza `go env`.

1. Crie a pasta $GOPATH, se ainda não existir: Introduza `mkdir ~/go`.

1. Crie uma pasta dentro da pasta $GOPATH: Introduza `mkdir ~/go/src`. Esta pasta será utilizada para armazenar e organizar as pastas de projeto diferente que pode criar, como a pasta de < your-módulo-name > que iremos criar no próximo passo.

1. Crie uma pasta para armazenar seu módulo Terraform: Introduza `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >Neste exemplo, escolhemos `my-module-name` para o nome da pasta.

1. Navegue até à pasta do módulo: Enter `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Criar e testar seu módulo Terraform

1. Introduza `yo az-terra-module` e siga as instruções no assistente.

    >[!NOTE]
    >Quando lhe for perguntado se pretende criar os ficheiros de Docker, pode introduzir `N`.

1. Introduza `bundle install` para instalar as dependências.

    Aguarde a mensagem **Grupo concluído** e, em seguida, continue com o próximo passo.

1. Introduza `rake build` para criar o seu módulo.

    ![Compilação Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Introduza `rake e2e` para executar o teste de ponto-a-ponto.

1. Após alguns momentos, a mensagem **PASS** será apresentada.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Instalar e utilizar a extensão Azure Terraform Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
