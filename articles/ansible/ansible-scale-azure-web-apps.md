---
title: Tutorial - Aplicativos de escala no Serviço de Aplicações Azure usando Ansible
description: Saiba como escalar uma app no Azure App Service
keywords: ansible, azul, devops, bash, playbook, Azure App Service, Web App, escala, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155923"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Tutorial: Aplicativos de escala no Serviço de Aplicações Azure usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Obtenha factos de um plano de serviço de aplicações existente
> * Aumentar o plano de Serviço de Aplicações para S2 com três trabalhadores

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplicação Azure App Service** - Se não tiver uma aplicação Azure App Service, [configure uma aplicação no Serviço de Aplicações Azure utilizando ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Escalar uma aplicação

Existem dois fluxos de trabalho para a escala: *escala e* escala para *fora*.

**Escala para cima:** Aumentar significa adquirir mais recursos. Estes recursos incluem CPU, memória, espaço em disco, VMs, e muito mais. Você escala uma app alterando o nível de preços do plano de Serviço de Aplicações a que a aplicação pertence. 
**Escala para fora:** Para escalonar significa aumentar o número de casos vm que executam a sua aplicação. Dependendo do seu nível de preços do plano de app service, pode aumentar para 20 instâncias. [A automatização](/azure/azure-monitor/platform/autoscale-get-started) permite-lhe escalar a contagem de instâncias automaticamente com base em regras e horários pré-definidos.

O código de jogadas nesta secção define o seguinte funcionamento:

* Obtenha factos de um plano de serviço de aplicações existente
* Atualize o plano de serviço da App para S2 com três trabalhadores

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook webapp_scaleup.yml
```

Depois de executar o livro de jogadas, vê a saída semelhante aos seguintes resultados:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)