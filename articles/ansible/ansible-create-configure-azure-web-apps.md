---
title: Tutorial-configurar aplicativos no serviço Azure App usando o Ansible
description: Saiba como criar um aplicativo no serviço de Azure App com o Java 8 e o tempo de execução do contêiner do Tomcat
keywords: ansible, azure, devops, bash, manual de procedimentos, Serviço de Aplicações do Azure,Aplicação Web, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5104db6bd7fa57600c7212e041263971ca4c91d4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242055"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Tutorial: configurar aplicativos no serviço Azure App usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um aplicativo no serviço de Azure App com o Java 8 e o tempo de execução do contêiner do Tomcat
> * Criar um perfil do Gerenciador de tráfego do Azure
> * Definir um ponto de extremidade do Gerenciador de tráfego usando o aplicativo criado

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Criar um serviço de aplicativo básico

O código do guia estratégico nesta seção define os seguintes recursos:

* Grupo de recursos do Azure no qual o plano do serviço de aplicativo e o aplicativo são implantados
* Serviço de aplicativo no Linux com Java 8 e o tempo de execução do contêiner Tomcat

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook firstwebapp.yml
```

Depois de executar o guia estratégico, você verá uma saída semelhante aos seguintes resultados:

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

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Criar um aplicativo e usar o Gerenciador de tráfego do Azure

O [Gerenciador de tráfego do Azure](/azure/app-service/web-sites-traffic-manager) permite controlar como as solicitações de clientes Web são distribuídas para aplicativos no serviço Azure app. Quando os pontos finais do Serviço de Aplicações são adicionados a um perfil do Gestor de Tráfego do Azure, o Gestor de Tráfego controla o estado das suas aplicações do Serviço de Aplicações. Os estados incluem em execução, paradas e eliminadas. O Gerenciador de tráfego é usado para decidir quais pontos de extremidade devem receber o tráfego.

No Serviço de Aplicações, as aplicações são executadas num [plano do Serviço de Aplicações](/azure/app-service/overview-hosting-plans). Um plano do serviço de aplicativo define um conjunto de recursos de computação para que um aplicativo seja executado. Pode gerir o seu plano do Serviço de Aplicações e a aplicação Web em grupos diferentes.

O código do guia estratégico nesta seção define os seguintes recursos:

* Grupo de recursos do Azure no qual o plano do serviço de aplicativo é implantado
* Plano do App Service
* Grupo de recursos do Azure no qual o aplicativo é implantado
* Serviço de aplicativo no Linux com Java 8 e o tempo de execução do contêiner Tomcat
* Perfil do Gestor de Tráfego
* Ponto de extremidade do Gerenciador de tráfego usando o aplicativo criado

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook webapp.yml
```

Depois de executar o guia estratégico, você verá uma saída semelhante aos seguintes resultados:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Tutorial: dimensionar aplicativos em Azure App serviço usando o Ansible](/azure/ansible/ansible-scale-azure-web-apps)