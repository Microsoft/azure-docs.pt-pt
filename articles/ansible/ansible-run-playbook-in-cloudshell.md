---
title: Guia de início rápido-executar guias estratégicos do Ansible via bash no Azure Cloud Shell
description: Neste guia de início rápido, saiba como realizar várias tarefas de Ansible com bash no Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: d04708be82a704c2ce20a928380fca1d325493da
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155968"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Início rápido: executar guias estratégicos do Ansible via bash no Azure Cloud Shell

Azure Cloud Shell é um shell interativo acessível por navegador para gerenciar recursos do Azure. O Cloud Shell fornece permite que você use uma linha de comando bash ou PowerShell. Neste artigo, você usa o bash dentro de Azure Cloud Shell para executar um guia estratégico de Ansible.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurar Azure cloud Shell** -se você for novo no Azure cloud Shell, consulte [início rápido para bash em Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

Quando conectado à Cloud Shell, o Ansible é autenticado com o Azure para gerenciar a infraestrutura sem nenhuma configuração adicional. 

Ao trabalhar com várias assinaturas, especifique a assinatura que o Ansible usa exportando a variável de ambiente `AZURE_SUBSCRIPTION_ID`. 

Para listar todas as suas assinaturas do Azure, execute o seguinte comando:

```azurecli-interactive
az account list
```

Usando sua ID de assinatura do Azure, defina o `AZURE_SUBSCRIPTION_ID` da seguinte maneira:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração bem-sucedida, use Ansible para criar um grupo de recursos do Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Início rápido: configurar a máquina virtual no Azure usando o Ansible](/azure/virtual-machines/linux/ansible-create-vm)