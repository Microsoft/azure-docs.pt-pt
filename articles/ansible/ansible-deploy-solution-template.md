---
title: Implementar o modelo de solução do Ansible para o Azure para CentOS
description: Saiba como implementar o modelo de solução do Ansible numa máquina virtual CentOS alojada no Azure, juntamente com ferramentas configuradas para trabalhar com o Azure.
ms.service: azure
keywords: ansible, azure, devops, modelo de solução, máquina virtual, identidades geridas para recursos do azure, centos, red hat
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 01/28/2019
ms.openlocfilehash: 6dfc1c0f9dcf29d9cf24df19fbd043c71852aeac
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790964"
---
# <a name="deploy-the-ansible-solution-template-for-azure-to-centos"></a>Implementar o modelo de solução do Ansible para o Azure para CentOS
O modelo de solução do Ansible para o Azure foi concebido para configurar uma instância do Ansible numa máquina virtual CentOS, juntamente com o Ansible e um conjunto de ferramentas configurado para trabalhar com o Azure. Estas ferramentas incluem:

- **Ansible módulos do Azure** – a [Ansible módulos para o Azure](./ansible-matrix.md) são um conjunto de módulos que permitem-lhe criar e gerir a sua infraestrutura no Azure. A versão mais recente destes módulos é implementada por predefinição. No entanto, durante o processo de implementação do modelo de solução, pode especificar um número de versão é apropriado para seu ambiente.
- **Interface de linha de comandos (CLI) 2.0 do Azure** – a [CLI 2.0 do Azure](/cli/azure/?view=azure-cli-latest) é uma experiência de linha de comandos para várias plataformas para o gerenciamento de recursos do Azure. 
- **gerido identidades para recursos do Azure** – a [geridos identidades para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) funcionalidade resolve o problema de manter cloud credenciais de aplicativo seguro.

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="deploy-the-ansible-solution-template-from-the-azure-marketplace"></a>Implementar o modelo de solução do Ansible no Azure Marketplace

1. Navegue para o [modelo de solução do Ansible no Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Selecione **obter agora**.

1. É apresentada uma janela que detalha os termos de utilização, a política de privacidade e a utilização de termos do Azure Marketplace. Selecione **Continuar**.

1. O portal do Azure aparece e apresenta a página de Ansible que descreve o modelo de solução. Selecione **Criar**.

1. Na **Ansible criar** página, verá várias guias. Sobre o **Noções básicas** separador, introduza as informações necessárias:

    - **Nome** -especifique o nome da sua instância do Ansible. Para fins de demonstração, o nome `ansiblehost` é utilizado.
    - **Nome de utilizador:** -especifique o nome de utilizador que terão acesso à instância do Ansible. Para fins de demonstração, o nome `ansibleuser` é utilizado.
    - **Tipo de autenticação:** -selecione **palavra-passe** ou **chave pública SSH**. Para fins de demonstração **chave pública SSH** está selecionada.
    - **Palavra-passe** e **Confirmar palavra-passe** - se de que seleciona **palavra-passe** para **tipo de autenticação**, introduza a palavra-passe para esses valores.
    - **Chave pública SSH** - se de que seleciona **chave pública SSH** para **tipo de autenticação**, introduza a chave pública RSA no formato de linha única - começando com `ssh-rsa`.
    - **Subscrição** -selecione a sua subscrição do Azure na lista pendente.
    - **Grupo de recursos** - selecione um grupo de recursos existente na lista pendente ou selecione **criar novo** e especifique um nome para um novo grupo de recursos. Para fins de demonstração, um novo grupo de recursos com o nome `ansiblerg` é utilizado.
    - **Localização** -selecione a localização na lista pendente que é adequada para o seu cenário.

    ![Separador de portal do Azure para as definições básicas do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Selecione **OK**.

1. Na **definições adicionais** separador, introduza as informações necessárias:

    - **Tamanho** -padrões de portais do Azure para um tamanho padrão. Para especificar um tamanho diferente, que permite a seu cenário específico, selecione a seta para apresentar uma lista de tamanhos diferentes.
    - **Tipo de disco VM** -selecione **SSD** (Solid-State unidade Premium) ou **HDD** (unidade de disco rígido). Para fins de demonstração **SSD** está selecionada para seus benefícios de desempenho. Para obter mais informações sobre cada um desses tipos de armazenamento em disco, consulte os artigos seguintes:
        - [Armazenamento Premium e discos geridos de elevado desempenho para VMs](/azure/virtual-machines/windows/premium-storage)
        - [SSD Managed Disks Standard para cargas de trabalho de Máquina Virtual do Azure](/azure/virtual-machines/windows/disks-standard-ssd)
    - **Endereço IP público** -especificar esta definição se pretender comunicar com a máquina virtual a partir de fora da máquina virtual. A predefinição é um novo endereço IP público com o nome `ansible-pip`. Para especificar um endereço IP diferente, selecione a seta para Especifica os atributos - como nome, SKU e a atribuição, desse endereço IP. 
    - **Etiqueta de nome de domínio** -introduza o nome de domínio destinado ao público da máquina virtual. O nome tem de ser exclusivos e cumprir requisitos de nomenclatura. Para obter mais informações sobre como especificar um nome para a máquina virtual, consulte [convenções de nomenclatura para recursos do Azure](/azure/architecture/best-practices/naming-conventions).
    - **Versão do Ansible** -Especifique um número de versão ou o valor `latest` para implementar a versão mais recente. Selecione o ícone de informações junto a **versão do Ansible** para obter mais informações sobre as versões disponíveis.

    ![Separador de portal do Azure para as definições adicionais do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Selecione **OK**.

1. Na **definições de integração do Ansible** separador, especifique o tipo de autenticação. Para obter mais informações sobre como proteger recursos do Azure, consulte [o que há de identidades geridas para recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Separador de portal do Azure para as definições de integração do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Selecione **OK**.

1. O **resumo** página apresenta que mostra o processo de validação e listando os critérios especificados para a implementação do Ansible. Um link na parte inferior do separador permite-lhe **transferir o modelo e parâmetros** para utilização com plataformas e idiomas com suporte do Azure. 

    ![Separador de portal do Azure para o separador Resumo do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Selecione **OK**.

1. Quando o **Create** separador for apresentada, selecione **OK** para implementar o Ansible.

1. Selecione o **notificações** na parte superior da página do portal para monitorizar a implementação do Ansible. Depois da implementação estiver concluída, selecione **vá para o grupo de recursos**. 

    ![Separador de portal do Azure para o separador Resumo do Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na página do grupo de recursos, obtenha o endereço IP do anfitrião do Ansible e inicie sessão para gerir os recursos do Azure com o Ansible.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Utilizar o Ansible para criar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/ansible-create-vm)
