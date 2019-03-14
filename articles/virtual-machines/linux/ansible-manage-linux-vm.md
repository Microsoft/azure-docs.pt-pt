---
title: Utilizar o Ansible para gerir uma máquina virtual do Linux no Azure
description: Aprenda a utilizar o Ansible para gerir uma máquina virtual do Linux no Azure
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: 8f97cf8a4231e9a2144f27c0540de96574e13795
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789882"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Utilizar o Ansible para gerir uma máquina virtual do Linux no Azure
O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Pode utilizar o Ansible para gerir máquinas virtuais do Azure, tal como faz em qualquer outro recurso. Este artigo mostra-lhe como utilizar o manual de procedimentos do Ansible para iniciar e parar uma máquina virtual do Linux. 

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Utilizar o Ansible para desalocar (parar) uma máquina virtual do Azure
Esta secção ilustra como utilizar o Ansible para desalocar (parar) uma máquina virtual do Azure

1.  Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Abra o [Cloud Shell](/azure/cloud-shell/overview).

1.  Crie um ficheiro (para conter o manual de procedimentos) denominado `azure-vm-stop.yml` e abra-o no editor VI, da seguinte forma:

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  Selecione a tecla **I** para entrar no modo de inserção.

1.  Cole o seguinte código de exemplo no editor:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  Saia do modo de inserção ao clicar na tecla **Esc**.

1.  Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

1.  Execute o manual de procedimentos de exemplo do Ansible.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  O resultado é semelhante ao exemplo seguinte que mostra que a máquina virtual foi desalocada com êxito (parada):

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Utilize o Ansible para iniciar uma máquina virtual do Azure desalocada (parada)
Esta secção ilustra como utilizar o Ansible para iniciar uma máquina virtual do Azure desalocada (parada)

1.  Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Abra o [Cloud Shell](/azure/cloud-shell/overview).

1.  Crie um ficheiro (para conter o manual de procedimentos) denominado `azure-vm-start.yml` e abra-o no editor VI, da seguinte forma:

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  Selecione a tecla **I** para entrar no modo de inserção.

1.  Cole o seguinte código de exemplo no editor:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  Saia do modo de inserção ao clicar na tecla **Esc**.

1.  Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

1.  Execute o manual de procedimentos de exemplo do Ansible.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  O resultado será semelhante ao exemplo seguinte que mostra que a máquina virtual foi iniciada com êxito:

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
> [Utilizar o Ansible para gerir os seus inventários dinâmicos do Azure](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)