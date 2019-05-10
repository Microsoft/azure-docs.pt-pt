---
title: Tutorial - dimensionamento de máquinas virtuais de dimensionamento automático define no Azure com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para dimensionar conjuntos de dimensionamento de máquinas virtuais com o dimensionamento automático no Azure
keywords: ansible, azure, devops, bash, playbook, dimensionamento, dimensionamento automático, máquina virtual, o conjunto de dimensionamento de máquina virtual, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f2cd66b7460fc6fe48cb55f45bf4bc309ae054c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231274"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Conjuntos de dimensionamento de máquina virtual de dimensionamento automático no Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

O recurso de ajustar automaticamente o número de instâncias VM é chamado [dimensionamento automático](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). A vantagem do dimensionamento automático é o que reduz os custos de gestão para monitorizar e otimizar o desempenho da sua aplicação. Dimensionamento automático pode ser configurado em resposta ao pedido ou com base numa agenda definida. A utilização do Ansible, pode especificar as regras de dimensionamento automático que definem o desempenho aceitável para uma experiência de cliente positivo.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definir um perfil de dimensionamento automático
> * Dimensionamento automático com base numa agenda periódica
> * Dimensionamento automático com base no desempenho da aplicação
> * Obter as informações de definições de dimensionamento automático 
> * Desativar uma definição de dimensionamento automático

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base numa agenda

Para ativar o dimensionamento automático num conjunto de dimensionamente, tem primeiro que definir um perfil de dimensionamento automático. Este perfil define a capacidade predefinida, máxima e mínima do conjunto de dimensionamento. Estes limites permitem-lhe controlar o custo por não continuamente a criar instâncias de VM e o balanceamento de um desempenho aceitável com um número mínimo de instâncias que permanecem num evento de dimensionamento. 

Ansible permite-lhe dimensionar seus conjuntos de dimensionamento numa data específica ou uma agenda periódica.

O código do playbook nesta secção aumenta o número de instâncias VM a três às 10:00 sempre de segunda.

Guarde o manual de procedimentos seguinte como `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Dimensionamento automático com base nos dados de desempenho

Se a exigência da aplicação aumenta, a carga nas instâncias de VM no seu dimensionamento define aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Ansible permite-lhe controlar que métricas para monitorizar, como a utilização da CPU, utilização do disco e tempo de carregamento da aplicação. Pode reduzir horizontalmente e horizontalmente no dimensionamento conjuntos de dimensionamento de desempenho com base nos limiares de métricas, por uma agenda periódica ou por uma data específica. 

O código do playbook nesta secção verifica se a carga de trabalho de CPU anteriores de 10 minutos às 9:00 18 sempre de segunda. 

Com base nas métricas de percentagem de CPU, o playbook faz uma das seguintes ações:

- Aumenta horizontalmente o número de instâncias de VM para quatro
- Aumenta o número de instâncias VM para um de

Guarde o manual de procedimentos seguinte como `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Obtenha informações de definições de dimensionamento automático 

O código do playbook nesta secção utiliza o `azure_rm_autoscale_facts` módulo para obter os detalhes da definição de dimensionamento automático.

Guarde o manual de procedimentos seguinte como `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Desativar as definições de dimensionamento automático

Existem duas formas de desativar as definições de dimensionamento automático. É uma forma alterar o `enabled` chave de `true` para `false`. A segunda maneira é eliminar a definição.

O código do playbook nesta secção elimina a definição de dimensionamento automático. 

Guarde o manual de procedimentos seguinte como `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Executar o playbook com o `ansible-playbook` comando:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Tutorial: Os conjuntos de imagem personalizada de atualização de dimensionamento de máquina virtual do Azure com o Ansible](./ansible-vmss-update-image.md)