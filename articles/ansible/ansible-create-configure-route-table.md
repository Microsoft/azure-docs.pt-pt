---
title: Tutorial - configurar tabelas de rota do Azure com o Ansible | Documentos da Microsoft
description: Saiba como criar, alterar e eliminar tabelas de rota do Azure com o Ansible
keywords: ansible, azure, devops, bash, playbook, funcionamento em rede, rota, tabela de rotas
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3d20a7bb98ba266850baa0512f5b767f8b649767
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707873"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Tutorial: Configurar tabelas de rota do Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

O Azure automaticamente encaminha o tráfego entre sub-redes do Azure, redes virtuais e redes no local. Se precisar de mais controlo sobre o encaminhamento de seu ambiente, pode criar uma [tabela de rotas](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Criar uma tabela de rotas criar uma rede virtual e uma sub-rede associar uma tabela de rotas uma sub-rede Disassociate uma tabela de rotas de sub-rede criar e eliminam encaminha uma tabela de rotas eliminar uma tabela de rotas de consulta

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

O código do playbook nesta secção cria uma tabela de rotas. Para obter informações sobre os limites de tabela de rotas, consulte [limites do Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

O código do playbook nesta secção:

* Cria uma rede virtual
* Cria uma sub-rede dentro da rede virtual
* Associa uma tabela de rotas à sub-rede

Tabelas de rotas não estão associadas às redes virtuais. Em vez disso, a tabelas de rotas estão associadas com a sub-rede de uma rede virtual.

A tabela virtual de rede e a rota tem coexistir na mesma localização do Azure e subscrição.

Sub-redes e tabelas de rotas têm uma relação um-para-muitos. Uma sub-rede pode ser definida com nenhuma tabela de rotas associada ou tabela de uma rota. Tabelas de rotas podem ser associadas a nenhum, uma ou várias sub-redes. 

O tráfego da sub-rede é encaminhado com base em:

- rotas definidas dentro de tabelas de rotas
- [Rotas predefinidas](/azure/virtual-network/virtual-networks-udr-overview#default)
- rotas propagar-se de uma rede no local

A rede virtual tem de estar ligada a um gateway de rede virtual do Azure. O gateway pode ser VPN ou ExpressRoute, se utilizar o BGP com um gateway de VPN.

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar a uma tabela de rotas de sub-rede

O código do playbook nesta secção dissociates uma tabela de rotas de sub-rede.

Quando a desassociar a uma tabela de rotas de sub-rede, definir o `route_table` para a sub-rede para `None`. 

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Criar uma rota

O código do playbook nesta secção uma rota dentro de uma tabela de rotas. 

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

Antes de executar o playbook, consulte as seguintes notas de:

* `virtual_network_gateway` é definido como `next_hop_type`. Para obter mais informações sobre como o Azure seleciona as rotas, consulte [descrição geral do encaminhamento](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` é definido como `10.1.0.0/16`. O prefixo não pode ser duplicado na tabela de rotas.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Eliminar uma rota

O código do playbook nesta secção elimina uma rota de uma tabela de rotas.

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Obter informações da tabela de rota

O código de playbook nesta secção utiliza o módulo do Ansible `azure_rm_routetable_facts` para obter informações da tabela de rota.

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Eliminar uma tabela de rotas

O código do playbook nesta secção uma tabela de rotas.

Quando uma tabela de rotas é eliminada, todas suas rotas também são eliminadas.

Não é possível eliminar uma tabela de rotas, se ele está associada uma sub-rede. [Desassociar a tabela de rotas de quaisquer sub-redes](#dissociate-a-route-table-from-a-subnet) antes de tentar eliminar a tabela de rotas. 

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)