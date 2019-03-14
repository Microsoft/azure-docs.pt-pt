---
title: Criar aplicações web do Azure com o Ansible
description: Saiba como utilizar o Ansible para criar uma aplicação Web com o runtime de contentor Java 8 e Tomcat no Serviço de Aplicações no Linux
ms.service: azure
keywords: ansible, azure, devops, bash, manual de procedimentos, Serviço de Aplicações do Azure,Aplicação Web, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 5f67a9f7d629eec9ab1462a25940355869c1cd28
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791227"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Criar aplicações web do serviço de aplicações do Azure com o Ansible
[Aplicações de Web do serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/overview) (ou apenas aplicativos da Web) anfitriões e aplicações web, REST APIs, back-ends móveis. Pode desenvolver no seu idioma favorito&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra-lhe como utilizar o Ansible para criar uma aplicação Web com o runtime de Java. 

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os manuais de procedimentos de exemplo neste tutorial.

## <a name="create-a-simple-app-service"></a>Criar um serviço de Aplicações simples
Esta secção apresenta um manual de procedimentos do Ansible de exemplo que define os seguintes recursos:
- Grupo de recursos, onde o plano do Serviço de Aplicações e a aplicação Web serão implementados
- numa aplicação Web com o runtime de contentor Java 8 e Tomcat no Serviço de Aplicações no Linux

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```
Guarde o manual de procedimentos anterior como **firstwebapp.yml**.

Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook firstwebapp.yml
```

O resultado da execução do manual de procedimentos do Ansible mostra que a aplicação Web foi criada com êxito:

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Criar um serviço de aplicações com o Gestor de Tráfego
Pode utilizar o [Gestor de Tráfego do Azure](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) para controlar como os pedidos de clientes Web são distribuídos para as aplicações no Serviço de Aplicações do Azure. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego controla o estado das suas aplicações do Serviço de Aplicações. Os estados incluem em execução, paradas e eliminadas. O Gestor de Tráfego pode, em seguida, decidir quais destes pontos finais devem receber tráfego.

No Serviço de Aplicações, as aplicações são executadas num [plano do Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
). Um plano do Serviço de Aplicações define um conjunto de recursos de computação para a execução da aplicação Web. Pode gerir o seu plano do Serviço de Aplicações e a aplicação Web em grupos diferentes.

Esta secção apresenta um manual de procedimentos do Ansible de exemplo que define os seguintes recursos:
- Grupo de recursos, onde o plano do serviço de aplicações será implementado
- Plano do App Service
- Grupo de recursos secundário, onde a aplicação Web será implementada
- numa aplicação Web com o runtime de contentor Java 8 e Tomcat no Serviço de Aplicações no Linux
- Perfil do Gestor de Tráfego
- Ponto final do Gestor de Tráfego, com o site criado

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```
Guarde o manual de procedimentos anterior como **webapp.yml** ou [transfira o manual de procedimentos](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook webapp.yml
```

O resultado da execução do manual de procedimentos do Ansible mostra que o plano do Serviço de Aplicações, a aplicação Web, o perfil do Gestor de Tráfego e o ponto final foram criados com êxito:
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Dimensionar aplicações web do serviço de aplicações do Azure com o Ansible](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)