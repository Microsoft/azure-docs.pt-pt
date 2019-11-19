---
title: Tutorial – configurar tabelas de rotas do Azure usando o Ansible
description: Saiba como criar, gerenciar e excluir tabelas de rotas do Azure usando o Ansible. Saiba também como criar e excluir rotas.
keywords: Ansible, Azure, DevOps, Bash, manual, rede, rota, tabela de rotas
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d1e44a98405bc1009f6f3d56d90fc1fd655d77d5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156501"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Tutorial: configurar tabelas de rotas do Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

O Azure roteia automaticamente o tráfego entre as sub-redes do Azure, as redes virtuais e as redes locais. Se precisar de mais controle sobre o roteamento do seu ambiente, você poderá criar uma [tabela de rotas](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Criar uma tabela de rotas criar uma rede virtual e uma sub-rede associar uma tabela de rotas a uma sub-rede desassociar uma tabela de rotas de uma sub-rede criar e excluir rotas consulta uma tabela de rotas excluir uma tabela de rotas

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

O código do guia estratégico nesta seção cria uma tabela de rotas. Para obter informações sobre limites de tabela de rotas, consulte [limites do Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Guarde o manual de procedimentos seguinte como `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

O código do guia estratégico nesta seção:

* Cria uma rede virtual
* Cria uma sub-rede dentro da rede virtual
* Associa uma tabela de rotas à sub-rede

As tabelas de rotas não estão associadas a redes virtuais. Em vez disso, as tabelas de rotas são associadas à sub-rede de uma rede virtual.

A rede virtual e a tabela de rotas devem coexistir no mesmo local e assinatura do Azure.

As sub-redes e as tabelas de rotas têm uma relação um-para-muitos. Uma sub-rede pode ser definida sem uma tabela de rotas associada ou uma tabela de rotas. As tabelas de rotas podem ser associadas a nenhuma, uma ou várias sub-redes. 

O tráfego da sub-rede é roteado com base em:

- rotas definidas nas tabelas de rotas
- [rotas padrão](/azure/virtual-network/virtual-networks-udr-overview#default)
- rotas propagadas de uma rede local

A rede virtual deve estar conectada a um gateway de rede virtual do Azure. O gateway pode ser ExpressRoute ou VPN se estiver usando BGP com um gateway de VPN.

Guarde o manual de procedimentos seguinte como `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissociar uma tabela de rotas de uma sub-rede

O código do guia estratégico nesta seção dissocia uma tabela de rotas de uma sub-rede.

Ao dissociar uma tabela de rotas de uma sub-rede, defina o `route_table` para a sub-rede a `None`. 

Guarde o manual de procedimentos seguinte como `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Criar uma rota

O código do guia estratégico nesta seção é uma rota dentro de uma tabela de rotas. 

Guarde o manual de procedimentos seguinte como `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Antes de executar o guia estratégico, consulte as seguintes observações:

* `virtual_network_gateway` é definido como `next_hop_type`. Para obter mais informações sobre como o Azure seleciona rotas, consulte [visão geral de roteamento](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` é definido como `10.1.0.0/16`. O prefixo não pode ser duplicado na tabela de rotas.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Excluir uma rota

O código do guia estratégico nesta seção exclui uma rota de uma tabela de rotas.

Guarde o manual de procedimentos seguinte como `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Obter informações da tabela de rotas

O código do guia estratégico nesta seção usa o módulo Ansible `azure_rm_routetable_facts` para recuperar informações da tabela de rotas.

Guarde o manual de procedimentos seguinte como `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

O código do guia estratégico nesta seção é uma tabela de rotas.

Quando uma tabela de rotas é excluída, todas as suas rotas também são excluídas.

Uma tabela de rotas não poderá ser excluída se estiver associada a uma sub-rede. [Desassocie a tabela de rotas de quaisquer sub-redes](#dissociate-a-route-table-from-a-subnet) antes de tentar excluir a tabela de rotas. 

Guarde o manual de procedimentos seguinte como `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)