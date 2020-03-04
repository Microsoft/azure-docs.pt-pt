---
title: Quickstart - Executar playbooks ansible via Bash in Azure Cloud Shell
description: Neste arranque rápido, aprenda a realizar várias tarefas ansíveis com Bash em Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247883"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Quickstart: Executar playbooks ansible via Bash in Azure Cloud Shell

A Azure Cloud Shell é uma concha interativa e acessível ao navegador para gerir os recursos do Azure. A Cloud Shell fornece-lhe que utilize uma linha de comando Bash ou PowerShell. Neste artigo, você usa Bash dentro de Azure Cloud Shell para executar um livro de ansible.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configure Azure Cloud Shell** - Se você é novo em Azure Cloud Shell, consulte [Quickstart para Bash em Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

Quando assinado na Cloud Shell, ansible autentica com Azure para gerir a infraestrutura sem qualquer configuração adicional. 

Ao trabalhar com várias subscrições, especifique a subscrição Utilizações Ansible exportando a variável ambiente `AZURE_SUBSCRIPTION_ID`. 

Para listar todas as suas subscrições Do Azure, execute o seguinte comando:

```azurecli-interactive
az account list
```

Utilizando o seu ID de subscrição Azure, detete a `AZURE_SUBSCRIPTION_ID` da seguinte forma:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração bem sucedida, utilize o Ansible para criar um grupo de recursos Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configure a máquina virtual em Azure usando Ansible](./ansible-create-vm.md)