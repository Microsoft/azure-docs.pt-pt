---
title: Dimensionar aplicações web do serviço de aplicações do Azure com o Ansible
description: Saiba como utilizar o Ansible para criar uma aplicação Web com o runtime de contentor Java 8 e Tomcat no Serviço de Aplicações no Linux
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, serviço de aplicações do Azure, aplicação Web, dimensionamento, o Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 2bafb73afa35c7670ac45f7027545277c70075ef
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792281"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Dimensionar aplicações web do serviço de aplicações do Azure com o Ansible
[Aplicações de Web do serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/overview) (ou apenas aplicativos da Web) anfitriões e aplicações web, REST APIs, back-end móvel. Pode desenvolver no seu idioma favorito&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Este artigo mostra-lhe como utilizar o Ansible para dimensionar a sua aplicação no serviço de aplicações do Azure.

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Aplicações de Web do serviço de aplicações do Azure** -se ainda não tiver uma aplicação de web do serviço de aplicações do Azure, pode [criar aplicações web do Azure com o Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Aumentar verticalmente uma aplicação no serviço de aplicações
Pode aumentar verticalmente ao alterar o escalão de preço do plano do serviço de aplicações que qual pertence a sua aplicação. Esta secção apresenta um playbook de Ansible de exemplo que define a seguinte operação:
- Obtenha os factos de um plano de serviço de aplicações existente
- Atualizar o plano do serviço de aplicações para S2 com três funções de trabalho

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Guardar este manual de comunicação social como *webapp_scaleup.yml*.

Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook webapp_scaleup.yml
```

Depois de executar o playbook, o resultado semelhante ao seguinte exemplo mostra que o plano do serviço de aplicações tem sido atualizado com êxito para S2 com três funções de trabalho:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App service plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)