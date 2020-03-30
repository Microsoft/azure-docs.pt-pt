---
title: Quickstart - Gerir máquinas virtuais Linux em Azure usando Ansible
description: Neste arranque rápido, aprenda a gerir uma máquina virtual Linux em Azure usando Ansible
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239539"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Quickstart: Gerir máquinas virtuais Linux em Azure usando Ansible

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Neste artigo, você usa um livro de ansible para iniciar e parar uma máquina virtual Linux. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Parar uma máquina virtual

Nesta secção, utiliza-se Ansible para desalocar (parar) uma máquina virtual Azure.

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um `azure-vm-stop.yml`ficheiro chamado , e abra-o no editor:

    ```bash
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

1. Substitua `{{ resource_group_name }}` `{{ vm_name }}` os espaços e os espaços reservados com os seus valores.

1. Guarde o ficheiro e saia do editor.

1. Executar o manual `ansible-playbook` usando o comando:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Depois de executar o livro de jogadas, vê a saída semelhante aos seguintes resultados:

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

Nesta secção, utiliza o Ansible para iniciar uma máquina virtual Azure localizada (parada).

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um `azure-vm-start.yml`ficheiro chamado , e abra-o no editor:

    ```bash
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

1. Substitua `{{ resource_group_name }}` `{{ vm_name }}` os espaços e os espaços reservados com os seus valores.

1. Guarde o ficheiro e saia do editor.

1. Executar o manual `ansible-playbook` usando o comando:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Depois de executar o livro de jogadas, vê a saída semelhante aos seguintes resultados:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Tutorial: Gerir inventários dinâmicos azure usando Ansible](./ansible-manage-azure-dynamic-inventories.md)