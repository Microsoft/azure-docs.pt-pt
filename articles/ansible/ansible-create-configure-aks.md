---
title: Tutorial – configurar clusters do AKS (serviço kubernetes do Azure) no Azure usando o Ansible
description: Saiba como utilizar o Ansible para criar e gerir um cluster do Azure Kubernetes Service no Azure
keywords: Ansible, Azure, DevOps, Bash, cloudshell, manual, AKs, contêiner, AKs, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 11/04/2019
ms.openlocfilehash: b0839cf418cd30f62623e046960c32d41537609a
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614383"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Tutorial: configurar clusters do AKS (serviço kubernetes do Azure) no Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS pode ser configurado para usar o [Azure Active Directory (AD)](/azure/active-directory/) para autenticação de usuário. Uma vez configurado, você usa o token de autenticação do Azure AD para entrar no cluster AKS. O RBAC pode ser baseado na identidade de um usuário ou em uma associação de grupo de diretório.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configurar um cluster AKS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Criar um cluster do AKS gerido

O guia estratégico de exemplo cria um grupo de recursos e um cluster AKS dentro do grupo de recursos.

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

Antes de executar o guia estratégico, consulte as seguintes observações:

- A primeira seção dentro de `tasks` define um grupo de recursos chamado `myResourceGroup` dentro do local `eastus`.
- A segunda seção dentro de `tasks` define um cluster AKS chamado `myAKSCluster` dentro do grupo de recursos `myResourceGroup`.
- Para o espaço reservado `your_ssh_key`, insira sua chave pública RSA no formato de linha única-começando com "ssh-RSA" (sem as aspas).
- Para o espaço reservado `aks_version`, use o comando [AZ AKs Get-Versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) .

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook azure_create_aks.yml
```

A execução do manual mostra resultados semelhantes à seguinte saída:

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

O manual de procedimentos de exemplo na secção anterior define dois nós. Você ajusta o número de nós modificando o valor de `count` no bloco de `agent_pool_profiles`.

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

Antes de executar o guia estratégico, consulte as seguintes observações:

- Para o espaço reservado `your_ssh_key`, insira sua chave pública RSA no formato de linha única-começando com "ssh-RSA" (sem as aspas).

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook azure_configure_aks.yml
```

A execução do manual mostra resultados semelhantes à seguinte saída:

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

O guia estratégico de exemplo exclui um cluster AKS.

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook azure_delete_aks.yml
```

A execução do manual mostra resultados semelhantes à seguinte saída:

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
> [Tutorial: Dimensionar uma aplicação no Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)