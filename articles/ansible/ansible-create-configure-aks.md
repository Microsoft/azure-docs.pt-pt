---
title: Criar e configurar clusters do Azure Kubernetes Service no Azure com o Ansible
description: Saiba como utilizar o Ansible para criar e gerir um cluster do Azure Kubernetes Service no Azure
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, manual de procedimentos, aks, contentor, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: 1e56ebe4c1928b455fa5abb626f1a5001af42382
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792672"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Criar e configurar clusters do Azure Kubernetes Service no Azure com o Ansible
O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Pode utilizar o Ansible para gerir o Azure Kubernetes Service (AKS). Este artigo mostra-lhe como utilizar o Ansible para criar e configurar um cluster do Azure Kubernetes Service.

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- **Principal de serviço do Azure** - quando [criar o principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), tome nota dos seguintes valores: **appId**, **displayName**, **password** e **tenant**.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.6 é necessário para executar os manuais de procedimentos de exemplo neste tutorial.

## <a name="create-a-managed-aks-cluster"></a>Criar um cluster do AKS gerido
O código nesta secção apresenta um playbook de Ansible de exemplo para criar um grupo de recursos e um cluster do AKS que reside no grupo de recursos.

> [!Tip]
> Para o `your_ssh_key` marcador de posição, introduza a chave pública RSA no formato de linha única - começando por "ssh-rsa" (sem as aspas).

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

As marcas abaixo ajudam a explicar o código do manual de procedimentos do Ansible anterior:
- A primeira secção nas **tarefas** define um grupo de recursos com o nome **myResourceGroup** na localização **eastus**.
- A segunda secção nas **tarefas** define um cluster do AKS com o nome **myAKSCluster** no grupo de recursos **myResourceGroup**.

Para criar o cluster do AKS com o Ansible, guarde o manual de procedimentos de exemplo anterior como `azure_create_aks.yml` e execute-o com o seguinte comando:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

A saída do comando **ansible-playbook* parece semelhante ao seguinte, que mostra que o cluster do AKS foi criado com êxito:

  ```Output
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Dimensionar nós do AKS

O manual de procedimentos de exemplo na secção anterior define dois nós. Se precisar de mais ou menos cargas de trabalho do contentor no seu cluster, pode ajustar o número de nós facilmente. O manual de procedimentos de exemplo nesta secção aumenta o número de nós de dois nós para três. A modificação do número de nós é feita ao alterar o valor **count** no bloco **agent_pool_profiles**.

> [!Tip]
> Para o `your_ssh_key` marcador de posição, introduza a chave pública RSA no formato de linha única - começando por "ssh-rsa" (sem as aspas).

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Para dimensionar o cluster do Azure Kubernetes Service com o Ansible, guarde o manual de procedimentos anterior como *azure_configure_aks.yml* e execute-o da seguinte forma:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

O resultado seguinte mostra que o cluster do AKS foi criado com êxito:

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Eliminar um cluster do AKS gerido

A secção do manual de procedimentos de exemplo do Ansible seguinte ilustra como eliminar um cluster do AKS:

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name:
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Para eliminar o cluster do Azure Kubernetes Service com o Ansible, guarde o manual de procedimentos anterior como *azure_delete_aks.yml* e execute-o da seguinte forma:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

O resultado seguinte mostra que o cluster do AKS foi eliminado com êxito:
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Tutorial: Dimensionar a aplicação no Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
