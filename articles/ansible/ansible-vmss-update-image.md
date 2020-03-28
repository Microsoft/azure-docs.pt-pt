---
title: Tutorial - Atualizar a imagem personalizada dos conjuntos de escala de máquinas virtuais Azure usando Ansible
description: Saiba como usar o Ansible para atualizar conjuntos de escala de máquinas virtuais em Azure com imagem personalizada
keywords: ansible, azure, devops, bash, manual de procedimentos, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b7d3053c09d2dcb667a4fc407035f4814f786932
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155841"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Tutorial: Atualizar a imagem personalizada dos conjuntos de escala de máquinas virtuais Azure usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Depois de implementado um VM, configura o VM com o software de que a sua aplicação necessita. Em vez de fazer esta tarefa de configuração para cada VM, pode criar uma imagem personalizada. Uma imagem personalizada é uma imagem instantânea de um VM existente que inclui qualquer software instalado. Quando [configurar um conjunto](./ansible-create-configure-vmss.md)de escala, especifique a imagem a utilizar para os VMs desse conjunto de escala. Ao utilizar uma imagem personalizada, cada instância VM está configurada de forma idêntica para a sua aplicação. Por vezes, pode ter de atualizar a imagem personalizada do seu conjunto de escala. Essa tarefa é o foco deste tutorial.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configure dois VMs com HTTPD
> * Criar uma imagem personalizada a partir de um VM existente
> * Criar um conjunto de escala a partir de uma imagem
> * Atualizar a imagem personalizada

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Configure dois VMs

O código de jogadas nesta secção cria duas máquinas virtuais com HTTPD instalada em ambas. 

A `index.html` página de cada VM apresenta uma cadeia de teste:

* O primeiro VM mostra o valor`Image A`
* Segundo VM mostra o valor`Image B`

Esta cadeia destina-se a imitar a configuração de cada VM com um software diferente.

Há duas maneiras de obter o livro de jogadas da amostra:

* [Descarregue o](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) livro `create_vms.yml`de jogadas e guarde-o para .
* Crie um `create_vms.yml` novo ficheiro nomeado e copie-o nos seguintes conteúdos:

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

Execute o `ansible-playbook` manual utilizando `myrg` o comando, substituindo o nome do grupo de recursos:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Devido às `debug` secções do livro `ansible-playbook` de jogadas, o comando irá imprimir o endereço IP de cada VM. Copie estes endereços IP para posterior utilização.

![Endereços IP da máquina virtual](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Ligue-se aos dois VMs

Nesta secção, ligue-se a cada VM. Como mencionado na secção anterior, `Image A` `Image B` as cordas e imitar ter dois VMs distintos com configurações diferentes.

Utilizando os endereços IP da secção anterior, ligue-se a ambos os VMs:

![Screenshot da máquina virtual A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Screenshot da máquina virtual B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Criar imagens de cada VM

Neste ponto, tem dois VMs com configurações `index.html` ligeiramente diferentes (os seus ficheiros).

O código de jogadas nesta secção cria uma imagem personalizada para cada VM:

* `image_vmforimageA`- Imagem personalizada criada para `Image A` o VM que exibe na sua página inicial.
* `image_vmforimageB`- Imagem personalizada criada para `Image B` o VM que exibe na sua página inicial.

Há duas maneiras de obter o livro de jogadas da amostra:

* [Descarregue o](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) livro `capture-images.yml`de jogadas e guarde-o para .
* Crie um `capture-images.yml` novo ficheiro nomeado e copie-o nos seguintes conteúdos:

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

Execute o `ansible-playbook` manual utilizando `myrg` o comando, substituindo o nome do grupo de recursos:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Criar conjunto de escala usando a imagem A

Nesta secção, é utilizado um livro de jogadas para configurar os seguintes recursos Azure:

* Endereço IP público
* Load balancer
* Conjunto de escala que referências`image_vmforimageA`

Há duas maneiras de obter o livro de jogadas da amostra:

* [Descarregue o](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) livro `create-vmss.yml`de jogadas e guarde-o para .
* Criar um novo `create-vmss.yml` ficheiro nomeado e copiar nele o seguinte conteúdo:"

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

Execute o `ansible-playbook` manual utilizando `myrg` o comando, substituindo o nome do grupo de recursos:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Devido à `debug` secção do livro `ansible-playbook` de jogadas, o comando irá imprimir o endereço IP do conjunto de escala. Copie este endereço IP para posterior utilização.

![Endereço IP Público](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Ligar ao conjunto de escala

Nesta secção, ligue-se ao conjunto de escala. 

Utilizando o endereço IP da secção anterior, ligue-se ao conjunto de escala.

Como mencionado na secção anterior, `Image A` `Image B` as cordas e imitar ter dois VMs distintos com configurações diferentes.

O conjunto de escala refere `image_vmforimageA`a imagem personalizada chamada . A `image_vmforimageA` imagem personalizada foi criada a `Image A`partir do VM cuja página inicial exibe.

Como resultado, você vê uma `Image A`página inicial exibindo:

![O conjunto de escala está associado ao primeiro VM.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Deixe a janela do navegador aberta à medida que continua para a próxima secção.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Alterar imagem personalizada em conjunto de escala e atualizar instâncias

O código de jogadanesta secção altera a `image_vmforimageA` imagem `image_vmforimageB`do conjunto de escala - de . Além disso, todas as máquinas virtuais atuais implantadas pelo conjunto de escala são atualizadas.

Há duas maneiras de obter o livro de jogadas da amostra:

* [Descarregue o](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) livro `update-vmss-image.yml`de jogadas e guarde-o para .
* Crie um `update-vmss-image.yml` novo ficheiro nomeado e copie-o nos seguintes conteúdos:

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

Execute o `ansible-playbook` manual utilizando `myrg` o comando, substituindo o nome do grupo de recursos:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Volte ao navegador e refresque a página. 

Vê que a imagem personalizada subjacente da máquina virtual está atualizada.

![O conjunto de escala está associado ao segundo VM](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo. 

Guarde o `cleanup.yml`seguinte código como:

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible)