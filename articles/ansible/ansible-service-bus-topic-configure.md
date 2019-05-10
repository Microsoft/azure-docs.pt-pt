---
title: Tutorial - configurar tópicos no Azure Service Bus com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para criar um tópico do Service bus do Azure
keywords: ansible, azure, devops, bash, playbook, do service bus, tópicos, subscrições
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ca8d849796520ac260d888d772c064316db68a30
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230872"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Tutorial: Configurar tópicos no Azure Service Bus com o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um tópico
> * Criar uma subscrição
> * Criar uma política SAS
> * Obter as informações de espaço de nomes
> * Obter as informações do tópico e uma subscrição
> * Uma política SAS de revogação

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Criar o tópico do Service Bus

O código do playbook de exemplo cria os seguintes recursos:
- Grupo de recursos do Azure
- Espaço de nomes do Service Bus no grupo de recursos
- Tópico do Service Bus com o espaço de nomes

Guarde o manual de procedimentos seguinte como `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Criar a subscrição

O código do playbook de exemplo cria a subscrição num tópico do Service Bus. Tópicos de Service Bus do Azure podem ter várias subscrições. Um subscritor para um tópico pode recebe uma cópia de cada mensagem enviada para o tópico. As subscrições são nomeadas entidades, que são criadas de maneira duradoura, mas podem, opcionalmente, expirar.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Guarde o manual de procedimentos seguinte como `servicebus_subscription.yml`:

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Criar a política SAS

R [assinatura de acesso partilhado (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseada em afirmações utilizando tokens. 

O código do playbook de exemplo cria duas políticas SAS para uma fila do Service Bus com privilégios diferentes.

Guarde o manual de procedimentos seguinte como `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Obter as informações do tópico e uma subscrição

As playbook código consultas de exemplo para as seguintes informações:
- Informações do tópico do Service Bus
- Lista de detalhes da subscrição para o tópico
 
Guarde o manual de procedimentos seguinte como `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Antes de executar o playbook, consulte as seguintes notas de:
- O `show_sas_policies` valor indica se as políticas SAS da fila especificada. Por predefinição, este valor é definido como `False` para evitar a sobrecarga de rede adicional.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Ansible no Azure](/azure/ansible/)