---
title: Tutorial-provisionar a infraestrutura com os slots de implantação do Azure usando o Terraform
description: Tutorial sobre como utilizar o Terraform com os blocos de implementação de fornecedor do Azure
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 0bfd10325f1a62e74f0d3573f052d114069491a3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838067"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Tutorial: provisionar a infraestrutura com os slots de implantação do Azure usando o Terraform

Pode utilizar os [blocos de implementação do Azure](/azure/app-service/deploy-staging-slots) para alternar entre diferentes versões da sua aplicação. Esta capacidade ajuda-o a minimizar o impacto de implementações interrompidas. 

Este artigo apresenta um exemplo de utilização de blocos de implementação ao orientá-lo pela implementação de duas aplicações através do GitHub e do Azure. Uma das aplicações encontra-se alojada num bloco de produção. A segunda aplicação encontra-se alojada num bloco de teste. (Os nomes "produção" e "preparo" são arbitrários. Eles podem ser qualquer um que seja apropriado para seu cenário.) Depois de configurar os slots de implantação, use Terraform para alternar entre os dois slots, conforme necessário.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Conta do GitHub**: tem de ter uma conta no [GitHub](https://www.github.com) para criar um fork e utilizar o repositório de teste do GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Criar e aplicar o plano do Terraform

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se não tiver selecionado um ambiente anteriormente, selecione **Bash** como o seu ambiente.

    ![Comandos do Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório denominado `deploy`.

    ```bash
    mkdir deploy
    ```

1. Crie um diretório denominado `swap`.

    ```bash
    mkdir swap
    ```

1. Utilize o comando bash `ls` para verificar se criou com êxito ambos os diretórios.

    ![Cloud Shell após criar diretórios](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Mude para o diretório `deploy`.

    ```bash
    cd deploy
    ```

1. No Cloud Shell, crie um ficheiro com o nome `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

1. Agora que já criou o ficheiro, verifique os seus conteúdos.

    ```bash
    cat deploy.tf
    ```

1. Inicie o Terraform.

    ```bash
    terraform init
    ```

1. Crie o plano do Terraform.

    ```bash
    terraform plan
    ```

1. Aprovisione os recursos que estão definidos no ficheiro de configuração `deploy.tf`. (Confirme a ação ao introduzir `yes` no pedido.)

    ```bash
    terraform apply
    ```

1. Feche a janela do Cloud Shell.

1. No menu principal do portal do Azure, selecione **Grupos de recursos**.

    ![Seleção dos "Grupos de recursos" no portal](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. No separador **Grupos de recursos** selecione **slotDemoResourceGroup**.

    ![Grupo de recursos criado pelo Terraform](./media/terraform-slot-walkthru/resource-group.png)

Agora consegue ver todos os recursos que o Terraform criou.

![Recursos criados pelo Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Criar um fork para o projeto de teste

Antes de poder testar a criação e alternar entre os blocos de implementação, precisa de criar um fork do projeto de teste a partir do GitHub.

1. Aceda ao [repositório awesome-terraform no GitHub](https://github.com/Azure/awesome-terraform).

1. Crie um fork para o repositório **awesome-terraform**.

    ![Criar um fork para o repositório awesome-terraform no GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Siga quaisquer mensagens para criar um fork para o seu ambiente.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Implementar os seus blocos de implementação a partir do GitHub

Após criar o fork para o repositório do projeto de teste, configure os blocos de implementação através dos seguintes passos:

1. No menu principal do portal do Azure, selecione **Grupos de recursos**.

1. Selecione **slotDemoResourceGroup**.

1. Selecione **slotAppService**.

1. Selecione **Opções de implementação**.

    ![Opções de implementação para um recurso do Serviço de Aplicações](./media/terraform-slot-walkthru/deployment-options.png)

1. No separador **Opção de implementação**, selecione **Escolher Origem** e, em seguida, selecione **GitHub**.

    ![Selecionar a origem da implementação](./media/terraform-slot-walkthru/select-source.png)

1. Após o Azure efetuar a ligação e apresentar todas as opções, selecione **Autorização**.

1. No separador **Autorização**, selecione **Autorizar** e forneça as credenciais de que o Azure precisa para aceder à sua conta do GitHub. 

1. Após o Azure validar as suas credenciais do GitHub, é apresentada uma mensagem que indica que o processo de autorização foi concluído. Selecione **OK** para fechar o separador **Autorização**.

1. Selecione **Escolha a sua organização** e selecione a sua organização.

1. Selecione **Escolher projeto**.

1. No separador **Escolher projeto**, selecione o projeto **awesome-terraform**.

    ![Selecionar o projeto awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Selecione **Escolher ramo**.

1. No separador **Escolher ramo**, selecione **principal**.

    ![Selecionar o ramo principal](./media/terraform-slot-walkthru/choose-branch-master.png)

1. No separador **Opção de implementação**, selecione **OK**.

Neste ponto, você implantou o slot de produção. Para implantar o slot de preparo, execute as etapas anteriores com as seguintes modificações:

- No passo 3, selecione o recurso **slotAppServiceSlotOne**.

- No passo 13, selecione o ramo de trabalho em vez do ramo principal.

    ![Selecionar o ramo de trabalho](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testar as implementações da aplicação

Nas secções anteriores, configurou dois blocos, o **slotAppService** e o **slotAppServiceSlotOne** para implementar a partir de diferentes ramos no GitHub. Vamos pré-visualizar as aplicações Web para nos certificarmos de que estas foram implementadas com êxito.

1. No menu principal do portal do Azure, selecione **Grupos de recursos**.

1. Selecione **slotDemoResourceGroup**.

1. Selecione o **slotAppService** ou o **slotAppServiceSlotOne**.

1. Na página Descrição geral, selecione **URL**.

    ![Selecionar o URL no separador da descrição geral para compor a aplicação](./media/terraform-slot-walkthru/resource-url.png)

1. Dependendo do aplicativo selecionado, você verá os seguintes resultados:
    - aplicativo Web **slotAppService** – página azul com um título de página de **slot demo app 1**. 
    - aplicativo Web **slotAppServiceSlotOne** – página verde com um título de página do **slot demo app 2**.

    ![Pré-visualizar as aplicações para testar se estas foram implementadas com êxito](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Alternar entre dois blocos de implementação

Para testar a troca de dois slots de implantação, execute as seguintes etapas:
 
1. Alterne para o separador do browser que está a executar o **slotAppService** (a aplicação com a página azul). 

1. Regresse ao portal do Azure num separador diferente.

1. Abra o Cloud Shell.

1. Altere para o diretório **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. No Cloud Shell, crie um ficheiro com o nome `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

1. Inicie o Terraform.

    ```bash
    terraform init
    ```

1. Crie o plano do Terraform.

    ```bash
    terraform plan
    ```

1. Aprovisione os recursos que estão definidos no ficheiro de configuração `swap.tf`. (Confirme a ação ao introduzir `yes` no pedido.)

    ```bash
    terraform apply
    ```

1. Após o Terraform ter trocado os slots, retorne ao navegador. Atualize a página. 

A aplicação Web no seu bloco de teste **slotAppServiceSlotOne** foi alternada para o bloco de produção e encontra-se representada a verde. 

![Os blocos de implementação foram alternados](./media/terraform-slot-walkthru/slots-swapped.png)

Para regressar à versão de produção original da aplicação, volte a aplicar o plano do Terraform que criou a partir do ficheiro de configuração `swap.tf`.

```bash
terraform apply
```

Após a aplicação alternar, verá a configuração original.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)