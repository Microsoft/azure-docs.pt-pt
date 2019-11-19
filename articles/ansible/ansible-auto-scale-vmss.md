---
title: Tutorial – conjuntos de dimensionamento de máquinas virtuais de dimensionamento automático no Azure usando Ansible
description: Saiba como usar o Ansible para dimensionar conjuntos de dimensionamento de máquinas virtuais com dimensionamento automático no Azure
keywords: Ansible, Azure, DevOps, Bash, manual, escala, dimensionamento automático, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156827"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: dimensionamento automático de conjuntos de dimensionamento de máquinas virtuais no Azure usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

O recurso de ajustar automaticamente o número de instâncias de VM é chamado de [dimensionamento automático](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). O benefício do dimensionamento automático é que ele reduz a sobrecarga de gerenciamento para monitorar e otimizar o desempenho do seu aplicativo. O dimensionamento automático pode ser configurado em resposta à demanda ou em um agendamento definido. Usando o Ansible, você pode especificar as regras de dimensionamento automático que definem o desempenho aceitável para uma experiência de cliente positiva.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definir um perfil de dimensionamento automático
> * Dimensionamento automático com base em uma agenda recorrente
> * Dimensionamento automático com base no desempenho do aplicativo
> * Recuperar informações de configurações de autoescala 
> * Desabilitar uma configuração de dimensionamento automático

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base em uma agenda

Para ativar o dimensionamento automático num conjunto de dimensionamente, tem primeiro que definir um perfil de dimensionamento automático. Este perfil define a capacidade predefinida, máxima e mínima do conjunto de dimensionamento. Esses limites permitem controlar o custo não criando continuamente instâncias de VM e equilibram o desempenho aceitável com um número mínimo de instâncias que permanecem em um evento de redução. 

O Ansible permite dimensionar seus conjuntos de dimensionamento em uma data específica ou em um agendamento recorrente.

O código de guia estratégico nesta seção aumenta o número de instâncias de VM para três às 10:00 todas as segundas-feiras.

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Dimensionamento automático baseado em dados de desempenho

Se a demanda do aplicativo aumentar, a carga nas instâncias de VM nos conjuntos de dimensionamento aumentará. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. O Ansible permite que você controle quais métricas monitorar, como uso da CPU, uso do disco e tempo de carregamento do aplicativo. Você pode reduzir horizontalmente e escalar horizontalmente em conjuntos de dimensionamento com base nos limites de métrica de desempenho, por um agendamento recorrente ou por uma data específica. 

O código do guia estratégico nesta seção verifica a carga de trabalho da CPU dos 10 minutos anteriores às 18:00 todas as segundas-feiras. 

Com base nas métricas de percentual de CPU, o guia estratégico realiza uma das seguintes ações:

- Dimensiona o número de instâncias de VM para quatro
- Dimensiona o número de instâncias de VM para uma

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Obter informações de configurações de dimensionamento automático 

O código de guia estratégico nesta seção usa o módulo `azure_rm_autoscale_facts` para recuperar os detalhes da configuração de dimensionamento automático.

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Desabilitar configurações de dimensionamento automático

Há duas maneiras de desabilitar as configurações de dimensionamento automático. Uma delas é alterar a chave de `enabled` de `true` para `false`. A segunda maneira é excluir a configuração.

O código do guia estratégico nesta seção exclui a configuração de dimensionamento automático. 

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Tutorial: atualizar imagem personalizada de conjuntos de dimensionamento de máquinas virtuais do Azure usando Ansible](./ansible-vmss-update-image.md)