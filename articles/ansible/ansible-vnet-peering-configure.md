---
title: Tutorial - configurar o peering de rede virtual do Azure com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para ligar redes virtuais com o peering de rede virtual.
keywords: ansible, do azure, devops, bash, playbook, redes, peering
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: d3d1aab17a94e928d113246f280c3391cae85655
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767090"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Tutorial: Configure o peering de rede virtual do Azure com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Peering de rede virtual (VNet)](/azure/virtual-network/virtual-network-peering-overview) permite que se conecte duas redes virtuais do Azure. Uma vez executado o peering, as duas redes virtuais aparecem como uma única para fins de conectividade. 

O tráfego é encaminhado entre VMs na mesma rede virtual através de endereços IP privados. Da mesma forma, o tráfego entre VMs numa rede virtual em modo de peering é encaminhado através da infraestrutura principal da Microsoft. Como resultado, as VMs nas redes virtuais em diferentes podem comunicar entre si.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar duas redes virtuais
> * Configurar o peering entre duas redes virtuais
> * Eliminar o peering entre duas redes

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Criar dois grupos de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O código do playbook de exemplo nesta secção é utilizado para:

- Criar dois grupos de recursos 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Criar a primeira rede virtual

O código do playbook de exemplo nesta secção é utilizado para:

- Criar uma rede virtual
- Criar uma sub-rede dentro da rede virtual

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Criar a segunda rede virtual

O código do playbook de exemplo nesta secção é utilizado para:

- Criar uma rede virtual
- Criar uma sub-rede dentro da rede virtual

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Configurar o peering entre duas redes virtuais

O código do playbook de exemplo nesta secção é utilizado para:

- Inicializar o peering de rede virtual
- Configurar o peering entre duas redes virtuais que criou anteriormente

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Eliminar o peering de rede virtual

O código do playbook de exemplo nesta secção é utilizado para:

- Eliminar o peering entre duas redes virtuais que criou anteriormente

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Obter o playbook de exemplo

Existem duas formas de obter o playbook de exemplo completo:

- [Transferir o manual de comunicação social](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) e guardá-lo para `vnet_peering.yml`.
- Crie um novo ficheiro designado `vnet_peering.yml` e copie no seguinte conteúdo:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Executar o playbook de exemplo

O código do playbook de exemplo nesta secção é utilizado para testar várias funcionalidades mostradas neste tutorial.

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Na `vars` secção, substitua o `{{ resource_group_name }}` marcador de posição pelo nome do seu grupo de recursos.

Execute o playbook com o comando do ansible playbook:

```bash
ansible-playbook vnet_peering.yml
```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

O código do playbook de exemplo nesta secção é utilizado para:

- Eliminar os grupos de dois recursos criados anteriormente

Guarde o manual de procedimentos seguinte como `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Substitua o `{{ resource_group_name-1 }}` marcador de posição pelo nome do primeiro grupo de recursos criado.
- Substitua o `{{ resource_group_name-2 }}` marcador de posição pelo nome do segundo grupo de recursos criado.
- Todos os recursos dentro de dois grupos de recursos especificado serão eliminados.

Execute o playbook com o comando do ansible playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)