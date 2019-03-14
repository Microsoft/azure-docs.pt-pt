---
title: Criar, alterar ou eliminar uma tabela de rota do Azure com o Ansible
description: Saiba como utilizar o Ansible para criar, alterar ou eliminar uma tabela de rotas com o Ansible
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, funcionamento em rede, rota, tabela de rotas
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 025a8182d32a7d0d00a48795c848d356eb1c3d4e
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792451"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Criar, alterar ou eliminar uma tabela de rota do Azure com o Ansible
O Azure automaticamente encaminha o tráfego entre sub-redes do Azure, redes virtuais e redes no local. Se pretender alterar qualquer um dos encaminhamento predefinido no Azure, fazê-lo através da criação de um [tabela de rotas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra-lhe como criar, alterar ou eliminar uma tabela de rota do Azure e anexar a tabela de rotas a uma sub-rede também. 

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 é necessário para executar os seguinte playbooks de exemplo neste tutorial.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas
Esta secção apresenta um playbook de Ansible de exemplo que cria uma tabela de rotas. Existe um limite para quantas tabelas de rotas que pode criar por subscrição e localização do Azure. Para obter mais detalhes, veja [Limites do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

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

Guardar este manual de comunicação social como `route_table_create.yml`. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede
Uma sub-rede pode ter zero ou uma tabela de rotas associada a ele. Uma tabela de rotas pode ser associada a zero ou várias sub-redes. Uma vez que as tabelas de rotas não foram associadas redes virtuais, tem de associar uma tabela de rotas para cada sub-rede que pretende que a tabela de rotas associada. Sai da sub-rede todo o tráfego é encaminhado com base nas rotas que criou em tabelas de rota [predefinido rotas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default), e as rotas são propagadas de uma rede no local, se a rede virtual está ligada a um (de gateway de rede virtual do Azure ExpressRoute ou VPN, se utilizar o BGP com um gateway de VPN). Só pode associar uma tabela de rotas às sub-redes nas redes virtuais existentes na mesma localização do Azure e subscrição como a tabela de rotas.

Esta secção apresenta um playbook de Ansible de exemplo que cria uma rede virtual e uma sub-rede, em seguida, associa uma tabela de rotas à sub-rede.

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

Guardar este manual de comunicação social como `route_table_associate.yml`. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar a uma tabela de rotas de sub-rede
Quando desassociar a uma tabela de rotas de sub-rede, apenas tem de definir o `route_table` numa sub-rede para `None`. Segue-se um manual de comunicação do ansible de exemplo. 

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

Guardar este manual de comunicação social como `route_table_dissociate.yml`. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Criar uma rota
Esta secção apresenta um playbook de Ansible de exemplo que cria uma rota na tabela de rotas. Ele define `virtual_network_gateway` como `next_hop_type` e `10.1.0.0/16` como `address_prefix`. O prefixo não pode ser duplicado em mais de uma rota na tabela de rotas, embora o prefixo pode ser dentro de outro prefixo. Para saber mais sobre como o Azure seleciona rotas e uma descrição detalhada de todos os tipos de salto seguintes, veja [descrição geral do encaminhamento](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

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
Guardar este manual de comunicação social como `route_create.yml`. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Eliminar uma rota
Esta secção apresenta um playbook de Ansible de exemplo elimina uma rota de uma tabela de rotas.

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

Guardar este manual de comunicação social como `route_delete.yml`. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Obtenha informações de uma tabela de rotas
Pode ver os detalhes de um route_table por meio de com o nome de módulo de Ansible `azure_rm_routetable_facts`. O módulo de fatos retornará as informações da tabela de rotas com todas as rotas ligadas ao mesmo.
Segue-se um manual de comunicação do ansible de exemplo. 

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

Guardar este manual de comunicação social como `route_table_facts.yml`. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Eliminar uma tabela de rotas
Se uma tabela de rotas associada a quaisquer sub-redes, não pode ser eliminada. [Desassociar](#dissociate-a-route-table-from-a-subnet) uma tabela de rotas de todas as sub-redes antes de tentar eliminá-lo.

É possível eliminar a tabela de rotas, juntamente com todas as rotas. Segue-se um manual de comunicação do ansible de exemplo. 

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

Guardar este manual de comunicação social como `route_table_delete.yml`. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)
