---
title: Tutorial-criar uma VM do Linux com uma identidade gerenciada da imagem do Azure Marketplace usando o Terraform
description: Criar VM do Linux Terraform com uma identidade gerenciada e gerenciamento de estado remoto usando a imagem do Azure Marketplace
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 233012d6caf1280914a6d2439ae856d69570fff7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838037"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Tutorial: criar uma VM do Linux com uma identidade gerenciada da imagem do Azure Marketplace usando o Terraform

Este artigo mostra-lhe como utilizar uma [imagem Terraform do Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para criar uma VM do Linux Ubuntu (16.04 LTS) com a versão mais recente do [Terraform](https://www.terraform.io/intro/index.html) instalada e configurada com [identidades geridas para os recursos do Azure](/azure/active-directory/managed-service-identity/overview). Esta imagem também configura um back-end remoto para permitir a gestão de [estado remoto](https://www.terraform.io/docs/state/remote.html) através do Terraform. 

A imagem Terraform do Marketplace facilita começar a utilizar o Terraform no Azure sem ter de instalar e configurar o Terraform manualmente. 

Esta imagem de VM do Terraform não tem custos de software. Você paga apenas as tarifas de uso de hardware do Azure com base no tamanho da VM provisionada. 

Para obter mais informações sobre as taxas de computação, consulte a [página de preços da VM do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Pré-requisitos
Para poder criar uma VM Terraform do Linux, você deve ter uma assinatura do Azure. Caso ainda não tenha, veja [Crie hoje a sua conta gratuita do Azure](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Criar sua VM Terraform 

Aqui estão as etapas para criar uma instância de uma VM Terraform do Linux: 

1. No portal do Azure, aceda à listagem [Criar um Recurso](https://ms.portal.azure.com/#create/hub).

1. Na barra de pesquisa **Pesquisar no Marketplace**, procure o **Terraform**. 

1. Selecione **criar**. 

1. As seções a seguir fornecem entradas para cada uma das etapas no Assistente para criar a VM do Linux Terraform. A secção seguinte lista as informações necessárias para configurar cada passo.

## <a name="details-on-the-create-terraform-tab"></a>Detalhes no separador Criar Terraform

Introduza os seguintes dados no separador **Criar Terraform**:

1. **Noções básicas**
    
   * **Nome**: o nome da VM Terraform.
   * **Nome de Utilizador**: o ID de início de sessão da primeira conta.
   * **Palavra-passe**: a palavra-passe da primeira conta. (Pode utilizar uma chave pública SSH em vez de uma palavra-passe.)
   * **Subscrição**: a subscrição na qual a máquina vai ser criada e faturada. Tem de ter privilégios de criação de recursos para esta subscrição.
   * **Grupo de recursos**: um grupo de recursos novo ou existente.
   * **Localização**: o datacenter mais indicado. Normalmente corresponde ao datacenter que tem a maior parte dos seus dados ou a um que esteja mais próximo da sua localização física, para um acesso de rede mais rápido.

2. **Definições adicionais**

   * **Tamanho**: o tamanho da VM. 
   * **Tipo de disco da VM**: SSD ou HDD.

3. **Resumo do Terraform**

   * Certifique-se de que todas as informações que introduziu estão corretas. 

4. **Comprar**

   * Para começar o processo de aprovisionamento, selecione **Comprar**. Receberá uma ligação para os termos da transação. A VM não tem encargos adicionais além da computação para o tamanho do servidor que você escolheu na etapa tamanho.

A imagem de VM Terraform executa as seguintes etapas:

* Cria uma VM com identidade atribuída ao sistema que se baseia na imagem Ubuntu 16.04 LTS.
* Instala as identidades gerenciadas para a extensão de recursos do Azure na VM para permitir que tokens OAuth sejam emitidos para recursos do Azure.
* Atribui permissões RBAC para a identidade gerida e concede direitos de proprietário ao grupo de recursos.
* Cria uma pasta de modelo Terraform (tfTemplate).
* Pré-configura um estado remoto do Terraform com o back-end do Azure.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Acessar e configurar uma VM Terraform do Linux

Depois de criar a VM, execute as seguintes etapas:

1. Entre na VM usando SSH. Use as credenciais de conta que você criou na seção anterior. No Windows, pode transferir uma ferramenta de cliente SSH, como o [Putty](https://www.putty.org/).

1. Conceda permissões de colaborador para toda a assinatura para identidades gerenciadas para recursos do Azure na VM. 

    A permissão de colaborador ajuda as identidades gerenciadas dos recursos do Azure na VM para usar o Terraform para criar recursos fora do grupo de recursos da VM. Execute esta ação executando o seguinte script: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Esse script usa o mecanismo de [logon interativo CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) para autenticar com o Azure. Esse processo atribui a permissão de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para toda a assinatura. 

1. A VM tem um back-end de estado remoto do Terraform. Para habilitá-lo em sua implantação do Terraform, você deve copiar o arquivo de `remoteState.tf` para a raiz dos scripts do Terraform.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Para obter mais informações sobre o gerenciamento de estado remoto, consulte [estado remoto do Terraform](https://www.terraform.io/docs/state/remote.html). A chave de acesso de armazenamento é exposta neste arquivo. Exclua-o antes de confirmar os arquivos de configuração do Terraform no controle do código-fonte.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)