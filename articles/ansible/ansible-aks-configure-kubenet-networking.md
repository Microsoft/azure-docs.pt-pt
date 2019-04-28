---
title: Tutorial - configurar kubenet funcionamento em rede no Azure Kubernetes Service (AKS) com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para configurar kubenet redes no cluster do Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, contentores, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 96c280f43aec32fe3d6631108dfd7e0a3f789224
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767293"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Configurar kubenet funcionamento em rede no Azure Kubernetes Service (AKS) com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Utilizar o AKS, pode implementar um cluster com os seguintes modelos de rede:

- [Redes de Kubenet](/azure/aks/configure-kubenet) -recursos de rede, normalmente, são criados e configurados, conforme o cluster do AKS é implementado.
- [Redes de Interface de rede de contentor (CNI) do Azure](/azure/aks/configure-azure-cni) -cluster do AKS está ligado a recursos de rede virtual existente e configurações.

Para obter mais informações sobre o funcionamento em rede para seus aplicativos no AKS, consulte [conceitos de aplicativos no AKS de rede](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configurar redes de kubenet do Azure

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

O código do playbook nesta secção cria os seguintes recursos do Azure:

- Rede virtual
- Sub-rede na rede virtual

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

O código do playbook nesta secção cria um cluster do AKS dentro de uma rede virtual. 

Guarde o manual de procedimentos seguinte como `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- Utilize `azure_rm_aks_version` módulo para localizar a versão suportada.
- O `vnet_subnet_id` é a sub-rede que criou na secção anterior.
- O `network_profile` define as propriedades para o plug-in do kubenet rede.
- O `service_cidr` é usado para atribuir serviços internos do cluster do AKS para um endereço IP. Este intervalo de endereços IP deve ser um espaço de endereços que não é utilizado em outro lugar na sua rede. 
- O `dns_service_ip` endereço deve ser o ". 10" endereço do seu intervalo de endereços IP do serviço.
- O `pod_cidr` deve ser um espaço de endereçamento amplo que não está em utilização em outro lugar no seu ambiente de rede. O intervalo de endereços tem de ser suficientemente grande para acomodar o número de nós que pretende dimensionar até. Não é possível alterar este intervalo de endereços assim que o cluster é implementado.
- O intervalo de endereços IP de pod é usado para atribuir um /24 espaço para cada nó do cluster de endereços. No exemplo a seguir, o `pod_cidr` de 192.168.0.0/16 atribui a primeira 192.168.0.0/24 nó, o segundo 192.168.1.0/24 nó e o terceiro 192.168.2.0/24 nó.
- Como as escalas de cluster ou atualizações, o Azure continua atribuir um intervalo de endereços IP de pod para cada novo nó.
- Carrega o playbook `ssh_key` partir `~/.ssh/id_rsa.pub`. Se modificá-lo, utilize o formato de linha única - começando por "ssh-rsa" (sem as aspas).
- O `client_id` e `client_secret` valores são carregados a partir do `~/.azure/credentials`, que é o arquivo de credencial padrão. Pode definir esses valores ao seu serviço principal ou carregar estes valores de variáveis de ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associar os recursos de rede

Quando cria um cluster do AKS, uma tabela de grupo e a rota da segurança de rede são criados. Estes recursos são geridos pelo AKS e atualizados ao criar e expor os serviços. Associe a tabela de grupo e a rota da segurança de rede à sub-rede de rede virtual da seguinte forma. 

Guardar o playbook seguinte como `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Seguem-se algumas notas essenciais a considerar ao trabalhar com o playbook de exemplo:

- O `node_resource_group` é o nome do grupo de recursos em que são criados os nós do AKS.
- O `vnet_subnet_id` é a sub-rede que criou na secção anterior.


## <a name="run-the-sample-playbook"></a>Executar o playbook de exemplo

Esta secção lista o playbook de exemplo completo que chama as tarefas que criar neste artigo. 

Guarde o manual de procedimentos seguinte como `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

Na `vars` secção, efetue as seguintes alterações:

- Para o `resource_group` alteração de chaves, o `aksansibletest` valor para o seu nome de grupo de recursos.
- Para o `name` alteração de chaves, o `aksansibletest` valor para o seu nome AKS.
- Para o `Location` alteração de chaves, o `eastus` valor para a sua localização do grupo de recursos.

Executar o playbook completado com o `ansible-playbook` comando:

```bash
ansible-playbook aks-kubenet.yml
```

Executar o playbook mostra resultados semelhantes à saída seguinte:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

Guarde o código a seguir como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Na `vars` secção, substitua o `{{ resource_group_name }}` marcador de posição pelo nome do seu grupo de recursos.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial - configurar redes de Interface de rede contentor do Azure (CNI) no AKS com o Ansible](./ansible-aks-configure-cni-networking.md)