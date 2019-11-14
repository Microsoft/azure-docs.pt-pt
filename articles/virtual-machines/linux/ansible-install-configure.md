---
title: Início rápido-instalar o Ansible em máquinas virtuais do Linux no Azure
description: Neste guia de início rápido, saiba como instalar e configurar o Ansible para gerenciar recursos do Azure no Ubuntu, CentOS e SLES
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a1ea5814ffd201456a2751b37b77f3062cac789a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037063"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Início rápido: instalar o Ansible em máquinas virtuais do Linux no Azure

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra como configurar o Ansible para alguns dos distribuições do Linux mais comuns. Para instalar o Ansible em outros distribuições, ajuste os pacotes instalados para sua plataforma específica. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Aceder ao Linux ou a uma máquina virtual do Linux** - se não tiver uma máquina virtual do Linux, crie uma [máquina virtual do Linux](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalar o Ansible numa máquina virtual do Linux para o Azure

Inicie sessão na máquina Linux e selecione uma das distribuições seguintes para obter os passos de instalação do Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Nesta seção, você configurará o CentOS para usar o Ansible.

1. Abra uma janela de terminal.

1. Digite o seguinte comando para instalar os pacotes necessários para os módulos do SDK do Python do Azure:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Digite o seguinte comando para instalar os pacotes necessários Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Crie as credenciais do Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Nesta seção, você configurará o Ubuntu para usar o Ansible.

1. Abra uma janela de terminal.

1. Digite o seguinte comando para instalar os pacotes necessários para os módulos do SDK do Python do Azure:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Digite o seguinte comando para instalar os pacotes necessários Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Crie as credenciais do Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Nesta seção, você configurará o SLES para usar o Ansible.

1. Abra uma janela de terminal.

1. Digite o seguinte comando para instalar os pacotes necessários para os módulos do SDK do Python do Azure:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Digite o seguinte comando para instalar os pacotes necessários Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Digite o seguinte comando para remover o pacote de criptografia do Python conflitante:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Crie as credenciais do Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Criar credenciais do Azure

Para configurar as credenciais do Ansible, você precisará das seguintes informações:

* Sua ID de assinatura do Azure 
* Os valores da entidade de serviço

Se você estiver usando o Ansible Tower ou Jenkins, declare os valores da entidade de serviço como variáveis de ambiente.

Configure as credenciais do Ansible usando uma das seguintes técnicas:

- [Criar um ficheiro de credenciais do Ansible](#file-credentials)
- [Utilizar variáveis de ambiente do Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Criar um ficheiro de credenciais do Ansible

Nesta seção, você cria um arquivo de credenciais local para fornecer credenciais para Ansible. 

Para obter mais informações sobre como definir as credenciais do Ansible, consulte [fornecendo credenciais para os módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Para um ambiente de desenvolvimento, crie um arquivo chamado `credentials` na máquina virtual do host:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Insira as linhas a seguir no arquivo. Substitua os espaços reservados pelos valores da entidade de serviço.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Guarde e feche o ficheiro.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Utilizar variáveis de ambiente do Ansible

Nesta seção, você exporta os valores da entidade de serviço para configurar suas credenciais do Ansible.

1. Abra uma janela de terminal.

1. Exporte os valores da entidade de serviço:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração bem-sucedida, use Ansible para criar um grupo de recursos do Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Início rápido: configurar uma máquina virtual do Linux no Azure usando o Ansible](./ansible-create-vm.md)