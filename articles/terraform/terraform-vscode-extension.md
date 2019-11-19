---
title: Tutorial – configurar a extensão de Visual Studio Code do Terraform do Azure
description: Saiba como instalar e utilizar a extensão do Azure Terraform no Visual Studio Code.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 7f2f15c3a45480b8922f34e191fb965d5e83d9fa
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159230"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>Tutorial: configurar a extensão de Visual Studio Code do Terraform do Azure

A extensão Visual Studio Code do Azure Terraform permite que você trabalhe com o Terraform no editor. Com essa extensão, você pode criar, testar e executar configurações de Terraform. A extensão também dá suporte à visualização de grafo de recursos.

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Automatizar o provisionamento de serviços do Azure usando o Terraform
> * Instale e use a extensão de Visual Studio Code Terraform para os serviços do Azure.
> * Use Visual Studio Code para gravar, planejar e executar planos de Terraform.

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Terraform**: [Instalar e configurar o Terraform](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: instalar a versão do [Visual Studio Code](https://code.visualstudio.com/download) mais adequada ao seu ambiente.

## <a name="prepare-your-dev-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="install-git"></a>Instalar o Git

Para concluir os exercícios neste artigo, precisa de [instalar o Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Instalar o Terraform da HashiCorp

Siga as instruções na página Web [Install Terraform](https://www.terraform.io/intro/getting-started/install.html) (Instalar o Terraform) da HashiCorp, a qual abrange:

- Transferir o Terraform
- Instalar o Terraform
- Verificar se o Terraform está corretamente instalado

>[!Tip]
>Certifique-se de que segue as instruções referentes à definição da sua variável do sistema PATH.

### <a name="install-nodejs"></a>Instalar o Node.js

Para utilizar o Terraform no Cloud Shell, tem de [instalar o Node.js](https://nodejs.org/) 6.0+.

>[!NOTE]
>Para confirmar que o Node.js está instalado, abra uma janela do terminal e introduza `node -v`.

### <a name="install-graphviz"></a>Instalar o GraphViz

Para utilizar a função de visualização do Terraform, precisa de [instalar o GraphViz](https://graphviz.org/).

>[!NOTE]
>Para confirmar que o GraphViz está instalado, abra uma janela do termo e introduza `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Instalar a extensão do Azure Terraform no Visual Studio Code

1. Abra o Visual Studio Code.

1. Selecione **Extensions** (Extensões).

    ![Botão Extensions (Extensões)](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Utilize a caixa de texto **Search Extensions in Marketplace** (Procurar Extensões no Marketplace) para procurar a extensão do Azure Terraform:

    ![Procurar extensões do Visual Studio Code no Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Selecione **Instalar**.

    >[!NOTE]
    >Quando selecionar **Install** (Instalar) para a extensão Azure Terraform, o Visual Studio Code irá automaticamente instalar a extensão de Conta do Azure. A Conta do Azure é um ficheiro de dependência para a extensão do Azure Terraform, que utiliza para executar autenticações de subscrição do Azure e extensões de código relacionadas com o Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Verificar se a extensão do Terraform está instalada no Visual Studio Code

1. Selecione **Extensions** (Extensões).

1. Introduza `@installed` na caixa de texto de pesquisa.

    ![Extensões instaladas](media/terraform-vscode-extension/tf-installed-extensions.png)

A extensão Azure Terraform irá aparecer na lista de extensões instaladas.

![Extensões Terraform instaladas](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Pode agora executar todos os comandos suportados do Terraform no seu ambiente do Cloud Shell a partir do Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Exercício: guia de comandos básicos do Terraform

Neste exercício, irá criar e executar um ficheiro de configuração básico do Terraform que aprovisiona um novo grupo de recursos do Azure.

### <a name="prepare-a-test-plan-file"></a>Preparar um ficheiro de plano de teste

1. No Visual Studio Code, selecione **File > New File** (Ficheiro > Novo Ficheiro) na barra de menus.

1. No seu browser, navegue para a [página Terraform azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) e copie o código no bloco de código **Utilização de Exemplo**:

    ![Utilização de Exemplo](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Cole o código copiado no novo ficheiro que criou no Visual Studio Code.

    ![Cole o código Example Usage](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Pode alterar o valor **name** do grupo de recursos, mas este tem de ser exclusivo para a sua subscrição do Azure.

1. Na barra de menus, selecione **File > Save As** (Ficheiro > Guardar Como).

1. Na caixa de diálogo **Save As** (Guardar Como), navegue para um local à sua escolha e selecione **New folder** (Nova pasta). (Mude o nome da nova pasta para um mais descritivo do que *New folder*.)

    >[!NOTE]
    >Neste exemplo, o nome da pasta é TERRAFORM-TEST-PLAN.

1. Certifique-se de que a nova pasta está selecionada e, em seguida, selecione **Open** (Abrir).

1. Na caixa de diálogo **Save As** (Guardar Como), mude o nome predefinido do ficheiro para *main.tf*.

    ![Guardar como main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Selecione **Guardar**.
1. Na barra de menus, selecione **File > Open Folder** (Ficheiro > Abrir Pasta). Navegue para e selecione a nova pasta que criou.

### <a name="run-terraform-init-command"></a>Executar o comando *init* do Terraform

1. Abra o Visual Studio Code.

1. Na barra de menus do Visual Studio Code, selecione **File > Open Folder...** (Ficheiro > Abrir Pasta) e selecione o ficheiro *main.tf*.

    ![Ficheiro main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. Na barra de menus, selecione **View > Command Palette... > Azure Terraform: Init** (Ver > Paleta de Comandos... > Azure Terraform: Init).

1. Quando aparecer a confirmação, selecione **OK**.

    ![Quer abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Na primeira vez que você iniciar Cloud Shell de uma nova pasta, você será solicitado a criar um aplicativo Web. Selecione **Open** (Abrir).

    ![Primeiro início do Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. É aberta a página de Boas-vindas ao Azure Cloud Shell. Selecione Bash ou PowerShell.

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Neste exemplo, foi selecionado, Bash (Linux).

1. Se ainda não tiver configurado uma conta de armazenamento do Azure, aparecerá o seguinte ecrã. Selecione **Create storage** (Criar armazenamento).

    ![Não tem armazenamento montado](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que selecionou anteriormente e apresenta informações para a unidade de cloud que acabou de criar.

    ![A sua unidade de cloud foi criada](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Pode sair do Cloud Shell

1. Na barra de menus, selecione **View** (Ver) > **Command Palette**(Paleta de Comandos) > **Azure Terraform: init**.

    ![O Terraform foi inicializado com êxito](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Ver o plano

Numa fase anterior deste tutorial, instalou o GraphViz. O Terraform pode usar o GraphViz para gerar uma representação visual de uma configuração ou plano de execução. A extensão Azure Terraform do Visual Studio Code implementa esta funcionalidade por meio do comando *visualize*.

- Na barra de menus, selecione **View > Command Palette > Azure Terraform: Visualize** (Ver > Paleta de Comandos... > Azure Terraform: Visualize).

    ![Ver o plano](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Executar o comando *plan* do Terraform

O comando *plan* do Terraform utiliza-se para ver se o plano de execução para um conjunto de alterações fará o que queria.

>[!NOTE]
>O *plan* não faz mudanças aos seus recursos reais do Azure. Para fazer realmente as alterações contidas no seu plano, usamos o comando *apply* do Terraform.

- Na barra de menus, selecione **View** (Ver) > **Command Palette** (Paleta de Comandos) > **Azure Terraform: plan**.

    ![Comando plan do Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Execute o comando *apply* do Terraform

Quando estiver satisfeito com os resultados do *plan* do Terraform, pode executar o comando *apply* do Terraform.

1. Na barra de menus, selecione **View** (Ver) > **Command Palette** (Paleta de Comandos) > **Azure Terraform: apply**.

    ![Comando apply do Terraform](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Introduza `yes`.

    ![Comndo apply - yes do Terraform](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Verificar se o plano do Terraform foi executado

Para ver se o novo grupo de recursos do Azure foi criado com êxito:

1. Abra o portal do Azure.

1. Selecione **Grupos de Recursos** no painel de navegação à esquerda.

    ![Verificar o novo recurso](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

O novo grupo de recursos deve estar listado na coluna **NAME**.

>[!NOTE]
>Pode deixar a janela do Portal do Azure aberta por enquanto, pois vamos utilizá-la no passo seguinte.

### <a name="run-terraform-destroy-command"></a>Execute o comando *destroy* do Terraform

1. Na barra de menus, selecione **View** (Ver) > **Command Palette** (Paleta de Comandos) > **Azure Terraform: destroy**.

    ![Comando destroy do Terraform](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Introduza *yes*.

    ![Comando destroy - yes do Terraform](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Verificar se o grupo de recursos foi destruído

Para confirmar se o Terraform destruiu com êxito o seu novo grupo de recursos:

1. Selecione **Atualizar** na página de **Grupos de recursos** do portal do Azure.

1. O seu grupo de recursos deixará de estar listado.

    ![Verificar se o grupo de recursos foi destruído](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Exercício 2: módulo *compute* do Terraform

Neste exercício, vai aprender a carregar o módulo *compute* do Terraform no ambiente do Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Clonar o módulo terraform-azurerm-compute

1. Utilize [esta ligação](https://github.com/Azure/terraform-azurerm-compute) para aceder ao módulo Azure Rm Compute no GitHub.

1. Selecione **Clone or download** (Clonar ou transferir).

    ![Clonar ou transferir](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Neste exemplo, a nossa pasta tem o nome *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Abrir a pasta no Visual Studio Code

1. Abra o Visual Studio Code.

1. Na barra de menus, selecione **File > Open Folder** (Ficheiro > Abrir Pasta), navegue para e selecione a pasta criada no passo anterior.

    ![Pasta terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicializar o Terraform

Antes de poder começar a utilizar comandos do Terraform no Visual Studio Code, transfira os plug-ins para dois fornecedores do Azure: random e azurerm.

1. No painel do Terminal do IDE do Visual Studio Code, introduza `terraform init`.

    ![Comando terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Introduza `az login`, prima **Enter** e siga as instruções no ecrã.

### <a name="module-test-lint"></a>Teste de módulo: *lint*

1. Na barra de menus, selecione **View > Command Palette > Azure Terraform: Execute Test** (Ver > Paleta de Comandos... > Azure Terraform: Executar Teste).

1. Na lista de opções de tipos de teste, selecione **lint**.

    ![Selecionar tipo de teste](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Quando a confirmação aparecer, selecione **OK** e siga as instruções no ecrã.

    ![Quer abrir o CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando executar o teste **lint** ou **end to end**, o Azure utiliza um serviço de contentores para aprovisionar uma máquina de teste de forma a executar o teste. Por este motivo, os seus resultados de teste podem geralmente demorar vários minutos para serem devolvidos.

Após uns momentos, verá uma listagem no painel do Terminal semelhante a este exemplo:

![Resultados de teste lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="test-the-module"></a>Testar o módulo

1. Na barra de menus, selecione **View > Command Palette > Azure Terraform: Execute Test** (Ver > Paleta de Comandos... > Azure Terraform: Executar Teste).

1. Na lista de opções de tipos de teste, selecione **end to end**.

    ![Selecionar tipo de teste](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Quando a confirmação aparecer, selecione **OK** e siga as instruções no ecrã.

    ![Quer abrir o CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando executar o teste **lint** ou **end to end**, o Azure utiliza um serviço de contentores para aprovisionar uma máquina de teste de forma a executar o teste. Por este motivo, os seus resultados de teste podem geralmente demorar vários minutos para serem devolvidos.

Após uns momentos, verá uma listagem no painel do Terminal semelhante a este exemplo:

![Resultados do teste](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Lista dos módulos do Terraform disponíveis para Azure (e outros fornecedores suportados)](https://registry.terraform.io/)