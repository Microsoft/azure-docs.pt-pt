---
title: Início rápido - executar playbooks do Ansible através de Bash no Azure Cloud Shell | Documentos da Microsoft
description: Neste guia de introdução, saiba como executar várias tarefas do Ansible com o Bash no Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: a0d0544601bfc6ce0c7ef642f576ed780c8642ba
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720211"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Início rápido: Executar playbooks do Ansible através de Bash no Azure Cloud Shell

O Azure Cloud Shell é um shell interativo, acessível para o browser para o gerenciamento de recursos do Azure. Cloud Shell disponibilizar permite a utilização de linha de comandos de Bash ou do Powershell. Neste artigo, vai utilizar Bash no Azure Cloud Shell para executar playbooks do Ansible.

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurar o Azure Cloud Shell** – se estiver familiarizado com o Azure Cloud Shell, consulte [início rápido para o Bash no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

Quando iniciar sessão no Cloud Shell, Ansible autentica com o Azure para gerir a infraestrutura sem qualquer configuração adicional. 

Ao trabalhar com várias subscrições, especifique a subscrição utiliza o Ansible exportando o `AZURE_SUBSCRIPTION_ID` variável de ambiente. 

Para listar todas as suas subscrições do Azure, execute o seguinte comando:

```azurecli-interactive
az account list
```

Usando o seu ID de subscrição do Azure, defina o `AZURE_SUBSCRIPTION_ID` da seguinte forma:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração com êxito, utilize o Ansible para criar um grupo de recursos do Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configurar a máquina virtual no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm)