---
title: Utilizar o Ansible para criar uma máquina virtual do Linux no Azure
description: Aprenda a utilizar o Ansible para criar uma máquina virtual do Linux no Azure
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, máquina virtual
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: 40631d706f287f9a4b0475a21e25ef65b15b31b5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790939"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>Utilizar o Ansible para criar uma máquina virtual do Linux no Azure
Ao utilizar uma linguagem declarativa, o Ansible permite-lhe automatizar a criação, a configuração e a implementação de recursos do Azure através dos *manuais de procedimentos* do Ansible. Cada secção deste artigo mostra o aspeto de cada secção de um manual de procedimentos do Ansible, de modo a criar e configurar diferentes aspetos de uma máquina virtual do Linux. O [manual de procedimentos completo do Ansible](#complete-sample-ansible-playbook) é apresentado no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
O Ansible precisa de um grupo de recursos no qual os seus recursos vão ser implementados. A seguinte secção de manual de procedimentos de exemplo do Ansible cria um grupo de recursos denominado `myResourceGroup` na localização `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Quando cria uma máquina virtual do Azure, tem de criar uma [rede virtual](/azure/virtual-network/virtual-networks-overview) ou de utilizar uma já existente. Também tem de decidir como pretende que se faça o acesso às máquinas virtuais na rede virtual. A seguinte secção de manual de procedimentos de exemplo do Ansible cria uma rede virtual denominada `myVnet` no espaço de endereços `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Todos os recursos do Azure implementados numa rede virtual são implementados numa [sub-rede](/azure/virtual-network/virtual-network-manage-subnet) dentro dessa rede virtual. 

A seguinte secção de manual de procedimentos de exemplo do Ansible cria uma sub-rede denominada `mySubnet` na rede virtual `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público
[Os endereços IP públicos](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Os endereços IP públicos também permitem que os recursos do Azure comuniquem com a Internet à saída, bem como com serviços do Azure destinados ao público com um endereço IP atribuído ao recurso. O endereço está dedicado ao recurso, até que a atribuição do mesmo seja anulada por si. Se um recurso não tiver um endereço IP público atribuído, continua a poder comunicar com a Internet à saída, mas o Azure atribui de forma dinâmica um endereço IP disponível que não se encontre dedicado ao recurso. 

A seguinte secção de manual de procedimentos de exemplo do Ansible cria um endereço IP público denominado `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Pode filtrar o tráfego de rede de e para os recursos do Azure numa rede virtual do Azure com um [grupo de segurança de rede](/azure/virtual-network/security-overview). Os grupos de segurança de rede contêm regras de segurança que permitem ou negam o tráfego de entrada ou de saída de e para vários tipos de recursos do Azure. 

A seguinte secção de manual de procedimentos de exemplo do Ansible cria um grupo de segurança de rede denominado `myNetworkSecurityGroup` e define uma regra para permitir o tráfego SSH na porta TCP 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-a-virtual-network-interface-card"></a>Criar uma interface de rede virtual
As interfaces de rede virtual ligam a sua máquina virtual a uma rede virtual, a um endereço IP público e a um grupo de segurança de rede específico. 

A seguinte secção numa secção do manual de procedimentos de exemplo do Ansible cria uma interface de rede virtual denominada `myNIC` e ligada aos recurso da rede virtual que criou:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
O passo final é criar uma máquina virtual que utilize todos os recursos que criou nas secções anteriores deste artigo. 

A secção de manual de procedimentos de exemplo do Ansible apresentada nesta secção cria uma máquina virtual denominada `myVM` e liga a interface de rede virtual com o nome `myNIC`. Substitua o marcador de posição &lt;your-key-data> pelos dados da sua chave pública.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Manual de procedimentos completo do Ansible de exemplo.

Esta secção apresenta o manual de procedimentos completo de exemplo do Ansible que criou ao longo deste artigo. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Executar o manual de procedimentos de executar do Ansible

Esta secção mostra-lhe como executar o manual de procedimentos de exemplo do Ansible apresentado neste artigo.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um ficheiro (para conter o manual de procedimentos) denominado `azure_create_complete_vm.yml` e abra-o no editor VI, da seguinte forma:

  ```azurecli-interactive
  vi azure_create_complete_vm.yml
  ```

1. Selecione a tecla **I** para entrar no modo de inserção.

1. Cole o [manual de procedimentos completo de exemplo do Ansible](#complete-sample-ansible-playbook) no editor.

1. Saia do modo de inserção ao clicar na tecla **Esc**.

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

1. Execute o manual de procedimentos de exemplo do Ansible.

  ```bash
  ansible-playbook azure_create_complete_vm.yml
  ```

1. O resultado é semelhante ao seguinte, em que pode ver que foi criada com êxito uma máquina virtual.

  ```bash
  PLAY [Create Azure VM] ****************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Create resource group] *********************************************
  changed: [localhost]

  TASK [Create virtual network] *********************************************
  changed: [localhost]

  TASK [Add subnet] *********************************************************
  changed: [localhost]

  TASK [Create public IP address] *******************************************
  changed: [localhost]

  TASK [Dump public IP for VM which will be created] ********************************************************************
  ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
  }

  TASK [Create Network Security Group that allows SSH] **********************
  changed: [localhost]

  TASK [Create virtual network inteface card] *******************************
  changed: [localhost]

  TASK [Create VM] **********************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0
  ```

1. O comando SSH é utilizado para aceder à sua VM do Linux. Substitua o marcador de posição &lt;ip-address> pelo endereço IP do passo anterior.

  ```bash
  ssh azureuser@<ip-address>
  ```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Use Ansible to manage a Linux virtual machine in Azure](./ansible-manage-linux-vm.md) (Utilizar o Ansible para gerir uma máquina virtual do Linux no Azure)
