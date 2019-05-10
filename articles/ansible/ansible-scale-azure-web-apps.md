---
title: Tutorial – dimensionar aplicações no serviço de aplicações do Azure com o Ansible | Documentos da Microsoft
description: Saiba como aumentar verticalmente uma aplicação no App Service do Azure
keywords: ansible, azure, devops, bash, playbook, serviço de aplicações do Azure, aplicação Web, dimensionamento, o Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d63708cd87afa426f2712da6d0fcb11c84590798
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230958"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Tutorial: Aplicações de escala no serviço de aplicações do Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Obtenha os factos de um plano de serviço de aplicações existente
> * Aumentar verticalmente o plano do serviço de aplicações para S2 com três funções de trabalho

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplicação de serviço de aplicações do Azure** – se não tiver uma aplicação de serviço de aplicações do Azure [configurar uma aplicação no serviço de aplicações do Azure com o Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Aumentar verticalmente uma aplicação

Existem dois fluxos de trabalho de dimensionamento: *aumentar verticalmente* e *aumentar horizontalmente*.

**Aumente verticalmente:** Para aumentar verticalmente significa adquirir mais recursos. Esses recursos incluem a CPU, memória, espaço em disco, VMs e muito mais. Aumentar verticalmente uma aplicação ao alterar o escalão de preço do plano do serviço de aplicações a que pertence a aplicação. 
**Aumente horizontalmente:** Para aumentar horizontalmente significa para aumentar o número de instâncias VM que executam a aplicação. Dependendo do seu plano de serviço de aplicações, escalão de preço, pode aumentar horizontalmente até 20 instâncias. [Dimensionamento automático](/azure/azure-monitor/platform/autoscale-get-started) permite-lhe dimensionar automaticamente com base em regras predefinidas e agendas de contagem de instâncias.

O código de playbook nesta secção define a seguinte operação:

* Obtenha os factos de um plano de serviço de aplicações existente
* Atualizar o plano do serviço de aplicações para S2 com três funções de trabalho

Guarde o manual de procedimentos seguinte como `webapp_scaleup.yml`:

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook webapp_scaleup.yml
```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)