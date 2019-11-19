---
title: Tutorial – configurar a rede CNI do Azure no AKS (serviço kubernetes do Azure) usando o Ansible
description: Saiba como usar o Ansible para configurar a rede kubenet no cluster do AKS (serviço kubernetes do Azure)
keywords: Ansible, Azure, DevOps, Bash, cloudshell, manual, AKs, contêiner, AKs, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156911"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: configurar a rede CNI do Azure no serviço kubernetes do Azure (AKS) usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Usando o AKS, você pode implantar um cluster usando os seguintes modelos de rede:

- [Rede Kubenet](/azure/aks/configure-kubenet) – os recursos de rede normalmente são criados e configurados à medida que o cluster AKs é implantado.
- [Rede CNI do Azure](/azure/aks/configure-azure-cni) – o cluster AKs está conectado a recursos e configurações de VNET (rede virtual) existentes.

Para obter mais informações sobre a rede para seus aplicativos no AKS, consulte [conceitos de rede para aplicativos em AKs](/azure/aks/concepts-network).

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

O código do guia estratégico de exemplo nesta seção é usado para:

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

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster AKS na rede virtual

O código do guia estratégico de exemplo nesta seção é usado para:

- Crie um cluster AKS em uma rede virtual.

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

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Use o módulo `azure_rm_aks_version` para localizar a versão com suporte.
- O `vnet_subnet_id` é a sub-rede criada na seção anterior.
- O guia estratégico carrega `ssh_key` de `~/.ssh/id_rsa.pub`. Se você modificá-lo, use o formato de linha única-começando com "ssh-RSA" (sem as aspas).
- Os valores de `client_id` e `client_secret` são carregados de `~/.azure/credentials`, que é o arquivo de credencial padrão. Você pode definir esses valores para sua entidade de serviço ou carregar esses valores de variáveis de ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Executar o guia estratégico de exemplo

O código do guia estratégico de exemplo nesta seção é usado para testar vários recursos mostrados em todo este tutorial.

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

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Altere o valor de `aksansibletest` para o nome do grupo de recursos.
- Altere o valor de `aksansibletest` para o nome do AKS.
- Altere o valor de `eastus` para o local do grupo de recursos.

Execute o guia estratégico usando o comando Ansible-manual:

```bash
ansible-playbook aks-azure-cni.yml
```

Depois de executar o guia estratégico, você verá uma saída semelhante aos seguintes resultados:

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

Quando não for mais necessário, exclua os recursos criados neste artigo. 

O código do guia estratégico de exemplo nesta seção é usado para:

- Exclua um grupo de recursos referido na seção `vars`.

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

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Substitua o espaço reservado `{{ resource_group_name }}` pelo nome do seu grupo de recursos.
- Todos os recursos dentro do grupo de recursos especificado serão excluídos.

Execute o guia estratégico usando o comando Ansible-manual:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: configurar Azure Active Directory no AKS usando Ansible](./ansible-aks-configure-rbac.md)