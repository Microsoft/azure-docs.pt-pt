---
title: Quickstart - Instale Ansible em máquinas virtuais Linux em Azure
description: Neste arranque rápido, aprenda a instalar e configurar ansible para gerir recursos Azure em Ubuntu, CentOS e SLES
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202411"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Quickstart: Instale Ansible em máquinas virtuais Linux em Azure

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra como configurar Ansible para alguns dos distros linux mais comuns. Para instalar o Ansible noutras distros, ajuste as embalagens instaladas para a sua plataforma específica. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Aceder ao Linux ou a uma máquina virtual do Linux** - se não tiver uma máquina virtual do Linux, crie uma [máquina virtual do Linux](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalar o Ansible numa máquina virtual do Linux para o Azure

Inicie sessão na máquina Linux e selecione uma das distribuições seguintes para obter os passos de instalação do Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Nesta secção, configura o CentOS para utilizar o Ansible.

1. Abra uma janela de terminal.

1. Introduza o seguinte comando para instalar as embalagens necessárias para os módulos Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Introduza o seguinte comando para instalar as embalagens necessárias Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Crie as credenciais Azure.](#create-azure-credentials)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Nesta secção, configura ubuntu para usar Ansible.

1. Abra uma janela de terminal.

1. Introduza o seguinte comando para instalar as embalagens necessárias para os módulos Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Introduza o seguinte comando para instalar as embalagens necessárias Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Crie as credenciais Azure.](#create-azure-credentials)

### <a name="sles-12-sp2"></a>SLES 12 SP2

Nesta secção, configura o SLES para utilizar o Ansible.

1. Abra uma janela de terminal.

1. Introduza o seguinte comando para instalar as embalagens necessárias para os módulos Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Introduza o seguinte comando para instalar as embalagens necessárias Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Introduza o seguinte comando para remover o pacote de criptografia Python conflituoso:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Crie as credenciais Azure.](#create-azure-credentials)

## <a name="create-azure-credentials"></a>Criar credenciais do Azure

Para configurar as credenciais Ansible, precisa das seguintes informações:

* O seu ID de subscrição Azure 
* Os principais valores do serviço

Se estiver a usar a Ansible Tower ou o Jenkins, declare os principais valores de serviço como variáveis ambientais.

Configure as credenciais Ansible utilizando uma das seguintes técnicas:

- [Criar um ficheiro de credenciais do Ansible](#file-credentials)
- [Use variáveis ambientais ansíveis](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Criar um ficheiro de credenciais do Ansible

Nesta secção, você cria um arquivo de credenciais locais para fornecer credenciais ao Ansible. 

Para obter mais informações sobre a definição de credenciais Ansible, consulte [Fornecer credenciais aos Módulos Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Para um ambiente de desenvolvimento, crie um ficheiro nomeado `credentials` na máquina virtual anfitrião:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Insira as seguintes linhas no ficheiro. Substitua os espaços reservados com os valores principais do serviço.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Guarde e feche o ficheiro.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Utilizar variáveis de ambiente do Ansible

Nesta secção, exporta os principais valores do serviço para configurar as suas credenciais Ansible.

1. Abra uma janela de terminal.

1. Exportar os valores principais do serviço:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração bem sucedida, utilize o Ansible para criar um grupo de recursos Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configure uma máquina virtual Linux em Azure usando Ansible](./ansible-create-vm.md)