---
title: Tutorial - atualizar a imagem personalizada de dimensionamento de máquina virtual do Azure define a utilização do Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para atualizar os conjuntos de dimensionamento de máquinas virtuais no Azure com uma imagem personalizada
keywords: ansible, azure, devops, bash, manual de procedimentos, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 8ef0c9c86562519fc3d41a72c022bf2531aa9451
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767244"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Tutorial: Os conjuntos de atualização da imagem personalizada de dimensionamento de máquina virtual do Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Depois de implementar uma VM, configurar a VM com o software de necessidades da sua aplicação. Em vez de fazer esta tarefa de configuração para cada VM, pode criar uma imagem personalizada. Uma imagem personalizada é um instantâneo de uma VM existente, que inclui qualquer software instalado. Quando [configurar um conjunto de dimensionamento](./ansible-create-configure-vmss.md), especificar a imagem a utilizar para de VMs esse conjunto de dimensionamento. Ao utilizar uma imagem personalizada, cada instância de VM homônimo está configurada para a sua aplicação. Às vezes, terá de atualizar a imagem personalizada do seu conjunto de dimensionamento. Essa tarefa é o foco deste tutorial.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurar duas VMs com HTTPD
> * Criar uma imagem personalizada a partir de uma VM existente
> * Criar um conjunto a partir de uma imagem de dimensionamento
> * Atualizar a imagem personalizada

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Configurar duas VMs

O código do playbook nesta secção cria duas máquinas virtuais com HTTPD instalado em ambos. 

O `index.html` página para cada VM apresenta uma cadeia de caracteres de teste:

* Primeira VM apresenta o valor `Image A`
* Segunda VM apresenta o valor `Image B`

Essa cadeia de caracteres deve imitar a configuração de cada VM com software diferentes.

Existem duas formas de obter o playbook de exemplo:

* [Transferir o manual de comunicação social](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) e guardá-lo para `create_vms.yml`.
* Crie um novo ficheiro designado `create_vms.yml` e copie no seguinte conteúdo:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Executar o playbook com o `ansible-playbook` comando, substituindo `myrg` com o nome do grupo de recursos:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Devido a `debug` secções do playbook, o `ansible-playbook` comando imprimirá o endereço IP de cada VM. Copie estes endereços IP para utilização posterior.

![Endereços IP da máquina virtual](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Ligar as duas VMs

Nesta secção, vai ligar a cada VM. Conforme mencionado na secção anterior, as cadeias de caracteres `Image A` e `Image B` imitar ter duas VMs distintas com configurações diferentes.

Ao utilizar os endereços IP da seção anterior, ligue para ambas as VMs:

![Captura de ecrã da máquina virtual a](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Captura de ecrã da máquina virtual B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Criar imagens a partir de cada VM

Neste ponto, tem duas VMs com configurações ligeiramente diferentes (seus `index.html` ficheiros).

O código do playbook nesta secção cria uma imagem personalizada para cada VM:

* `image_vmforimageA` -Imagem personalizada criada para a VM que apresenta `Image A` na sua home page.
* `image_vmforimageB` -Imagem personalizada criada para a VM que apresenta `Image B` na sua home page.

Existem duas formas de obter o playbook de exemplo:

* [Transferir o manual de comunicação social](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) e guardá-lo para `capture-images.yml`.
* Crie um novo ficheiro designado `capture-images.yml` e copie no seguinte conteúdo:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Executar o playbook com o `ansible-playbook` comando, substituindo `myrg` com o nome do grupo de recursos:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Criar conjunto de dimensionamento com a imagem A

Nesta secção, um playbook é utilizado para configurar os seguintes recursos do Azure:

* Endereço IP público
* Load balancer
* Conjunto de dimensionamento que faça referência `image_vmforimageA`

Existem duas formas de obter o playbook de exemplo:

* [Transferir o manual de comunicação social](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) e guardá-lo para `create-vmss.yml`.
* Crie um novo ficheiro designado `create-vmss.yml` e copie no seguinte conteúdo: "

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Executar o playbook com o `ansible-playbook` comando, substituindo `myrg` com o nome do grupo de recursos:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Devido a `debug` secção do playbook, o `ansible-playbook` comando imprimirá o endereço IP do conjunto de dimensionamento. Copie este endereço IP para utilização posterior.

![Endereço IP Público](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Ligar ao conjunto de dimensionamento

Nesta secção, vai ligar para o conjunto de dimensionamento. 

Utilizar o endereço IP da seção anterior, ligue para o conjunto de dimensionamento.

Conforme mencionado na secção anterior, as cadeias de caracteres `Image A` e `Image B` imitar ter duas VMs distintas com configurações diferentes.

O conjunto de dimensionamento de referências a imagem personalizada com o nome `image_vmforimageA`. Imagem personalizada `image_vmforimageA` foi criada a partir da VM cuja home page do apresenta `Image A`.

Como resultado, verá uma home page, exibindo `Image A`:

![O conjunto de dimensionamento está associado a primeira VM.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Deixe a janela do browser aberta enquanto continua a secção seguinte.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Imagem personalizada de alteração no dimensionamento definir e atualizar as instâncias

O código do playbook nesta seção imagem do conjunto de dimensionamento - é alterado de `image_vmforimageA` para `image_vmforimageB`. Além disso, todas as máquinas virtuais atuais implementadas pelo conjunto de dimensionamento são atualizadas.

Existem duas formas de obter o playbook de exemplo:

* [Transferir o manual de comunicação social](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) e guardá-lo para `update-vmss-image.yml`.
* Crie um novo ficheiro designado `update-vmss-image.yml` e copie no seguinte conteúdo:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Executar o playbook com o `ansible-playbook` comando, substituindo `myrg` com o nome do grupo de recursos:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Regresse ao navegador e atualize a página. 

Verá que a imagem personalizada de subjacente da máquina virtual está atualizada.

![O conjunto de dimensionamento está associado com a segunda VM](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

Guarde o código a seguir como `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible)