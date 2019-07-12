---
title: Guia de introdução – instalação Ansible em máquinas virtuais do Linux no Azure | Documentos da Microsoft
description: Neste guia de introdução, saiba como instalar e configurar o Ansible para gerir recursos do Azure no Ubuntu, CentOS e SLES
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3302d999dd70a83be18ce610b9c3d44992c865c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671846"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Início rápido: Instalar o Ansible em máquinas virtuais do Linux no Azure

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra como configurar o Ansible para algumas das distribuições de Linux mais comuns. Para instalar o Ansible em outras distribuições, ajuste os pacotes instalados para a sua plataforma específica. 

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

Nesta secção, vai configurar CentOS para utilizar o Ansible.

1. Abra uma janela de terminal.

1. Introduza o seguinte comando para instalar os pacotes necessários para os módulos do Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Introduza o seguinte comando para instalar os pacotes necessários Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Criar as credenciais do Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Nesta secção, vai configurar Ubuntu para utilizar o Ansible.

1. Abra uma janela de terminal.

1. Introduza o seguinte comando para instalar os pacotes necessários para os módulos do Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Introduza o seguinte comando para instalar os pacotes necessários Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Criar as credenciais do Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Nesta secção, vai configurar SLES para utilizar o Ansible.

1. Abra uma janela de terminal.

1. Introduza o seguinte comando para instalar os pacotes necessários para os módulos do Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Introduza o seguinte comando para instalar os pacotes necessários Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Introduza o seguinte comando para remover o pacote de criptografia de Python em conflito:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Criar as credenciais do Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Criar credenciais do Azure

Para configurar as credenciais do Ansible, terá das seguintes informações:

* O ID de subscrição do Azure 
* Os valores de principal de serviço

Se estiver a utilizar o Ansible Tower ou o Jenkins, declare os valores de principal de serviço como variáveis de ambiente.

Configure as credenciais do Ansible através de uma das seguintes técnicas:

- [Criar um ficheiro de credenciais do Ansible](#file-credentials)
- [Utilizar variáveis de ambiente do Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Criar um ficheiro de credenciais do Ansible

Nesta secção, vai criar um ficheiro de credenciais locais para fornecer credenciais para o Ansible. 

Para obter mais informações sobre como definir as credenciais do Ansible, consulte [fornecer credenciais para módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Para um ambiente de desenvolvimento, crie um ficheiro denominado `credentials` da máquina virtual do anfitrião:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Inserir as linhas seguintes no ficheiro. Substitua os marcadores de posição com os valores de principal de serviço.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Guarde e feche o ficheiro.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Utilizar variáveis de ambiente do Ansible

Nesta secção, vai exportar os valores de principal de serviço para configurar as suas credenciais do Ansible.

1. Abra uma janela de terminal.

1. Exporte os valores de principal de serviço:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração com êxito, utilize o Ansible para criar um grupo de recursos do Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configurar uma máquina virtual Linux no Azure com o Ansible](./ansible-create-vm.md)