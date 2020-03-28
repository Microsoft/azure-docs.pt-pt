---
title: Tutorial - Conjuntos de escala automática de máquinas virtuais em Azure usando Ansible
description: Aprenda a usar conjuntos de escala virtual de máquinas virtuais com escala automática em Azure
keywords: ansible, azul, devops, bash, playbook, escala, escala automática, máquina virtual, conjunto de escala de máquina virtual, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156827"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Conjuntos de escala automática de máquinas virtuais em Azure usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

A função de ajustar automaticamente o número de instâncias vm chama-se [escala automática](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). O benefício da escala automática é que reduz a despesa de gestão para monitorizar e otimizar o desempenho da sua aplicação. A escala automática pode ser configurada em resposta à procura ou num horário definido. Utilizando o Ansible, pode especificar as regras de escala automática que definem o desempenho aceitável para uma experiência positiva do cliente.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definir um perfil de dimensionamento automático
> * Escala automática com base num horário recorrente
> * Escala automática com base no desempenho da aplicação
> * Recuperar informações sobre configurações de escala automática 
> * Desative uma definição de escala automática

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Escala automática com base num horário

Para ativar o dimensionamento automático num conjunto de dimensionamente, tem primeiro que definir um perfil de dimensionamento automático. Este perfil define a capacidade predefinida, máxima e mínima do conjunto de dimensionamento. Estes limites permitem controlar os custos não criando continuamente casos de VM, e equilibrar o desempenho aceitável com um número mínimo de instâncias que permanecem em um evento de escala. 

O Ansible permite-lhe escalar os seus conjuntos de escala numa data específica ou num horário recorrente.

O código de jogadanesta secção aumenta o número de casos de VM para três às 10:00 todas as segundas-feiras.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Escala automática com base em dados de desempenho

Se a procura de aplicação aumentar, a carga nas instâncias VM na sua escala aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. O Ansible permite-lhe controlar quais as métricas a monitorizar, tais como o uso do CPU, o uso do disco e o tempo de carregamento de aplicações. Pode escalar e escalar em conjuntos de escala com base em limiares métricos de desempenho, por um calendário recorrente, ou por uma data específica. 

O código de jogadas nesta secção verifica a carga de trabalho da CPU durante os 10 minutos anteriores às 18:00 todas as segundas-feiras. 

Com base nas métricas percentuais do CPU, o livro de jogadas faz uma das seguintes ações:

- Escala o número de casos vm para quatro
- Escalas no número de casos vm para um

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Obtenha informações sobre configurações de escala automática 

O código de jogadas `azure_rm_autoscale_facts` nesta secção utiliza o módulo para recuperar os detalhes da definição de escala automática.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Desativar as definições de escala automática

Existem duas formas de desativar as definições de escala automática. Uma maneira é `enabled` mudar `true` a `false`chave de . A segunda forma é apagar a definição.

O código de jogadas nesta secção elimina a definição de escala automática. 

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

Executar o manual `ansible-playbook` usando o comando:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Tutorial: Atualizar imagem personalizada de conjuntos de escala de máquinas virtuais Azure usando Ansible](./ansible-vmss-update-image.md)