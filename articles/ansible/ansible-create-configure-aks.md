---
title: Tutorial - configurar clusters do Azure Kubernetes Service (AKS) no Azure com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para criar e gerir um cluster do Azure Kubernetes Service no Azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, contentores, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1467afce60038e086daace72947c1ab21569865a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231321"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Tutorial: Configurar clusters do Azure Kubernetes Service (AKS) no Azure com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS pode ser configurado para utilizar [do Azure Active Directory (AD)](/azure/active-directory/) para autenticação de utilizador. Uma vez configurado, utilize o token de autenticação do Azure AD para iniciar sessão no cluster do AKS. O RBAC pode basear-se na identidade de um utilizador ou associação de grupo do diretório.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configurar um cluster do AKS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Criar um cluster do AKS gerido

O playbook de exemplo cria um grupo de recursos e um cluster do AKS no grupo de recursos.

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

Antes de executar o playbook, consulte as seguintes notas de:

- A primeira seção `tasks` define um grupo de recursos com o nome `myResourceGroup` dentro do `eastus` localização.
- A segunda secção dentro `tasks` define um cluster do AKS com o nome `myAKSCluster` dentro do `myResourceGroup` grupo de recursos.
- Para o `your_ssh_key` marcador de posição, introduza a chave pública RSA no formato de linha única - começando por "ssh-rsa" (sem as aspas).

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook azure_create_aks.yml
```

Executar o playbook mostra resultados semelhantes à saída seguinte:

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

O manual de procedimentos de exemplo na secção anterior define dois nós. Ajustar o número de nós, modificando o `count` valor no `agent_pool_profiles` bloco.

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

Antes de executar o playbook, consulte as seguintes notas de:

- Para o `your_ssh_key` marcador de posição, introduza a chave pública RSA no formato de linha única - começando por "ssh-rsa" (sem as aspas).

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook azure_configure_aks.yml
```

Executar o playbook mostra resultados semelhantes à saída seguinte:

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

O playbook de exemplo elimina um cluster do AKS.

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook azure_delete_aks.yml
```

Executar o playbook mostra resultados semelhantes à saída seguinte:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Dimensionar a aplicação no Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)