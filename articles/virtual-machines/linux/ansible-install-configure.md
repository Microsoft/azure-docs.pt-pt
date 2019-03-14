---
title: Instalar o Ansible em máquinas virtuais do Azure
description: Saiba como instalar e configurar o Ansible para gerir os recursos do Azure no Ubuntu, CentOS e SLES
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 38a1ffdc815b357f7bb7ebe2c337b55a738fb6b5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790437"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Instalar o Ansible em máquinas virtuais do Azure

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Pode utilizar o Ansible para gerir as suas máquinas virtuais (VMs) no Azure, tal como faria em qualquer outro recurso. Este artigo fornece detalhes sobre como instalar o Ansible e os módulos do SDK Python do Azure necessários para algumas das distribuições de Linux mais comuns. Pode instalar o Ansible noutras distribuições ao ajustar os pacotes instalados de acordo com a sua plataforma específica. Para criar recursos do Azure de forma segura, também aprenderá a criar e definir credenciais para o Ansible utilizar. Para obter uma lista de ferramentas adicionais disponíveis no Cloud Shell, veja [Funcionalidades e ferramentas para o Bash no Azure Cloud Shell](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Aceder ao Linux ou a uma máquina virtual do Linux** - se não tiver uma máquina virtual do Linux, crie uma [máquina virtual do Linux](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Principal de serviço do Azure**: Siga as instruções na secção do **criar o principal de serviço** secção no artigo [criar principal de serviço de Azure CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Tome nota dos valores para **appId**, **displayName**, **password** e **tenant**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalar o Ansible numa máquina virtual do Linux para o Azure

Inicie sessão na máquina Linux e selecione uma das distribuições seguintes para obter os passos de instalação do Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Instale os pacotes necessários para os módulos do SDK Python do Azure e o Ansible ao introduzir os comandos seguintes numa janela de terminal ou do Bash:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Siga as instruções descritas na secção [Criar credenciais do Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Instale os pacotes necessários para os módulos do SDK Python do Azure e o Ansible ao introduzir os comandos seguintes numa janela de terminal ou do Bash:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Siga as instruções descritas na secção [Criar credenciais do Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Instale os pacotes necessários para os módulos do SDK Python do Azure e o Ansible ao introduzir os comandos seguintes numa janela de terminal ou do Bash:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Siga as instruções descritas na secção [Criar credenciais do Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Criar credenciais do Azure

A combinação do ID da subscrição e das informações devolvidas da criação do principal de serviço é utilizada para configurar as credenciais do Ansible numa de duas formas:

- [Criar um ficheiro de credenciais do Ansible](#file-credentials)
- [Utilizar variáveis de ambiente do Ansible](#env-credentials)

Se quiser utilizar ferramentas como o Ansible Tower ou o Jenkins, terá de utilizar a opção de declarar os valores do principal de serviço como variáveis de ambiente.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>Criar um ficheiro de credenciais do Ansible

Esta secção explica como criar um ficheiro de credenciais local para fornecer credenciais ao Ansible. Para obter mais informações sobre como definir as credenciais do Ansible, veja [Fornecer Credenciais aos Módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Para um ambiente de desenvolvimento, crie um ficheiro de *credenciais* para o Ansible na máquina virtual do anfitrião da seguinte forma:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Insira as linhas seguintes no ficheiro de *credenciais* e substitua os marcadores de posição pelas informações da criação do principal de serviço.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Guarde e feche o ficheiro.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Utilizar variáveis de ambiente do Ansible

Esta secção explica como configurar as credenciais do Ansible ao exportá-las como variáveis de ambiente.

Numa janela de terminal ou do Bash, execute os seguintes comandos:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração com êxito, pode agora utilizar o Ansible para criar um grupo de recursos.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Utilizar o Ansible para criar uma máquina virtual do Linux no Azure](./ansible-create-vm.md)
