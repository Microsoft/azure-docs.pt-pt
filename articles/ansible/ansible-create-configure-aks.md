---
title: Tutorial - Configure Azure Kubernetes Service (AKS) clusters em Azure usando Ansible
description: Saiba como utilizar o Ansible para criar e gerir um cluster do Azure Kubernetes Service no Azure
keywords: ansível, azul, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156679"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Tutorial: Configure Azure Kubernetes Service (AKS) clusters em Azure usando Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

O AKS pode ser configurado para utilizar o [Diretório Ativo Azure (AD)](/azure/active-directory/) para autenticação do utilizador. Uma vez configurado, utilize o seu símbolo de autenticação Azure AD para assinar no cluster AKS. O RBAC pode basear-se na identidade de um utilizador ou na adesão ao grupo de diretórios.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configure um cluster AKS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Criar um cluster do AKS gerido

O livro de jogadas de amostra cria um grupo de recursos e um cluster AKS dentro do grupo de recursos.

Guarde o manual de procedimentos seguinte como `azure_create_aks.yml`:

```yml
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
    aks_version: aks_version
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
      kubernetes_version: "{{aks_version}}"
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

Antes de executar o livro de jogadas, consulte as seguintes notas:

- A primeira `tasks` secção dentro define `myResourceGroup` um `eastus` grupo de recursos nomeado dentro do local.
- A segunda `tasks` secção dentro define um `myAKSCluster` cluster `myResourceGroup` AKS nomeado dentro do grupo de recursos.
- Para `your_ssh_key` o espaço reservado, introduza a sua chave pública RSA no formato de linha única - começando com "ssh-rsa" (sem as cotações).
- Para `aks_version` o espaço reservado, utilize o comando [az aks get-versions.](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions)

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook azure_create_aks.yml
```

Executar o livro de jogadas mostra resultados semelhantes aos seguintes resultados:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Dimensionar nós do AKS

O manual de procedimentos de exemplo na secção anterior define dois nós. Ajusta o número de nós `count` modificando `agent_pool_profiles` o valor do bloco.

Guarde o manual de procedimentos seguinte como `azure_configure_aks.yml`:

```yml
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

Antes de executar o livro de jogadas, consulte as seguintes notas:

- Para `your_ssh_key` o espaço reservado, introduza a sua chave pública RSA no formato de linha única - começando com "ssh-rsa" (sem as cotações).

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook azure_configure_aks.yml
```

Executar o livro de jogadas mostra resultados semelhantes aos seguintes resultados:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Eliminar um cluster do AKS gerido

O livro de jogadas da amostra elimina um cluster AKS.

Guarde o manual de procedimentos seguinte como `azure_delete_aks.yml`:


```yml
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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook azure_delete_aks.yml
```

Executar o livro de jogadas mostra resultados semelhantes aos seguintes resultados:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Dimensionar uma aplicação no Serviço Kubernetes do Azure (AKS)](/azure/aks/tutorial-kubernetes-scale)