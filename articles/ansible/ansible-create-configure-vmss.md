---
title: Criar conjuntos de dimensionamento de máquinas virtuais no Azure utilizando o Ansible
description: Aprenda a utilizar o Ansible para criar e configurar um conjunto de dimensionamento de máquinas virtuais no Azure
ms.service: azure
keywords: ansible, azure, devops, bash, manual de procedimentos, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: 1176987ab318a97a7db6a12e619e7b7db06ad2da
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097894"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Criar conjuntos de dimensionamento de máquinas virtuais no Azure utilizando o Ansible
O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Pode utilizar o Ansible para gerir o seu conjunto de dimensionamento de máquinas virtuais (VMSS) no Azure, tal como faria em qualquer outro recurso do Azure. Este artigo mostra-lhe como utilizar o Ansible para criar e dimensionar um conjunto de dimensionamento de máquinas virtuais. 

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.6 é necessário para executar os manuais de procedimentos de exemplo neste tutorial. 

## <a name="create-a-vmss"></a>Criar um VMSS
Esta secção apresenta um manual de procedimentos do Ansible de exemplo que define os seguintes recursos:
- **Grupo de recursos** no qual todos os seus recursos vão ser implementados
- **Rede virtual** no espaço de endereços: 10.0.0.0/16
- **Sub-rede** dentro da rede virtual
- **Endereço IP público** que lhe permite aceder aos recursos na internet
- **Grupo de segurança de rede** que controla o fluxo de tráfego de rede que entra e sai do seu conjunto de dimensionamento de máquinas virtuais
- **Balanceador de carga** que distribui o tráfego por um conjunto de VMs definidas através das regras do balanceador de carga
- **Conjunto de dimensionamento de máquinas virtuais** que utiliza todos os recursos criados

Introduza a sua própria palavra-passe para o *admin_password* valor.

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Para criar o ambiente de conjunto de dimensionamento de máquinas virtuais com o Ansible, guarde o manual de procedimentos anterior como `vmss-create.yml` ou [transfira o manual de procedimentos do Ansible de exemplo](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Depois de executar o manual de procedimentos, um resultado semelhante ao seguinte exemplo mostra que o conjunto de dimensionamento de máquinas virtuais foi criado com êxito:

  ```Output
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Aumentar horizontalmente um VMSS
O conjunto de dimensionamento de máquinas de virtuais criada tem duas instâncias. Se navegar para o conjunto de dimensionamento no portal do Azure, verá **Standard_DS1_v2 (2 instâncias)**. Também pode utilizar o [Azure Cloud Shell](https://shell.azure.com/) executando o seguinte comando no Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Verá resultados semelhantes à saída seguinte:

  ```bash
  {
    "capacity": 2,
  }
  ```

Agora, vamos dimensionar de duas instâncias para três instâncias. O seguinte código de manual de procedimentos do Ansible obtém informações sobre o dimensionamento de máquinas virtuais e altera a sua capacidade de duas a três. 

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Para aumentar horizontalmente o ambiente de conjunto de dimensionamento de máquinas virtuais que criou, guarde o manual de procedimentos anterior como `vmss-scale-out.yml` ou [transfira o manual de procedimentos do Ansible de exemplo](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

O seguinte comando irá executar o manual de procedimentos:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

O resultado da execução do manual de procedimentos do Ansible mostra que o conjunto de dimensionamento de máquinas virtuais foi dimensionado horizontalmente com êxito:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Se navegar para o conjunto de dimensionamento de máquinas virtuais que configurou no portal do Azure, verá **Standard_DS1_v2 (3 instâncias)**. Também pode verificar a alteração com o [Azure Cloud Shell](https://shell.azure.com/), executando o seguinte comando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

O resultado da execução do comando no Cloud Shell mostra que existem agora três instâncias. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Implementar aplicações em conjuntos de dimensionamento de máquina virtual com o Ansible](https://docs.microsoft.com/azure/ansible/ansible-deploy-app-vmss)
> 
> [Dimensionar automaticamente um conjunto de dimensionamento com o Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)