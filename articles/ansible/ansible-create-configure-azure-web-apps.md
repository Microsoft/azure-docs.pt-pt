---
title: Tutorial - configurar aplicações no serviço de aplicações do Azure com o Ansible | Documentos da Microsoft
description: Saiba como criar uma aplicação no serviço de aplicações do Azure com Java 8 e o tempo de execução do contentor Tomcat
keywords: ansible, azure, devops, bash, manual de procedimentos, Serviço de Aplicações do Azure,Aplicação Web, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: aed09baf410ce25f2e5383aa746344a440e2a052
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231236"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Tutorial: Configurar aplicações no serviço de aplicações do Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma aplicação no serviço de aplicações do Azure com Java 8 e o tempo de execução do contentor Tomcat
> * Criar um perfil do Gestor de tráfego do Azure
> * Definir um ponto de final do Gestor de tráfego através da aplicação criada

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Criar um serviço de aplicações básico

O código do playbook nesta secção define os seguintes recursos:

* Grupo de recursos do Azure no qual o plano de serviço de aplicações e aplicações são implementadas
* Serviço de aplicações no Linux com Java 8 e o tempo de execução do contentor Tomcat

Guarde o manual de procedimentos seguinte como `firstwebapp.yml`:

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook firstwebapp.yml
```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Criar uma aplicação e utilizar o Gestor de tráfego do Azure

[O Gestor de tráfego do Azure](/azure/app-service/web-sites-traffic-manager) permite-lhe controlar como os pedidos de clientes da web são distribuídos para as aplicações no serviço de aplicações do Azure. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego controla o estado das suas aplicações do Serviço de Aplicações. Os estados incluem em execução, paradas e eliminadas. O Gestor de tráfego é utilizado para decidir quais pontos de extremidade devem receber o tráfego.

No Serviço de Aplicações, as aplicações são executadas num [plano do Serviço de Aplicações](/azure/app-service/overview-hosting-plans). Um plano do serviço de aplicação define um conjunto de recursos de computação para uma aplicação ser executado. Pode gerir o seu plano do Serviço de Aplicações e a aplicação Web em grupos diferentes.

O código do playbook nesta secção define os seguintes recursos:

* Grupo de recursos do Azure no qual está implementado o plano do serviço de aplicações
* Plano do App Service
* Grupo de recursos do Azure no qual a aplicação é implementada
* Serviço de aplicações no Linux com Java 8 e o tempo de execução do contentor Tomcat
* Perfil do Gestor de Tráfego
* Ponto final do Gestor de tráfego através da aplicação criada

Guarde o manual de procedimentos seguinte como `webapp.yml`:

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook webapp.yml
```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Tutorial: Aplicações de escala no serviço de aplicações do Azure com o Ansible](/azure/ansible/ansible-scale-azure-web-apps)