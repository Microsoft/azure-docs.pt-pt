---
title: Tutorial - Configure filas em Ônibus de Serviço Azure usando Ansible
description: Aprenda a usar ansible para criar uma fila de ônibus de serviço Azure
keywords: ansível, azul, devops, bash, playbook, ônibus de serviço, fila
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713234"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutorial: Configure as filas no ônibus de serviço azure usando Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma fila
> * Criar uma plicy SAS
> * Recuperar informações sobre espaço de nome
> * Recuperar informações sobre fila
> * Revogar a política sas da fila

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Crie a fila de ônibus de serviço

O código de livro de amostras cria os seguintes recursos:
- Grupo de recursos do Azure
- Espaço de nome de ônibus de serviço dentro do grupo de recursos
- Fila de ônibus de serviço com o espaço de nome

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

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Criar a política SAS

A [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseado em sinistros que utiliza fichas. 

O código de playbook da amostra cria duas políticas SAS para uma fila de ônibus de serviço com diferentes privilégios.

Guarde o manual de procedimentos seguinte como `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
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

Antes de executar o guia estratégico, consulte as seguintes observações:
- O valor `rights` representa o privilégio que um utilizador tem com a fila. Especifique um dos seguintes valores: `manage`, `listen`, `send`ou `listen_send`.

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Recuperar informações sobre espaço de nome

O código de livro de amostras consulta a informação do espaço de nome.

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

Antes de executar o guia estratégico, consulte as seguintes observações:
- O valor `show_sas_policies` indica se deve mostrar as políticas SAS no âmbito do espaço de nome especificado. Por predefinição, o valor é `False` para evitar sobrecargas adicionais da rede.

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Recuperar informações sobre fila

O código de livro de amostras consulta informações de fila. 

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

Antes de executar o guia estratégico, consulte as seguintes observações:
- O valor `show_sas_policies` indica se deve mostrar as políticas sas sob a fila especificada. Por predefinição, este valor está definido para `False` para evitar sobrecargas adicionais da rede.

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Revogar a política sas da fila

O código de livro de amostras elimina uma política SAS de fila.

Guarde o manual de procedimentos seguinte como `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo. 

Guarde o seguinte código `cleanup.yml`:

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

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Tutorial: Configure um tópico no Ônibus de Serviço Azure usando Ansible](ansible-service-bus-topic-configure.md)