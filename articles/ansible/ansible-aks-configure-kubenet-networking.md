---
title: Tutorial – configurar a rede kubenet no AKS (serviço kubernetes do Azure) usando o Ansible
description: Saiba como usar o Ansible para configurar a rede kubenet no cluster do AKS (serviço kubernetes do Azure)
keywords: Ansible, Azure, DevOps, Bash, cloudshell, manual, AKs, contêiner, AKs, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/23/2019
ms.openlocfilehash: 67b4eb9e9ee53613ec8b54b2bf8d3bbdb89778c7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881530"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: configurar a rede kubenet no AKS (serviço kubernetes do Azure) usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Usando o AKS, você pode implantar um cluster usando os seguintes modelos de rede:

- [Rede Kubenet](/azure/aks/configure-kubenet) – os recursos de rede normalmente são criados e configurados à medida que o cluster AKs é implantado.
- [Rede CNI (interface de rede de contêiner do Azure)](/azure/aks/configure-azure-cni) -o cluster AKs está conectado a recursos e configurações de rede virtual existentes.

Para obter mais informações sobre a rede para seus aplicativos no AKS, consulte [conceitos de rede para aplicativos em AKs](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configurar a rede kubenet do Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

O código do guia estratégico nesta seção cria os seguintes recursos do Azure:

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

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster AKS na rede virtual

O código do guia estratégico nesta seção cria um cluster AKS em uma rede virtual. 

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

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- Use `azure_rm_aks_version` módulo para localizar a versão com suporte.
- O `vnet_subnet_id` é a sub-rede criada na seção anterior.
- O `network_profile` define as propriedades do plug-in de rede kubenet.
- O `service_cidr` é usado para atribuir serviços internos no cluster AKS a um endereço IP. Esse intervalo de endereços IP deve ser um espaço de endereço que não é usado em outro lugar na rede. 
- O endereço de `dns_service_ip` deve ser o endereço ". 10" do intervalo de endereços IP do seu serviço.
- O `pod_cidr` deve ser um espaço de endereço grande que não está em uso em outro lugar em seu ambiente de rede. O intervalo de endereços deve ser grande o suficiente para acomodar o número de nós que você espera escalar verticalmente. Você não pode alterar esse intervalo de endereços depois que o cluster é implantado.
- O intervalo de endereços IP de Pod é usado para atribuir um espaço de endereço/24 a cada nó no cluster. No exemplo a seguir, o `pod_cidr` de 192.168.0.0/16 atribui o primeiro nó 192.168.0.0/24, o segundo nó 192.168.1.0/24 e o terceiro nó 192.168.2.0/24.
- À medida que o cluster é dimensionado ou atualizado, o Azure continua a atribuir um intervalo de endereços IP de Pod a cada novo nó.
- O guia estratégico carrega `ssh_key` de `~/.ssh/id_rsa.pub`. Se você modificá-lo, use o formato de linha única-começando com "ssh-RSA" (sem as aspas).
- Os valores de `client_id` e `client_secret` são carregados de `~/.azure/credentials`, que é o arquivo de credencial padrão. Você pode definir esses valores para sua entidade de serviço ou carregar esses valores de variáveis de ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associar os recursos de rede

Quando você cria um cluster AKS, um grupo de segurança de rede e uma tabela de rotas são criados. Esses recursos são gerenciados pelo AKS e atualizados quando você cria e expõe serviços. Associe o grupo de segurança de rede e a tabela de rotas à sua sub-rede de rede virtual da seguinte maneira. 

Salve o guia estratégico a seguir como `associate.yml`.

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

Aqui estão algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- O `node_resource_group` é o nome do grupo de recursos no qual os nós AKS são criados.
- O `vnet_subnet_id` é a sub-rede criada na seção anterior.


## <a name="run-the-sample-playbook"></a>Executar o guia estratégico de exemplo

Esta seção lista o manual de exemplo completo que chama as tarefas que criam neste artigo. 

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

Na seção `vars`, faça as seguintes alterações:

- Para a chave de `resource_group`, altere o valor de `aksansibletest` para o nome do grupo de recursos.
- Para a chave `name`, altere o valor de `aksansibletest` para o nome do AKS.
- Para a chave `Location`, altere o valor de `eastus` para o local do grupo de recursos.

Execute o manual completo usando o comando `ansible-playbook`:

```bash
ansible-playbook aks-kubenet.yml
```

A execução do manual mostra resultados semelhantes à seguinte saída:

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

Quando não for mais necessário, exclua os recursos criados neste artigo. 

Salve o código a seguir como `cleanup.yml`:

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

Na seção `vars`, substitua o espaço reservado `{{ resource_group_name }}` pelo nome do seu grupo de recursos.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial – configurar a rede de CNI (interface de rede de contêiner do Azure) no AKS usando o Ansible](./ansible-aks-configure-cni-networking.md)