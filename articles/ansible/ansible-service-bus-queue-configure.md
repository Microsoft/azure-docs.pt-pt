---
title: Tutorial - configurar filas no Azure Service Bus com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para criar uma fila do Service bus do Azure
keywords: ansible, azure, devops, bash, playbook, do service bus, fila
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 6efc11106fae18beac43ab1896733ab6bfc64dad
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230766"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutorial: Configurar as filas no Azure Service Bus com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma fila
> * Criar uma SAS plicy
> * Obter as informações de espaço de nomes
> * Obter as informações de fila
> * Revogar a política SAS da fila

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Criar a fila do Service Bus

O código do playbook de exemplo cria os seguintes recursos:
- Grupo de recursos do Azure
- Espaço de nomes do Service Bus no grupo de recursos
- Fila do Service Bus com o espaço de nomes

Guarde o manual de procedimentos seguinte como `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Criar a política SAS

R [assinatura de acesso partilhado (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseada em afirmações utilizando tokens. 

O código do playbook de exemplo cria duas políticas SAS para uma fila do Service Bus com privilégios diferentes.

Guarde o manual de procedimentos seguinte como `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Antes de executar o playbook, consulte as seguintes notas de:
- O `rights` valor representa o privilégio de um utilizador tiver com a fila. Especifique um dos seguintes valores: `manage`, `listen`, `send`, ou `listen_send`.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Obter as informações de espaço de nomes

O código do playbook de exemplo consulta as informações de espaço de nomes.

Guarde o manual de procedimentos seguinte como `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Antes de executar o playbook, consulte as seguintes notas de:
- O `show_sas_policies` valor indica se as políticas SAS no espaço de nomes especificado. Por predefinição, o valor é `False` para evitar a sobrecarga de rede adicional.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Obter as informações de fila

As informações de fila consultas código do exemplo playbook. 

Guarde o manual de procedimentos seguinte como `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Antes de executar o playbook, consulte as seguintes notas de:
- O `show_sas_policies` valor indica se as políticas SAS da fila especificada. Por predefinição, este valor é definido como `False` para evitar a sobrecarga de rede adicional.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Revogar a política SAS da fila

O código do playbook de exemplo elimina uma política SAS da fila.

Guarde o manual de procedimentos seguinte como `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados neste artigo. 

Guarde o código a seguir como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Tutorial: Configurar um tópico no Azure Service Bus com o Ansible](ansible-service-bus-topic-configure.md)