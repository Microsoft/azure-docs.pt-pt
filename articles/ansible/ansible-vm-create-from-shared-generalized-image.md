---
title: Tutorial - Criar um conjunto de máquinas VM ou virtual a partir da Galeria de Imagem Partilhada Azure usando Ansible
description: Aprenda a usar Ansible para criar vM ou conjunto de escala de máquina virtual com base numa imagem generalizada na Galeria de Imagem Partilhada.
keywords: ansível, azul, devops, bash, playbook, máquina virtual, conjunto de escala de máquina virtual, galeria de imagem partilhada
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155832"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Tutorial: Criar um conjunto de vm ou máquina virtual da Galeria de Imagem Partilhada Azure usando Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[A Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries) é um serviço que lhe permite gerir, partilhar e organizar imagens geridas sob medida facilmente. Esta funcionalidade é benéfica para cenários onde muitas imagens são mantidas e partilhadas. Imagens personalizadas podem ser partilhadas através de subscrições e entre inquilinos do Azure Ative Directory. As imagens também podem ser replicadas em várias regiões para uma escala de implantação mais rápida.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um VM generalizado e imagem personalizada
> * Criar uma Galeria de Imagem Partilhada
> * Criar uma versão de imagem e imagem partilhada
> * Criar um VM usando a imagem generalizada
> * Criar um conjunto de escala de máquina virtual usando a imagem generalizada
> * Obtenha informações sobre a sua Galeria de Imagem Partilhada, imagem e versão.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Obtenha os livros de prostinções

Há duas maneiras de obter o conjunto completo de playbooks de amostra:

- [Descarregue a pasta SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) e guarde-a para a sua máquina local.
- Crie um novo ficheiro para cada secção e copie o livro de jogadas da amostra nele.

O `vars.yml` ficheiro contém as variáveis utilizadas por todos os playbooks da amostra para este tutorial. Pode editar o ficheiro para fornecer nomes e valores únicos.

O primeiro `00-prerequisites.yml` livro de jogadas de amostra cria o que é necessário para completar este tutorial:
- Um grupo de recursos, que é um recipiente lógico no qual os recursos azure são implantados e geridos.
- Uma rede virtual; subnet; endereço IP público e cartão de interface de rede para o VM.
- Uma fonte Máquina Virtual, que é usada para criar a imagem generalizada.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 00-prerequisites.yml
```

No [portal Azure,](https://portal.azure.com)consulte o grupo `vars.yml` de recursos que especificou para ver a nova máquina virtual e vários recursos que criou.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalize o VM e crie uma imagem personalizada

O próximo `01a-create-generalized-image.yml`livro de jogadas, generaliza a fonte VM criada em passo anterior e, em seguida, cria uma imagem personalizada com base nele.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Verifique o seu grupo `testimagea` de recursos e certifique-se de que aparece.

## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de Imagem Partilhada

A galeria de imagens é o repositório para a partilha e gestão de imagens. O código de `02-create-shared-image-gallery.yml` playbook da amostra cria uma Galeria de Imagem Partilhada no seu grupo de recursos.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Agora vê uma nova `myGallery`galeria, no seu grupo de recursos.

## <a name="create-a-shared-image-and-image-version"></a>Criar uma versão de imagem e imagem partilhada

O próximo `03a-create-shared-image-generalized.yml` livro de jogadas, cria uma definição de imagem e uma versão de imagem.

As definições de imagem incluem o tipo de imagem (Windows ou Linux), notas de lançamento e requisitos mínimos e máximos de memória. A versão de imagem é a versão da imagem. Galeria, definição de imagem e versão de imagem ajudam-no a organizar imagens em grupos lógicos. 

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

O seu grupo de recursos tem agora uma definição de imagem e uma versão de imagem para a sua galeria.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Criar um VM baseado na imagem generalizada

Finalmente, `04a-create-vm-using-generalized-image.yml` corra para criar um VM baseado na imagem generalizada que criou em passo anterior.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Criar um conjunto de escala de máquina virtual com base na imagem generalizada

Também pode criar um conjunto de escala de máquina virtual baseado na imagem generalizada. Corra `05a-create-vmss-using-generalized-image.yml` para fazê-lo.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Obtenha informações sobre a galeria

Pode obter informações sobre a galeria, definição de imagem e versão executando `06-get-info.yml`.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Eliminar a imagem partilhada

Para eliminar os recursos da `07-delete-gallery.yml`galeria, consulte o livro de imagens . Eliminar recursos por ordem inversa. Comece por apagar a versão de imagem. Depois de eliminar todas as versões de imagem, pode eliminar a definição de imagem. Depois de eliminar todas as definições de imagem, pode apagar a galeria.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo. 

O código de livro de amostras nesta secção é utilizado para:

- Eliminar os dois grupos de recursos criados anteriormente

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

Aqui estão algumas notas-chave a considerar ao trabalhar com o livro de jogadas da amostra:

- Substitua `{{ resource_group_name }}` o espaço reservado pelo nome do seu grupo de recursos.
- Todos os recursos dentro dos dois grupos de recursos especificados serão eliminados.

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)