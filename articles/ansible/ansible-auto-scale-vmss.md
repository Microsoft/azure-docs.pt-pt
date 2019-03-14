---
title: Dimensionar automaticamente um conjunto de dimensionamento no Azure com o Ansible
description: Saiba como utilizar o Ansible para dimensionar um conjunto de dimensionamento com o dimensionamento automático no Azure
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, dimensionamento, dimensionamento automático, máquina virtual, o conjunto de dimensionamento de máquina virtual, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 578ad3207f62e74805be056ca11d3bd9b46513da
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792434"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Dimensionar automaticamente um conjunto de dimensionamento no Azure com o Ansible
O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Pode utilizar o Ansible para gerir o seu conjunto de dimensionamento de máquinas virtuais (VMSS) no Azure, tal como faria em qualquer outro recurso do Azure. 

Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação. Neste artigo, irá criar uma definição de dimensionamento automático e associá-la a um conjunto de dimensionamento de máquina virtual existente. Na definição de dimensionamento automático, pode configurar uma regra para ampliar ou reduzir horizontalmente conforme desejado.

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Um conjunto de dimensionamento de máquina virtual do Azure existente. -Se não tiver um, [conjuntos de dimensionamento de máquina virtual de criar no Azure com o Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> O Ansible 2.7 é necessário para executar os manuais de procedimentos de exemplo neste tutorial. 

## <a name="auto-scale-based-on-a-schedule"></a>Dimensionamento automático com base numa agenda   
Para ativar o dimensionamento automático num conjunto de dimensionamente, tem primeiro que definir um perfil de dimensionamento automático. Este perfil define a capacidade predefinida, máxima e mínima do conjunto de dimensionamento. Estes limites permitem-lhe controlar o custo por não continuamente a criar instâncias de VM e o balanceamento de um desempenho aceitável com um número mínimo de instâncias que permanecem num evento de dimensionamento. 

Pode reduzir horizontalmente e dimensionar horizontalmente em conjuntos de dimensionamento de Máquina Virtual por um agendamento periódico, ou por uma data específica. Esta secção apresenta um playbook de Ansible de exemplo que cria uma definição de dimensionamento automático aumenta o número de instâncias de VM para três conjuntos de dimensionamento no 10:00 de todas as segundas-feiras, fuso horário do Pacífico. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
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

Guardar este manual de comunicação social como *vmss-auto-scale.yml*. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Dimensionamento automático com base nos dados de desempenho
Se a exigência da aplicação aumenta, a carga nas instâncias de VM no seu dimensionamento define aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar, e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Pode reduzir horizontalmente e dimensionar horizontalmente em conjuntos de dimensionamento de Máquina Virtual com base nos limiares de métricas de desempenho, por uma agenda periódica ou por uma data específica. Esta secção apresenta um playbook de Ansible de exemplo que verifica a carga de trabalho nos últimos 10 minutos no 18:00 de todas as segundas-feiras, fuso horário do Pacífico e aumenta horizontalmente o número de instâncias VM em seus conjuntos de dimensionamento para quatro ou é reduzido horizontalmente uma instância, de acordo com a percentagem de CPU métricas. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
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

Guardar este manual de comunicação social como *vmss-auto-dimensionamento-metrics.yml*. Para executar o manual de procedimentos do Ansible, utilize o comando **ansible playbook** da seguinte forma:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Obter informações para as definições de dimensionamento automático existente
Pode obter detalhes de qualquer definição de dimensionamento automático através de *azure_rm_autoscale_facts* módulo com o playbook da seguinte forma:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Desative as definições de dimensionamento automático
Pode desativar a definição de dimensionamento automático, alterando `enabled: true` para `enabled: false`, ou eliminar as definições de dimensionamento automático com o playbook da seguinte forma:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Define o playbook de exemplo do Ansible para dimensionamento de máquinas virtuais](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)