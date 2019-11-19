---
title: Tutorial – gerenciar o tráfego da Web com o gateway de Aplicativo Azure usando o Ansible
description: Saiba como pode utilizar o Ansible para criar e configurar um Gateway de Aplicação do Azure para gerir o tráfego da Web
keywords: Ansible, Azure, DevOps, Bash, manual, gateway de aplicativo, balanceador de carga, tráfego da Web
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156622"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Tutorial: gerenciar o tráfego da Web com o gateway de Aplicativo Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

O [Gateway de Aplicação do Azure](/azure/application-gateway/overview) é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego das suas aplicações Web. Com base na porta e no endereço IP de origem, os balanceadores de carga tradicionais roteiam o tráfego para um endereço IP de destino e porta. O gateway de aplicativo fornece um nível mais refinado de controle em que o tráfego pode ser roteado com base na URL. Por exemplo, você poderia definir que, se `images` for o caminho da URL, o tráfego será roteado para um conjunto específico de servidores (conhecido como um pool) configurado para imagens.

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

O código do guia estratégico nesta seção cria um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são configurados.  

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

Antes de executar o guia estratégico, consulte as seguintes observações:

- O nome do grupo de recursos é `myResourceGroup`. Esse valor é usado em todo o tutorial.
- O grupo de recursos é criado no local `eastus`.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Criar recursos de rede

O código do guia estratégico nesta seção cria uma rede virtual para permitir que o gateway de aplicativo se comunique com outros recursos.

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

Antes de executar o guia estratégico, consulte as seguintes observações:

* A seção `vars` contém os valores que são usados para criar os recursos de rede. 
* Você precisará alterar esses valores para seu ambiente específico.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Criar servidores

O código de guia estratégico nesta seção cria duas instâncias de contêiner do Azure com imagens HTTP para serem usadas como servidores Web para o gateway de aplicativo.  

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

O código do guia estratégico nesta seção cria um gateway de aplicativo chamado `myAppGateway`.  

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

Antes de executar o guia estratégico, consulte as seguintes observações:

* `appGatewayIP` é definido no bloco de `gateway_ip_configurations`. Para a configuração do IP do gateway, é necessária uma referência de sub-rede.
* `appGatewayBackendPool` é definido no bloco de `backend_address_pools`. Os gateways de aplicação têm de ter, pelo menos, um conjunto de endereços de back-end.
* `appGatewayBackendHttpSettings` é definido no bloco de `backend_http_settings_collection`. Ele especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
* `appGatewayHttpListener` é definido no bloco de `backend_http_settings_collection`. É o serviço de escuta predefinido associado a appGatewayBackendPool.
* `appGatewayFrontendIP` é definido no bloco de `frontend_ip_configurations`. Atribui myAGPublicIPAddress a appGatewayHttpListener.
* `rule1` é definido no bloco de `request_routing_rules`. É a regra de encaminhamento predefinida associada a appGatewayHttpListener.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook appgw_create.yml
```

A criação do gateway de aplicação pode demorar vários minutos.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Na seção [criar um grupo de recursos](#create-a-resource-group) , especifique um local. Anote seu valor.

1. Na seção [criar recursos de rede](#create-network-resources) , você especifica o domínio. Anote seu valor.

1. Para a URL de teste substituindo o seguinte padrão pelo local e domínio: `http://<domain>.<location>.cloudapp.azure.com`.

1. Navegue até a URL de teste.

1. Se vir a página seguinte, quer dizer que o gateway de aplicação está a funcionar devidamente.

    ![Teste bem-sucedido de um gateway de aplicação em funcionamento](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo. 

Salve o código a seguir como `cleanup.yml`:

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)