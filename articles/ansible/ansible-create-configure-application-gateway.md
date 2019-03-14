---
title: Gerir o tráfego da web com o Gateway de aplicação do Azure com o Ansible
description: Saiba como pode utilizar o Ansible para criar e configurar um Gateway de Aplicação do Azure para gerir o tráfego da Web
ms.service: azure
keywords: devops do azure, do ansible, bash, playbook, gateway de aplicação, Balanceador de carga, o tráfego da web
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 83f21573af7ec523acc376c4b3364cdcfb47f96f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792145"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Gerir o tráfego da web com o Gateway de aplicação do Azure com o Ansible

O [Gateway de Aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/) é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego das suas aplicações Web.

O Ansible ajuda-o a automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra-lhe como utilizar o Ansible para criar um gateway de aplicação. Também ensina a utilizar o gateway para gerir o tráfego para dois servidores Web que são executados em instância de contentor do Azure.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar duas instâncias de contentor do Azure com imagens HTTPD
> * Criar um gateway de aplicação que funciona com as instâncias de contentor do Azure no agrupamento de servidores

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os manuais de procedimentos de exemplo neste tutorial. 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome **myResourceGroup** na localização **eastus**.

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

Guarde este manual de procedimentos como *rg.yml*. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Criar recursos de rede

Primeiro, crie uma rede virtual para permitir que o gateway de aplicação comunique com outros recursos.

O exemplo seguinte cria uma rede virtual denominada **myVNet**, uma sub-rede denominada **myAGSubnet** e um endereço IP público denominado **myAGPublicIPAddress** com o nome de domínio **mydomain**.

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

Guarde este manual de procedimentos como *vnet_create.yml*. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Criar servidores

O exemplo seguinte mostra-lhe como criar duas instâncias de contentor do Azure com imagens HTTPD para serem utilizadas como servidores Web para o gateway de aplicação.  

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

Guarde este manual de procedimentos como *aci_create.yml*. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

O exemplo seguinte cria um gateway de aplicação com o nome **myAppGateway**, com configurações para back-end, front-end e HTTP.  

* **appGatewayIP** é definido no bloco **gateway_ip_configurations**. Para a configuração do IP do gateway, é necessária uma referência de sub-rede.
* **appGatewayBackendPool** é definido no bloco **backend_address_pools**. Os gateways de aplicação têm de ter, pelo menos, um conjunto de endereços de back-end.
* **appGatewayBackendHttpSettings** é definido no bloco **backend_http_settings_collection**. Especifica que a porta 80 e um protocolo HTTP são utilizados para a comunicação.
* **appGatewayHttpListener** é definido no bloco **backend_http_settings_collection**. É o serviço de escuta predefinido associado a appGatewayBackendPool.
* **appGatewayFrontendIP** é definido no bloco **frontend_ip_configurations**. Atribui myAGPublicIPAddress a appGatewayHttpListener.
* **rule1** é definido no bloco **request_routing_rules**. É a regra de encaminhamento predefinida associada a appGatewayHttpListener.

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

Guarde este manual de procedimentos como *appgw_create.yml*. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook appgw_create.yml
```

A criação do gateway de aplicação pode demorar vários minutos.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

No manual de procedimentos de exemplo relativo aos recursos de rede, o domínio **mydomain** foi criado em **eastus**. Aceda a `http://mydomain.eastus.cloudapp.azure.com` no browser. Se vir a página seguinte, quer dizer que o gateway de aplicação está a funcionar devidamente.

![Teste bem-sucedido de um gateway de aplicação em funcionamento](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar destes recursos, pode executar o código seguinte e eliminá-los. Elimina um grupo de recursos com o nome **myResourceGroup**.

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

Guarde o manual de procedimentos como *rg_delete*.yml. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)
