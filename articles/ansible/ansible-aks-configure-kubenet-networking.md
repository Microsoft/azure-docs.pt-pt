---
title: Tutorial - Configure rede kubenet no Serviço Azure Kubernetes (AKS) usando Ansible
description: Saiba como usar o Ansible para configurar a rede kubenet no cluster Azure Kubernetes Service (AKS)
keywords: ansível, azul, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156887"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Configure a rede kubenet no Serviço Azure Kubernetes (AKS) usando Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Utilizando aks, pode implantar um cluster utilizando os seguintes modelos de rede:

- [Rede Kubenet](/azure/aks/configure-kubenet) - Os recursos de rede são tipicamente criados e configurados à medida que o cluster AKS é implantado.
- Rede de rede de [contentores Azure (CNI) networking](/azure/aks/configure-azure-cni) - Cluster AKS está ligado aos recursos e configurações de rede virtuais existentes.

Para obter mais informações sobre a ligação em rede às suas aplicações no AKS, consulte conceitos de [rede para aplicações no AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster do AKS (Create an AKS cluster)
> * Configure rede de kubenet Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

O código de jogadas nesta secção cria os seguintes recursos Azure:

- Rede virtual
- Sub-rede dentro da rede virtual

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

O código de jogadas nesta secção cria um cluster AKS dentro de uma rede virtual. 

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

Aqui estão algumas notas-chave a considerar ao trabalhar com o livro de jogadas da amostra:

- Utilize `azure_rm_aks_version` o módulo para encontrar a versão suportada.
- A `vnet_subnet_id` é a sub-rede criada na secção anterior.
- O `network_profile` define as propriedades para o plug-in da rede Kubenet.
- O `service_cidr` é utilizado para atribuir serviços internos no cluster AKS a um endereço IP. Esta gama de endereços IP deve ser um espaço de endereço que não é utilizado em outros lugares da sua rede. 
- O `dns_service_ip` endereço deve ser o endereço ".10" do seu endereço IP de serviço.
- O `pod_cidr` deve ser um grande espaço de endereçoque não está a ser usado em outros lugares do ambiente da sua rede. O intervalo de endereços deve ser grande o suficiente para acomodar o número de nós que espera escalar. Não pode alterar esta gama de endereços uma vez que o cluster é implantado.
- A gama de endereços IP da cápsula é utilizada para atribuir um espaço de endereço /24 a cada nó do cluster. No exemplo seguinte, `pod_cidr` o de 192.168.0.0/16 atribui o primeiro nó 192.168.0.0.0/24, o segundo nó 192.168.1.0/24 e o terceiro nó 192.168.2.0/24.
- À medida que o cluster escala ou atualiza, o Azure continua a atribuir uma gama de endereços IP de pod a cada novo nó.
- O livro `ssh_key` `~/.ssh/id_rsa.pub`de jogadas carrega de. Se o modificar, utilize o formato de linha única - começando com "ssh-rsa" (sem as cotações).
- Os `client_id` `client_secret` valores e `~/.azure/credentials`valores são carregados de , que é o ficheiro credencial padrão. Pode definir estes valores para o seu principal de serviço ou carregar estes valores a partir de variáveis ambientais:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associar os recursos da rede

Quando se cria um cluster AKS, é criado um grupo de segurança de rede e uma tabela de rotas. Estes recursos são geridos pela AKS e atualizados quando cria e expõe serviços. Associe o grupo de segurança da rede e a tabela de rotas com a sua subnet de rede virtual da seguinte forma. 

Guarde o `associate.yml`seguinte livro de jogadas como .

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

Aqui estão algumas notas-chave a considerar ao trabalhar com o livro de jogadas da amostra:

- É `node_resource_group` o nome do grupo de recursos em que os nódosos AKS são criados.
- A `vnet_subnet_id` é a sub-rede criada na secção anterior.


## <a name="run-the-sample-playbook"></a>Executar o livro de jogadas da amostra

Esta secção lista o manual completo da amostra que chama as tarefas que criam neste artigo. 

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

Na `vars` secção, enumere as seguintes alterações:

- Para `resource_group` a chave, `aksansibletest` altere o valor para o nome do seu grupo de recursos.
- Para `name` a chave, `aksansibletest` altere o valor para o seu nome AKS.
- Para `Location` a chave, `eastus` altere o valor para a localização do seu grupo de recursos.

Executar o manual `ansible-playbook` completo usando o comando:

```bash
ansible-playbook aks-kubenet.yml
```

Executar o livro de jogadas mostra resultados semelhantes aos seguintes resultados:

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

Quando já não for necessário, apague os recursos criados neste artigo. 

Guarde o `cleanup.yml`seguinte código como:

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

Na `vars` secção, substitua o `{{ resource_group_name }}` espaço reservado pelo nome do seu grupo de recursos.

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial - Configure Azure Container Networking Interface (CNI) networking em AKS usando Ansible](./ansible-aks-configure-cni-networking.md)