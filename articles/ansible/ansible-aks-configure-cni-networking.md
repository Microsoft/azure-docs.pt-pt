---
title: Tutorial - configurar redes do Azure CNI no Azure Kubernetes Service (AKS) com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para configurar kubenet redes no cluster do Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, contentores, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 2d43b1ffbb7910b16c81df2ff5b21e67dbcb0193
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231355"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Configurar redes do Azure CNI no Azure Kubernetes Service (AKS) com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Utilizar o AKS, pode implementar um cluster com os seguintes modelos de rede:

- [Redes de Kubenet](/azure/aks/configure-kubenet) -recursos de rede, normalmente, são criados e configurados, conforme o cluster do AKS é implementado.
- [Redes do Azure de CNI](/azure/aks/configure-azure-cni) -cluster do AKS está ligado a recursos de rede virtual (VNET) existentes e configurações.

Para obter mais informações sobre o funcionamento em rede para seus aplicativos no AKS, consulte [conceitos de aplicativos no AKS de rede](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configurar rede de CNI do Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

O código do playbook de exemplo nesta secção é utilizado para:

- Criar uma rede virtual
- Criar uma sub-rede dentro da rede virtual

Guarde o manual de procedimentos seguinte como `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster do AKS na rede virtual

O código do playbook de exemplo nesta secção é utilizado para:

- Crie um cluster do AKS dentro de uma rede virtual.

Guarde o manual de procedimentos seguinte como `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Utilize o `azure_rm_aks_version` módulo para localizar a versão suportada.
- O `vnet_subnet_id` é a sub-rede que criou na secção anterior.
- Carrega o playbook `ssh_key` partir `~/.ssh/id_rsa.pub`. Se modificá-lo, utilize o formato de linha única - começando por "ssh-rsa" (sem as aspas).
- O `client_id` e `client_secret` valores são carregados a partir do `~/.azure/credentials`, que é o arquivo de credencial padrão. Pode definir esses valores ao seu serviço principal ou carregar estes valores de variáveis de ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Executar o playbook de exemplo

O código do playbook de exemplo nesta secção é utilizado para testar várias funcionalidades mostradas neste tutorial.

Guarde o manual de procedimentos seguinte como `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Alterar o `aksansibletest` valor para o seu nome de grupo de recursos.
- Alterar o `aksansibletest` valor para o seu nome AKS.
- Alterar o `eastus` valor para a sua localização do grupo de recursos.

Execute o playbook com o comando do ansible playbook:

```bash
ansible-playbook aks-azure-cni.yml
```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

O código do playbook de exemplo nesta secção é utilizado para:

- Eliminar um grupo de recursos mencionado na `vars` secção.

Guarde o manual de procedimentos seguinte como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Substitua o `{{ resource_group_name }}` marcador de posição pelo nome do seu grupo de recursos.
- Todos os recursos no grupo de recursos especificado serão eliminados.

Execute o playbook com o comando do ansible playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Configurar o Azure Active Directory no AKS com o Ansible](./ansible-aks-configure-rbac.md)