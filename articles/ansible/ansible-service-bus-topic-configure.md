---
title: Tutorial - Configurar tópicos no Ônibus de Serviço Azure usando Ansible
description: Saiba como usar o Ansible para criar um tópico de ônibus de serviço azure
keywords: ansível, azul, devops, bash, playbook, ônibus de serviço, tópicos, subscrições
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155793"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Tutorial: Configurar tópicos no ônibus de serviço azure usando Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um tópico
> * Criar uma subscrição
> * Criar uma política SAS
> * Recuperar informações sobre espaço de nome
> * Recuperar informações sobre tópicos e subscrições
> * Revogar uma política sas

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Crie o tópico do Ônibus de serviço

O código de livro de amostras cria os seguintes recursos:
- Grupo de recursos do Azure
- Espaço de nome de ônibus de serviço dentro do grupo de recursos
- Tópico de ônibus de serviço com o espaço de nome

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Criar a subscrição

O código de playbook da amostra cria a subscrição sob um tópico de Ônibus de serviço. Os tópicos do Azure Service Bus podem ter várias subscrições. Um assinante de um tópico pode receber uma cópia de cada mensagem enviada para o tópico. As assinaturas são entidades nomeadas, que são duramente criadas, mas podem expirar opcionalmente.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Criar a política SAS

A [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseado em sinistros que utiliza fichas. 

O código de playbook da amostra cria duas políticas SAS para uma fila de ônibus de serviço com diferentes privilégios.

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook servicebus_topic_policy.yml
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

Antes de executar o livro de jogadas, consulte as seguintes notas:
- O `show_sas_policies` valor indica se deve mostrar as políticas SAS no espaço de nome especificado. Por predefinição, `False` o valor é evitar despesas adicionais de rede.

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Recuperar informações sobre tópicos e subscrições

O código de livro de amostras consulta para as seguintes informações:
- Informações sobre tópicos de ônibus de serviço
- Lista de detalhes de subscrição para o tópico
 
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

Antes de executar o livro de jogadas, consulte as seguintes notas:
- O `show_sas_policies` valor indica se deve mostrar as políticas SAS sob a fila especificada. Por predefinição, este `False` valor está definido para evitar sobrecargas adicionais da rede.

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook servicebus_list.yml
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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo. 

Guarde o `cleanup.yml`seguinte código como:

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

Executar o manual `ansible-playbook` usando o comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)