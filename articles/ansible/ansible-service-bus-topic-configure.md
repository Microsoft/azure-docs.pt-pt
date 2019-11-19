---
title: Tutorial-configurar tópicos no barramento de serviço do Azure usando o Ansible
description: Saiba como usar o Ansible para criar um tópico do barramento de serviço do Azure
keywords: Ansible, Azure, DevOps, Bash, manual, barramento de serviço, tópicos, assinaturas
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155793"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Tutorial: configurar tópicos no barramento de serviço do Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um tópico
> * Criar uma subscrição
> * Criar uma política SAS
> * Recuperar informações de namespace
> * Recuperar informações de tópico e assinatura
> * Revogar uma política SAS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Criar o tópico do barramento de serviço

O código do guia estratégico de exemplo cria os seguintes recursos:
- Grupo de recursos do Azure
- Namespace do barramento de serviço dentro do grupo de recursos
- Tópico do barramento de serviço com o namespace

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Criar a assinatura

O código do guia estratégico de exemplo cria a assinatura em um tópico do barramento de serviço. Os tópicos do barramento de serviço do Azure podem ter várias assinaturas. Um assinante de um tópico pode receber uma cópia de cada mensagem enviada para o tópico. As assinaturas são entidades nomeadas, que são permanentemente criadas, mas podem, opcionalmente, expirar.

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Criar a política SAS

Uma [SAS (assinatura de acesso compartilhado)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseado em declarações usando tokens. 

O código do guia estratégico de exemplo cria duas políticas SAS para uma fila do barramento de serviço com privilégios diferentes.

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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Recuperar informações de tópico e assinatura

O código do guia estratégico de exemplo consulta as seguintes informações:
- Informações do tópico do barramento de serviço
- Lista de detalhes da assinatura para o tópico
 
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

Antes de executar o guia estratégico, consulte as seguintes observações:
- O valor `show_sas_policies` indica se as políticas SAS devem ser mostradas na fila especificada. Por padrão, esse valor é definido como `False` para evitar a sobrecarga de rede adicional.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_list.yml
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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)