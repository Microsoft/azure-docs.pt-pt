---
title: Tutorial – criar uma VM ou um conjunto de dimensionamento de máquinas virtuais da Galeria de imagens compartilhadas do Azure usando o Ansible
description: Saiba como usar o Ansible para criar uma VM ou conjunto de dimensionamento de máquinas virtuais com base em uma imagem generalizada na Galeria de imagens compartilhadas.
keywords: Ansible, Azure, DevOps, Bash, manual, máquina virtual, conjunto de dimensionamento de máquinas virtuais, Galeria de imagens compartilhadas
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 814be49c972e444f2a4e4a703501e88fa1272b89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392146"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Tutorial: criar uma VM ou um conjunto de dimensionamento de máquinas virtuais da Galeria de imagens compartilhadas do Azure usando o Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

A [Galeria de imagens compartilhadas](/azure/virtual-machines/windows/shared-image-galleries) é um serviço que permite que você gerencie, Compartilhe e organize facilmente imagens gerenciadas por personalizados. Esse recurso é benéfico para cenários em que muitas imagens são mantidas e compartilhadas. Imagens personalizadas podem ser compartilhadas entre assinaturas e entre Azure Active Directory locatários. As imagens também podem ser replicadas para várias regiões para um dimensionamento mais rápido da implantação.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma VM generalizada e uma imagem personalizada
> * Criar uma galeria de imagens compartilhadas
> * Criar uma imagem compartilhada e uma versão de imagem
> * Criar uma VM usando a imagem generalizada
> * Criar um conjunto de dimensionamento de máquinas virtuais usando a imagem generalizada
> * Obtenha informações sobre sua galeria de imagens, imagem e versão compartilhados.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Obter os guias estratégicos de exemplo

Há duas maneiras de obter o conjunto completo de guias estratégicos de exemplo:

- [Baixe a pasta SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) e salve-a em seu computador local.
- Crie um novo arquivo para cada seção e copie o guia estratégico de exemplo nele.

O arquivo `vars.yml` contém as variáveis usadas por todos os guias estratégicos de exemplo para este tutorial. Você pode editar o arquivo para fornecer nomes e valores exclusivos.

O primeiro guia estratégico de exemplo `00-prerequisites.yml` cria o que é necessário para concluir este tutorial:
- Um grupo de recursos, que é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.
- Uma rede virtual; redes Endereço IP público e placa de interface de rede para a VM.
- Uma máquina virtual de origem, que é usada para criar a imagem generalizada.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 00-prerequisites.yml
```

No [portal do Azure](https://portol.azure.com), verifique o grupo de recursos especificado em `vars.yml` para ver a nova máquina virtual e vários recursos que você criou.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalizar a VM e criar uma imagem personalizada

O próximo guia estratégico, `01a-create-generalized-image.yml`, generaliza a VM de origem criada na etapa anterior e, em seguida, cria uma imagem personalizada com base nela.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Verifique seu grupo de recursos e certifique-se de que `testimagea` seja exibido.

## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de imagens compartilhadas

A Galeria de imagens é o repositório para compartilhar e gerenciar imagens. O código do guia estratégico de exemplo no `02-create-shared-image-gallery.yml` cria uma galeria de imagens compartilhada em seu grupo de recursos.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Agora você verá uma nova galeria, `myGallery`, em seu grupo de recursos.

## <a name="create-a-shared-image-and-image-version"></a>Criar uma imagem compartilhada e uma versão de imagem

O próximo guia estratégico, `03a-create-shared-image-generalized.yml` cria uma definição de imagem e uma versão de imagem.

As definições de imagem incluem o tipo de imagem (Windows ou Linux), notas de versão e requisitos mínimos e máximos de memória. A versão da imagem é a versão da imagem. A Galeria, a definição de imagem e a versão de imagem ajudam a organizar imagens em grupos lógicos. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Seu grupo de recursos agora tem uma definição de imagem e uma versão de imagem para sua galeria.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Criar uma VM com base na imagem generalizada

Por fim, execute `04a-create-vm-using-generalized-image.yml` para criar uma VM com base na imagem generalizada criada na etapa anterior.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Criar conjuntos de dimensionamento de máquinas virtuais com base na imagem generalizada

Você também pode criar um conjunto de dimensionamento de máquinas virtuais com base na imagem generalizada. Execute `05a-create-vmss-using-generalized-image.yml` para fazer isso.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Obter informações sobre a Galeria

Você pode obter informações sobre a Galeria, a definição de imagem e a versão executando `06-get-info.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Excluir a imagem compartilhada

Para excluir os recursos da galeria, consulte o guia estratégico de exemplo `07-delete-gallery.yml`. Excluir recursos na ordem inversa. Comece excluindo a versão da imagem. Depois de excluir todas as versões da imagem, você pode excluir a definição da imagem. Depois de excluir todas as definições de imagem, você pode excluir a galeria.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo. 

O código do guia estratégico de exemplo nesta seção é usado para:

- Excluir os dois grupos de recursos criados anteriormente

Guarde o manual de procedimentos seguinte como `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Substitua o espaço reservado `{{ resource_group_name }}` pelo nome do seu grupo de recursos.
- Todos os recursos dentro dos dois grupos de recursos especificados serão excluídos.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)