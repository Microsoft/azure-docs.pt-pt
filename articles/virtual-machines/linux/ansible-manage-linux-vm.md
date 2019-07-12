---
title: Início rápido - gerir máquinas virtuais do Linux no Azure com o Ansible | Documentos da Microsoft
description: Neste guia de introdução, saiba como gerir uma máquina virtual do Linux no Azure com o Ansible
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: c4878902425a26086ad77647ea06568f2110ccfe
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668626"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Início rápido: Gerir máquinas virtuais do Linux no Azure com o Ansible

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Neste artigo, vai utilizar playbooks do Ansible para iniciar e parar uma máquina virtual Linux. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Parar uma máquina virtual

Nesta secção, vai utilizar o Ansible para desalocar a máquina de virtual (parar) do Azure.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um ficheiro denominado `azure-vm-stop.yml`e abra-o no editor:

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. Cole o seguinte código de exemplo no editor:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Substitua a `{{ resource_group_name }}` e `{{ vm_name }}` marcadores de posição pelos seus valores.

1. Guarde o ficheiro e saia do editor.

1. Executar o playbook com o `ansible-playbook` comando:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Iniciar uma máquina virtual

Nesta secção, vai utilizar o Ansible para iniciar uma máquina de virtual do Azure de (parada) desalocada.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um ficheiro denominado `azure-vm-start.yml`e abra-o no editor:

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. Cole o seguinte código de exemplo no editor:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Substitua a `{{ resource_group_name }}` e `{{ vm_name }}` marcadores de posição pelos seus valores.

1. Guarde o ficheiro e saia do editor.

1. Executar o playbook com o `ansible-playbook` comando:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Tutorial: Gerir os inventários dinâmicos do Azure com o Ansible](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)