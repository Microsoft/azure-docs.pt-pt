---
title: Utilizar uma imagem do Azure Marketplace para criar uma máquina virtual Terraform Linux com uma identidade gerida
description: Utilize a imagem do Marketplace para criar a máquina virtual Terraform Linux com uma identidade gerida e Gestão de Estado Remota para implementar facilmente recursos no Azure.
services: terraform
ms.service: azure
keywords: terraform, devops, MSI, máquina virtual, estado remoto, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: ae424638967de808f3ffb7d9a56a02398ed79f9e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768158"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Utilizar uma imagem do Azure Marketplace para criar uma máquina virtual Terraform Linux com identidades geridas para recursos do Azure

Este artigo mostra-lhe como utilizar uma [imagem Terraform do Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para criar uma VM do Linux Ubuntu (16.04 LTS) com a versão mais recente do [Terraform](https://www.terraform.io/intro/index.html) instalada e configurada com [identidades geridas para os recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Esta imagem também configura um back-end remoto para permitir a gestão de [estado remoto](https://www.terraform.io/docs/state/remote.html) através do Terraform. 

A imagem Terraform do Marketplace facilita começar a utilizar o Terraform no Azure sem ter de instalar e configurar o Terraform manualmente. 

Esta imagem de VM do Terraform não tem custos de software. Paga apenas as taxas de utilização de hardware do Azure avaliadas com base no tamanho da máquina virtual aprovisionada. Para mais informações sobre as taxas de computação, veja a [página de preços de máquinas virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder criar uma máquina virtual Terraform do Linux, tem de ter uma subscrição do Azure. Caso ainda não tenha, veja [Crie hoje a sua conta gratuita do Azure](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Criar a sua máquina virtual do Terraform 

Eis os passos para criar uma instância de uma máquina virtual Terraform do Linux: 

1. No portal do Azure, aceda à listagem [Criar um Recurso](https://ms.portal.azure.com/#create/hub).

2. Na barra de pesquisa **Pesquisar no Marketplace**, procure o **Terraform**. Selecione o modelo **Terraform**. 

3. No separador de detalhes do Terraform, no canto inferior direito, selecione o botão **Criar**.

    ![Criar uma máquina virtual do Terraform](media/terraformmsi.png)

4. As seguintes secções fornecem informações sobre cada um dos passos no assistente para criar a máquina virtual Terraform do Linux. A secção seguinte lista as informações necessárias para configurar cada passo.

## <a name="details-on-the-create-terraform-tab"></a>Detalhes no separador Criar Terraform

Introduza os seguintes dados no separador **Criar Terraform**:

1. **Noções básicas**
    
   * **Nome**: O nome da sua máquina virtual do Terraform.
   * **Nome de utilizador**: A primeiro de início de sessão na conta ID.
   * **palavra-passe**: A primeira conta palavra-passe. (Pode utilizar uma chave pública SSH em vez de uma palavra-passe.)
   * **Subscrição**: A subscrição em que a máquina está a ser criado e faturadas. Tem de ter privilégios de criação de recursos para esta subscrição.
   * **Grupo de recursos**: Um grupo de recursos novo ou existente.
   * **Localização**: O datacenter mais adequada. Normalmente corresponde ao datacenter que tem a maior parte dos seus dados ou a um que esteja mais próximo da sua localização física, para um acesso de rede mais rápido.

2. **Definições adicionais**

   * **Tamanho**: Tamanho da máquina virtual. 
   * **Tipo de disco VM**: SSD ou HDD.

3. **Resumo do Terraform**

   * Certifique-se de que todas as informações que introduziu estão corretas. 

4. **Comprar**

   * Para começar o processo de aprovisionamento, selecione **Comprar**. Receberá uma ligação para os termos da transação. A VM não implica custos adicionais para além da computação referente ao tamanho de servidor que selecionou no passo de tamanho.

A imagem da VM do Terraform efetua os seguintes passos:

* Cria uma VM com identidade atribuída ao sistema que se baseia na imagem Ubuntu 16.04 LTS.
* Instala a extensão MSI na VM para permitir aos tokens de OAuth serem emitidos para recursos do Azure.
* Atribui permissões RBAC para a identidade gerida e concede direitos de proprietário ao grupo de recursos.
* Cria uma pasta de modelo Terraform (tfTemplate).
* Pré-configura um estado remoto do Terraform com o back-end do Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Aceder e configurar uma máquina virtual Terraform do Linux

Após criar a VM, pode iniciar sessão com SSH. Utilize as credenciais da conta que criou na secção "Noções básicas" do passo 3 para a interface de shell de texto. No Windows, pode transferir uma ferramenta de cliente SSH, como o [Putty](http://www.putty.org/).

Após utilizar o SSH para ligar à máquina virtual, tem de dar permissões de contribuidor para toda a subscrição a identidades geridas para recursos do Azure na máquina virtual. 

A permissão de contribuidor ajuda o MSI na VM a utilizar o Terraform de forma a criar recursos fora do grupo de recursos da VM. Pode facilmente conseguir isto ao executar um script uma vez. Utilize o seguinte comando:

`. ~/tfEnv.sh`

O script anterior utiliza o mecanismo de [início de sessão interativo do AZ CLI v 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) para autenticar com o Azure e atribuir a permissão de contribuidor de Identidade Gerida em máquinas virtuais em toda a subscrição. 

 A VM tem um back-end de estado remoto do Terraform. Para o ativar na sua implementação do Terraform, copie o ficheiro remoteState.tf do diretório tfTemplate para a raiz dos scripts do Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Para mais informações sobre a Gestão de Estado Remoto, veja [esta página sobre o estado remoto do Terraform](https://www.terraform.io/docs/state/remote.html). A chave de acesso ao armazenamento está exposta neste ficheiro e tem de ser excluída antes de consolidar os ficheiros de configuração do Terraform no controlo de origem.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a configurar uma máquina virtual Terraform do Linux no Azure. Aqui estão alguns recursos adicionais para o ajudar a saber mais acerca do Terraform no Azure: 

 [Hub do Terraform no Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentação do fornecedor do Azure do Terraform](https://aka.ms/terraform)  
 [Origem do fornecedor do Azure do Terraform](https://aka.ms/tfgit)  
 [Módulos do Azure do Terraform](https://aka.ms/tfmodules)
 

















