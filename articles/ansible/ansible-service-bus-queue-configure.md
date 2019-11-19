---
title: Tutorial – configurar filas no barramento de serviço do Azure usando o Ansible
description: Saiba como usar o Ansible para criar uma fila do barramento de serviço do Azure
keywords: Ansible, Azure, DevOps, Bash, manual, barramento de serviço, fila
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9903419a52be61cd7ec74214858bce81df7e727e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155823"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutorial: configurar filas no barramento de serviço do Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma fila
> * Criar um plicy SAS
> * Recuperar informações de namespace
> * Recuperar informações da fila
> * Revogar a política de SAS da fila

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Criar a fila do barramento de serviço

O código do guia estratégico de exemplo cria os seguintes recursos:
- Grupo de recursos do Azure
- Namespace do barramento de serviço dentro do grupo de recursos
- Fila do barramento de serviço com o namespace

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Criar a política SAS

Uma [SAS (assinatura de acesso compartilhado)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseado em declarações usando tokens. 

O código do guia estratégico de exemplo cria duas políticas SAS para uma fila do barramento de serviço com privilégios diferentes.

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

Antes de executar o guia estratégico, consulte as seguintes observações:
- O valor de `rights` representa o privilégio que um usuário tem com a fila. Especifique um dos seguintes valores: `manage`, `listen`, `send`ou `listen_send`.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Recuperar informações de namespace

O código do guia estratégico de exemplo consulta as informações do namespace.

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
- O valor `show_sas_policies` indica se as políticas SAS devem ser mostradas no namespace especificado. Por padrão, o valor é `False` para evitar a sobrecarga de rede adicional.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Recuperar informações da fila

O código do manual de exemplo consulta informações da fila. 

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
- O valor `show_sas_policies` indica se as políticas SAS devem ser mostradas na fila especificada. Por padrão, esse valor é definido como `False` para evitar a sobrecarga de rede adicional.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Revogar a política de SAS da fila

O código do guia estratégico de exemplo exclui uma política de SAS da fila.

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo. 

Salve o código a seguir como `cleanup.yml`:

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"] 
> [Tutorial: configurar um tópico no barramento de serviço do Azure usando o Ansible](ansible-service-bus-topic-configure.md)