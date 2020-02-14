---
title: Quickstart - Implemente o modelo de solução Ansible para Azure para o CentOS
description: Neste arranque rápido, aprenda a implementar o modelo de solução Ansible numa máquina virtual CentOS alojada no Azure, juntamente com ferramentas configuradas para trabalhar com o Azure.
keywords: ansível, azul, devops, modelo de solução, máquina virtual, identidades geridas para recursos azuis, centos, chapéu vermelho
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: cc3a6c1f0ef36b51b62e6aa58f317aee13149589
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193791"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Quickstart: Implemente o modelo de solução Ansible para Azure para o CentOS

O modelo de solução Ansible para o Azure foi concebido para configurar uma instância Ansible numa máquina virtual CentOS juntamente com o Ansible e um conjunto de ferramentas configuradas para trabalhar com o Azure. Estas ferramentas incluem:

- **Módulos ansíveis para o Azure** - Os [módulos Ansible para o Azure](./ansible-matrix.md) são um conjunto de módulos que lhe permitem criar e gerir a sua infraestrutura no Azure. A versão mais recente destes módulos é implementada por padrão. No entanto, durante o processo de implementação do modelo de solução, pode especificar um número de versão adequado para o seu ambiente.
- Interface de **Linha de Comando Azure (CLI) 2.0** - O [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) é uma experiência de linha de comando transversal para gerir os recursos do Azure. 
- **identidades geridas para os recursos Azure** - As [identidades geridas para recursos Azure](/azure/active-directory/managed-identities-azure-resources/overview) abordam a questão de manter as credenciais de aplicação em nuvem seguras.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Implementar o modelo de solução Ansível

1. Navegue no modelo de [solução Ansible no Mercado Azure.](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview)

1. Selecione **OBTÊ-lo agora**.

1. Uma janela aparece que detalha os Termos de Uso, Política de Privacidade e Utilização de Termos do Mercado Azure. Selecione **Continuar**.

1. O portal Azure aparece e exibe a página Ansible que descreve o modelo de solução. Selecione **Criar**.

1. Na página **Create Ansible,** você vê vários separadores. No separador Basics, introduza as informações **necessárias:**

   - **Nome** - Especifique o nome da sua instância Ansible. Para fins de demonstração, o nome `ansiblehost` é usado.
   - Nome do **utilizador:** - Especifique o nome de utilizador que terá acesso à instância Ansible. Para fins de demonstração, o nome `ansibleuser` é usado.
   - **Tipo de autenticação:** - Selecione **palavra-passe** ou **chave pública SSH**. Para fins de demonstração, a **chave pública SSH** é selecionada.
   - **Palavra-passe** e **confirme a palavra-passe** - Se selecionar **palavra-passe** para **tipo de autenticação,** introduza a sua palavra-passe para estes valores.
   - **Chave pública SSH** - Se selecionar **a chave pública SSH** para o tipo de **autenticação,** introduza a sua chave pública RSA no formato de linha única - a começar por `ssh-rsa`.
   - **Subscrição** - Selecione a subscrição do Azure na lista de dropdown.
   - **Grupo de recursos** - Selecione um grupo de recursos existente da lista de dropdown, ou selecione **Criar novo** e especificar um nome para um novo grupo de recursos. Para fins de demonstração, é utilizado um novo grupo de recursos chamado `ansiblerg`.
   - **Localização** - Selecione a localização da lista de dropdown que é apropriada para o seu cenário.

     ![Separador de portal Azure para configurações básicas ansíveis](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Selecione **OK**.

1. No separador **Definições Adicionais,** introduza as informações necessárias:

   - **Tamanho** - O portal Azure falha num tamanho padrão. Para especificar um tamanho diferente que acomoda o seu cenário específico, selecione a seta para apresentar uma lista de diferentes tamanhos.
   - Tipo de **disco VM** - Selecione **SSD** (Unidade de Estado Sólido Premium) ou **HDD** (Disco Rígido). Para fins de demonstração, **o SSD** é selecionado para os seus benefícios de desempenho. Para obter mais informações sobre cada tipo de armazenamento em disco, consulte os seguintes artigos:
       - [Armazenamento Premium e discos geridos de elevado desempenho para VMs](/azure/virtual-machines/windows/premium-storage)
       - [Discos geridos standard SSD para cargas de trabalho de máquinas azure virtual](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Endereço IP público** - Especifique esta definição se pretender comunicar com a máquina virtual de fora da máquina virtual. O predefinido é um novo endereço IP público que tem o nome `ansible-pip`. Para especificar um endereço IP diferente, selecione a seta especificar os atributos - como nome, SKU e Atribuição, desse endereço IP. 
   - **Etiqueta de nome** de domínio - Introduza o nome de domínio virado para o público da máquina virtual. O nome deve ser único e satisfazer os requisitos de nomeação. Para obter mais informações sobre a especificação de um nome para a máquina virtual, consulte [as convenções de nomeação para os recursos do Azure](/azure/architecture/best-practices/resource-naming).
   - **Versão ansível** - Especifique um número de versão ou o valor `latest` para implementar a versão mais recente. Selecione o ícone de informação ao lado da **versão Ansible** para ver mais informações sobre as versões disponíveis.

     ![Separador de portal Azure para configurações adicionais ansíveis](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Selecione **OK**.

1. No separador Definições de **Integração Ansível,** especifique o tipo de autenticação. Para obter mais informações sobre a segurança dos recursos do Azure, veja [o que é gerida identidades para os recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview)

    ![Separador de portal Azure para configurações de integração ansível](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Selecione **OK**.

1. A página **resumo** apresenta mostrando o processo de validação e listando os critérios especificados para a implementação ansível. Um link na parte inferior do separador permite-lhe **descarregar o modelo e os parâmetros** para utilização com linguagens e plataformas Azure suportadas. 

     ![Separador de portal Azure para separador resumo ansível](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Selecione **OK**.

1. Quando aparecer o separador **Criar,** selecione **OK** para implementar O Ansible.

1. Selecione o ícone **Notificações** na parte superior da página do portal para acompanhar a implementação ansível. Uma vez concluída a implementação, selecione **Ir para o grupo de recursos**. 

     ![Separador de portal Azure para separador resumo ansível](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na página do grupo de recursos, obtenha o endereço IP do seu anfitrião Ansible e inscreva-se para gerir os seus recursos Azure usando ansible.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configure uma máquina virtual Linux em Azure usando Ansible](./ansible-create-vm.md)