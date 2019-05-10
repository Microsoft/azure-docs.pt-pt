---
title: Tutorial - Gerir o tráfego web com o Gateway de aplicação do Azure com o Ansible | Documentos da Microsoft
description: Saiba como pode utilizar o Ansible para criar e configurar um Gateway de Aplicação do Azure para gerir o tráfego da Web
keywords: devops do azure, do ansible, bash, playbook, gateway de aplicação, Balanceador de carga, o tráfego da web
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9f8ed3e1da72db3e1b13d5d2aef1cce8fc3922a2
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231251"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Tutorial: Gerir o tráfego web com o Gateway de aplicação do Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

O [Gateway de Aplicação do Azure](/azure/application-gateway/overview) é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego das suas aplicações Web. Com base no endereço IP de origem e porta, balanceadores de carga tradicional encaminham o tráfego para um endereço IP de destino e porta. Gateway de aplicação proporciona um nível mais de controle em que o tráfego pode ser encaminhado com base no URL. Por exemplo, poderia definir que, se `images` é o caminho do URL, o tráfego é encaminhado para um conjunto específico de servidores (conhecido como agrupamento) configurado para imagens.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurar uma rede
> * Criar duas instâncias de contentor do Azure com imagens HTTPD
> * Criar um gateway de aplicação que funciona com as instâncias de contentor do Azure no agrupamento de servidores

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O código do playbook nesta secção cria um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico no qual os recursos estão configurados.  

Guarde o manual de procedimentos seguinte como `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Antes de executar o playbook, consulte as seguintes notas de:

- O nome do grupo de recursos é `myResourceGroup`. Este valor é utilizado ao longo do tutorial.
- O grupo de recursos é criado no `eastus` localização.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Criar recursos de rede

O código do playbook nesta secção cria uma rede virtual para ativar o gateway de aplicação comunicar com outros recursos.

Guarde o manual de procedimentos seguinte como `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Antes de executar o playbook, consulte as seguintes notas de:

* O `vars` secção contém os valores que são utilizados para criar os recursos de rede. 
* Terá de alterar estes valores para o seu ambiente específico.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Criar servidores

O código do playbook nesta secção cria duas instâncias de contentor do Azure com imagens HTTPD a ser utilizada como servidores web para o gateway de aplicação.  

Guarde o manual de procedimentos seguinte como `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

O código de playbook nesta secção cria um gateway de aplicação com o nome `myAppGateway`.  

Guarde o manual de procedimentos seguinte como `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Antes de executar o playbook, consulte as seguintes notas de:

* `appGatewayIP` está definido no `gateway_ip_configurations` bloco. Para a configuração do IP do gateway, é necessária uma referência de sub-rede.
* `appGatewayBackendPool` está definido no `backend_address_pools` bloco. Os gateways de aplicação têm de ter, pelo menos, um conjunto de endereços de back-end.
* `appGatewayBackendHttpSettings` está definido no `backend_http_settings_collection` bloco. Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
* `appGatewayHttpListener` está definido no `backend_http_settings_collection` bloco. É o serviço de escuta predefinido associado a appGatewayBackendPool.
* `appGatewayFrontendIP` está definido no `frontend_ip_configurations` bloco. Atribui myAGPublicIPAddress a appGatewayHttpListener.
* `rule1` está definido no `request_routing_rules` bloco. É a regra de encaminhamento predefinida associada a appGatewayHttpListener.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook appgw_create.yml
```

A criação do gateway de aplicação pode demorar vários minutos.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Na [criar um grupo de recursos](#create-a-resource-group) secção, especifique uma localização. Tenha em atenção o seu valor.

1. Na [criar recursos de rede](#create-network-resources) secção, especifique o domínio. Tenha em atenção o seu valor.

1. Para o URL de teste, substituindo o seguinte padrão com a localização e o domínio: `http://<domain>.<location>.cloudapp.azure.com`.

1. Navegue para o URL de teste.

1. Se vir a página seguinte, quer dizer que o gateway de aplicação está a funcionar devidamente.

    ![Teste bem-sucedido de um gateway de aplicação em funcionamento](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

Guarde o código a seguir como `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)