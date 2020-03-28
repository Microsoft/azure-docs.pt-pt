---
title: Tutorial - Configure tabelas de rotas azure usando Ansible
description: Aprenda a criar, gerir e eliminar tabelas de rotas Azure usando O Ansible. Também aprenda a criar e apagar rotas.
keywords: ansível, azul, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659802"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Tutorial: Configure tabelas de rotas azure usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

O Azure encaminha automaticamente o tráfego entre as redes Azure, redes virtuais e no local. Se precisar de mais controlo sobre o encaminhamento do seu ambiente, pode criar uma mesa de [rota.](/azure/virtual-network/virtual-networks-udr-overview) 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Criar uma tabela de rotas Crie uma rede virtual e subnet A Associate uma tabela de rotas com uma subnet Desassociate uma tabela de rotas a partir de uma subnet Criar e excluir rotas Consultar uma tabela de rotas Delete a route table

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

O código de jogadas nesta secção cria uma tabela de rotas. Para obter informações sobre os limites da tabela de rotas, consulte [os limites do Azure.](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits) 

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

O código de jogadas nesta secção:

* Cria uma rede virtual
* Cria uma sub-rede dentro da rede virtual
* Associa uma tabela de rota à subneta

As tabelas de rotas não estão associadas a redes virtuais. Pelo contrário, as tabelas de rotas estão associadas à subneta de uma rede virtual.

A rede virtual e a tabela de rotas devem coexistir na mesma localização e subscrição do Azure.

Subredes e mesas de rota têm uma relação de um a muitos. Uma sub-rede pode ser definida sem tabela de rota associada ou uma tabela de rota. As tabelas de rotas podem ser associadas a nenhuma, uma ou muitas subredes. 

O tráfego da sub-rede é encaminhado com base em:

- rotas definidas dentro de tabelas de rotas
- [rotas padrão](/azure/virtual-network/virtual-networks-udr-overview#default)
- rotas propagadas a partir de uma rede no local

A rede virtual deve ser ligada a um portal de rede virtual Azure. O gateway pode ser ExpressRoute, ou VPN se utilizar BGP com um gateway VPN.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissociar uma mesa de rota de uma subneta

O código de jogadanesta secção dissocia uma tabela de rota de uma subnet.

Ao dissociar uma tabela de rota `route_table` de uma sub-rede, desloque a sub-rede para `None`. 

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Criar uma rota

O código de jogadas nesta secção é uma rota dentro de uma mesa de rota. 

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

Antes de executar o livro de jogadas, consulte as seguintes notas:

* `virtual_network_gateway`é definido `next_hop_type`como . Para mais informações sobre como o Azure seleciona rotas, consulte a [visão geral do Routing](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix`é definido `10.1.0.0/16`como . O prefixo não pode ser duplicado dentro da tabela de rotas.

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Apagar uma rota

O código de jogadas nesta secção elimina uma rota de uma tabela de rotas.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Obtenha informações sobre tabela de rotas

O código de jogadas nesta secção `azure_rm_routetable_facts` utiliza o módulo Ansible para recuperar informações da tabela de rotas.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Eliminar uma tabela de rotas

O código de jogadas nesta secção uma mesa de rota.

Quando uma tabela de rotas é eliminada, todas as suas rotas também são eliminadas.

Uma mesa de rota não pode ser apagada se estiver associada a uma sub-rede. [Dissociar a tabela de rotas de quaisquer subredes](#dissociate-a-route-table-from-a-subnet) antes de tentar apagar a tabela de rotas. 

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)