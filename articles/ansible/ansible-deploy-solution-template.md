---
title: Início rápido – implantar o modelo de solução Ansible para o Azure para CentOS
description: Neste guia de início rápido, saiba como implantar o modelo de solução Ansible em uma máquina virtual CentOS hospedada no Azure, juntamente com as ferramentas configuradas para trabalhar com o Azure.
keywords: Ansible, Azure, DevOps, modelo de solução, máquina virtual, identidades gerenciadas para recursos do Azure, CentOS, Red Hat
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5ababe9eb1f680378e882970df2d0b008287a7c4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241720"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Início rápido: implantar o modelo de solução Ansible para o Azure para CentOS

O modelo de solução Ansible para o Azure foi projetado para configurar uma instância Ansible em uma máquina virtual CentOS junto com Ansible e um conjunto de ferramentas configuradas para funcionar com o Azure. Estas ferramentas incluem:

- **Módulos Ansible para Azure** -os [módulos Ansible do Azure](./ansible-matrix.md) são um conjunto de módulos que permitem criar e gerenciar sua infraestrutura no Azure. A versão mais recente desses módulos é implantada por padrão. No entanto, durante o processo de implantação de modelo de solução, você pode especificar um número de versão apropriado para seu ambiente.
- **CLI (interface de linha de comando) do Azure 2,0** -o [CLI do Azure 2,0](/cli/azure/?view=azure-cli-latest) é uma experiência de linha de comando de plataforma cruzada para gerenciar recursos do Azure. 
- **identidades gerenciadas para recursos do Azure** – o recurso [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) aborda o problema de manter as credenciais do aplicativo de nuvem seguras.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Implantar o modelo de solução Ansible

1. Navegue até o [modelo de solução Ansible no Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Selecione **obter agora**.

1. É exibida uma janela que detalha os termos de uso, a política de privacidade e o uso dos termos do Azure Marketplace. Selecione **Continuar**.

1. O portal do Azure aparece e exibe a página Ansible que descreve o modelo de solução. Selecione **Criar**.

1. Na página **criar Ansible** , você verá várias guias. Na guia **noções básicas** , insira as informações necessárias:

   - **Nome** – especifique o nome da instância de Ansible. Para fins de demonstração, o nome `ansiblehost` é usado.
   - **Nome de usuário:** -especifique o nome de usuário que terá acesso à instância de Ansible. Para fins de demonstração, o nome `ansibleuser` é usado.
   - **Tipo de autenticação:** -selecione a **senha** ou a **chave pública SSH**. Para fins de demonstração, a **chave pública SSH** é selecionada.
   - **Senha** e **Confirmar senha** – se você selecionar **senha** para o **tipo de autenticação**, insira sua senha para esses valores.
   - **Chave pública SSH** -se você selecionar **chave pública SSH** para o **tipo de autenticação**, insira sua chave pública RSA no formato de linha única-começando com `ssh-rsa`.
   - **Assinatura** – selecione sua assinatura do Azure na lista suspensa.
   - **Grupo de recursos** – selecione um grupo de recursos existente na lista suspensa ou selecione **criar novo** e especifique um nome para um novo grupo de recursos. Para fins de demonstração, um novo grupo de recursos chamado `ansiblerg` é usado.
   - **Local** -selecione o local na lista suspensa que é apropriado para seu cenário.

     ![Guia portal do Azure para configurações básicas do Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Selecione **OK**.

1. Na guia **configurações adicionais** , insira as informações necessárias:

   - **Tamanho** -o portal do Azure usa como padrão um tamanho padrão. Para especificar um tamanho diferente que acomode seu cenário específico, selecione a seta para exibir uma lista de tamanhos diferentes.
   - **Tipo de disco da VM** -selecione **SSD** (unidade de estado sólido Premium) ou **HDD** (unidade de disco rígido). Para fins de demonstração, o **SSD** é selecionado para seus benefícios de desempenho. Para obter mais informações sobre cada um desses tipos de armazenamento em disco, consulte os seguintes artigos:
       - [Armazenamento Premium e discos geridos de elevado desempenho para VMs](/azure/virtual-machines/windows/premium-storage)
       - [Managed Disks de SSD Standard para cargas de trabalho de máquina virtual do Azure](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Endereço IP público** -especifique essa configuração se desejar se comunicar com a máquina virtual de fora da máquina virtual. O padrão é um novo endereço IP público com o nome `ansible-pip`. Para especificar um endereço IP diferente, selecione a seta especificar os atributos, como nome, SKU e atribuição, desse endereço IP. 
   - **Rótulo de nome de domínio** – Insira o nome de domínio voltado para o público da máquina virtual. O nome deve ser exclusivo e atender aos requisitos de nomenclatura. Para obter mais informações sobre como especificar um nome para a máquina virtual, consulte [convenções de nomenclatura para recursos do Azure](/azure/architecture/best-practices/naming-conventions).
   - **Versão do Ansible** -especifique um número de versão ou o valor `latest` para implantar a versão mais recente. Selecione o ícone de informações ao lado de **Ansible versão** para ver mais informações sobre as versões disponíveis.

     ![Guia portal do Azure para configurações adicionais do Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Selecione **OK**.

1. Na guia **configurações de integração do Ansible** , especifique o tipo de autenticação. Para obter mais informações sobre como proteger os recursos do Azure, consulte [o que são identidades gerenciadas para recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Guia portal do Azure para as configurações de integração do Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Selecione **OK**.

1. A página **Resumo** exibe a exibição do processo de validação e a listagem dos critérios especificados para a implantação do Ansible. Um link na parte inferior da guia permite que você **Baixe o modelo e os parâmetros** para uso com plataformas e linguagens do Azure com suporte. 

     ![Guia portal do Azure para a guia Resumo de Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Selecione **OK**.

1. Quando a guia **criar** for exibida, selecione **OK** para implantar o Ansible.

1. Selecione o ícone **notificações** na parte superior da página do portal para acompanhar a implantação do Ansible. Quando a implantação for concluída, selecione **ir para o grupo de recursos**. 

     ![Guia portal do Azure para a guia Resumo de Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na página grupo de recursos, obtenha o endereço IP do host Ansible e entre para gerenciar seus recursos do Azure usando o Ansible.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Início rápido: configurar uma máquina virtual do Linux no Azure usando o Ansible](/azure/virtual-machines/linux/ansible-create-vm)