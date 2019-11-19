---
title: Tutorial – configurar o emparelhamento de rede virtual do Azure usando o Ansible
description: Saiba como usar o Ansible para conectar redes virtuais com o emparelhamento de rede virtual.
keywords: Ansible, Azure, DevOps, Bash, manual, rede, emparelhamento
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155727"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Tutorial: configurar o emparelhamento de rede virtual do Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

O [emparelhamento de rede virtual (VNet)](/azure/virtual-network/virtual-network-peering-overview) permite que você conecte diretamente duas redes virtuais do Azure. Uma vez emparelhadas, as duas redes virtuais aparecem como uma para fins de conectividade. 

O tráfego é roteado entre VMs na mesma rede virtual por meio de endereços IP privados. Da mesma forma, o tráfego entre as VMs em uma rede virtual emparelhada é roteado por meio da infra-estrutura de backbone da Microsoft. Como resultado, as VMs em diferentes redes virtuais podem se comunicar entre si.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar duas redes virtuais
> * Emparelhar as duas redes virtuais
> * Excluir o emparelhamento entre as duas redes

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Criar dois grupos de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O código do guia estratégico de exemplo nesta seção é usado para:

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

O código do guia estratégico de exemplo nesta seção é usado para:

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

O código do guia estratégico de exemplo nesta seção é usado para:

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

## <a name="peer-the-two-virtual-networks"></a>Emparelhar as duas redes virtuais

O código do guia estratégico de exemplo nesta seção é usado para:

- Inicializar o emparelhamento de rede virtual
- Emparelhar duas redes virtuais criadas anteriormente

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

## <a name="delete-the-virtual-network-peering"></a>Excluir o emparelhamento de rede virtual

O código do guia estratégico de exemplo nesta seção é usado para:

- Excluir o emparelhamento entre as duas redes virtuais criadas anteriormente

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Obter o guia estratégico de exemplo

Há duas maneiras de obter o guia estratégico de exemplo completo:

- [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) e salve-o em `vnet_peering.yml`.
- Crie um novo arquivo chamado `vnet_peering.yml` e copie-o para ele no seguinte conteúdo:

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

## <a name="run-the-sample-playbook"></a>Executar o guia estratégico de exemplo

O código do guia estratégico de exemplo nesta seção é usado para testar vários recursos mostrados em todo este tutorial.

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Na seção `vars`, substitua o espaço reservado `{{ resource_group_name }}` pelo nome do seu grupo de recursos.

Execute o guia estratégico usando o comando Ansible-manual:

```bash
ansible-playbook vnet_peering.yml
```

Depois de executar o guia estratégico, você verá uma saída semelhante aos seguintes resultados:

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

Quando não for mais necessário, exclua os recursos criados neste artigo. 

O código do guia estratégico de exemplo nesta seção é usado para:

- Excluir os dois grupos de recursos criados anteriormente

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

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Substitua o espaço reservado `{{ resource_group_name-1 }}` pelo nome do primeiro grupo de recursos criado.
- Substitua o espaço reservado `{{ resource_group_name-2 }}` pelo nome do segundo grupo de recursos criado.
- Todos os recursos dentro dos dois grupos de recursos especificados serão excluídos.

Execute o guia estratégico usando o comando Ansible-manual:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)