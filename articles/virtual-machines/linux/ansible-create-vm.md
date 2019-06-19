---
title: Início rápido - configurar máquinas virtuais do Linux no Azure com o Ansible | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar uma máquina virtual Linux no Azure com o Ansible
keywords: ansible, azure, devops, máquina virtual
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ff0d3508f1d418a189fab0dfe5803280a20f9a00
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190129"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Início rápido: Configurar máquinas virtuais do Linux no Azure com o Ansible

Ao utilizar uma linguagem declarativa, o Ansible permite-lhe automatizar a criação, a configuração e a implementação de recursos do Azure através dos *manuais de procedimentos* do Ansible. Este artigo apresenta um playbook de Ansible de exemplo para configurar máquinas virtuais do Linux. O [manual de procedimentos completo do Ansible](#complete-sample-ansible-playbook) é apresentado no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

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





[Os endereços IP públicos](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Endereços IP públicos também permitem que os recursos do Azure comunicar com os serviços do Azure destinados ao público saída. Em ambos os cenários, um endereço IP atribuído ao recurso que está sendo acessado. O endereço está dedicado ao recurso, até que a atribuição. Se um endereço IP público não está atribuído a um recurso, o recurso continua a pode comunicar com a Internet à saída. A conexão é feita pelo Azure atribuir dinamicamente um endereço IP disponível. O endereço atribuído de forma dinâmica não é dedicado ao recurso.

A seguinte secção de manual de procedimentos de exemplo do Ansible cria um endereço IP público denominado `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

[Grupos de segurança de rede](/azure/virtual-network/security-overview) filtrar o tráfego de rede entre recursos do Azure numa rede virtual. Regras de segurança são definidas que regem o tráfego de entrada e saída de e para recursos do Azure. Para obter mais informações sobre recursos do Azure e grupos de segurança de rede, consulte [integração da rede Virtual para serviços do Azure](/azure/virtual-network/virtual-network-for-azure-services)

O playbook seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`. O grupo de segurança de rede inclui uma regra que permita o tráfego SSH na porta TCP 22.

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

A seção a seguir numa seção de manual de comunicação do Ansible de exemplo cria uma placa de interface de rede virtual com o nome `myNIC` ligado para os recursos de rede virtual que criou:

```yaml
- name: Create virtual network interface card
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
  - name: Create virtual network interface card
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

   TASK [Create virtual network interface card] *******************************
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
> [Quickstart: Gerir uma máquina virtual do Linux no Azure com o Ansible](./ansible-manage-linux-vm.md)
