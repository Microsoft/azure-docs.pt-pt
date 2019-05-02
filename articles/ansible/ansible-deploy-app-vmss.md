---
title: Tutorial - implementar aplicações em conjuntos de dimensionamento de máquinas virtuais no Azure com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para configurar conjuntos de dimensionamento de máquina virtual do Azure e implementar a aplicação num conjunto de dimensionamento
keywords: ansible, azure, devops, bash, manual de procedimentos, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3c45a0bc5bbabeb6f4511140f83b08fd2943127c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694939"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Implementar aplicações em conjuntos de dimensionamento de máquinas virtuais no Azure com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Obter informações do anfitrião para um grupo de VMs do Azure
> * Clonar e criar a aplicação de exemplo
> * Instalar o JRE (Java Runtime Environment) num conjunto de dimensionamento
> * Implementar a aplicação de Java num conjunto de dimensionamento

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- [!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- O **git** - [git](https://git-scm.com) serve para transferir um exemplo do Java utilizado neste tutorial.
- **Kit de Desenvolvimento SE do Java (JDK)** - o [JDK](https://aka.ms/azure-jdks) serve para compilar o projeto Java de exemplo.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) é usado para compilar o projeto de Java de exemplo.

## <a name="get-host-information"></a>Obter informações dos anfitriões

O código do playbook nesta secção obtém informações do anfitrião para um grupo de máquinas virtuais. O código obtém os endereços IP públicos e carregar balanceador dentro de um grupo de recursos especificado e cria um grupo de anfitriões com o nome `scalesethosts` no inventário.

Guarde o manual de procedimentos de exemplo seguinte como `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Preparar uma aplicação para implementação

O código do playbook nesta secção usa `git` para clonar um projeto de exemplo de Java do GitHub e cria o projeto. 

Guarde o manual de procedimentos seguinte como `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Execute o manual de procedimentos do Ansible de exemplo com o seguinte comando:

  ```bash
  ansible-playbook app.yml
  ```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Implementar a aplicação para um conjunto de dimensionamento

O código do playbook nesta secção é utilizado para:

* Instalar o JRE num grupo de anfitriões com o nome `saclesethosts`
* Implementar a aplicação de Java num grupo de anfitriões com o nome `saclesethosts`

Existem duas formas de obter o playbook de exemplo:

* [Transferir o manual de comunicação social](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) e guardá-lo para `vmss-setup-deploy.yml`.
* Crie um novo ficheiro designado `vmss-setup-deploy.yml` e copie no seguinte conteúdo:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Antes de executar o playbook, consulte as seguintes notas de:

* Na `vars` secção, substitua o `{{ admin_password }}` marcador de posição pela sua própria palavra-passe.
* Para utilizar o ssh tipo de ligação com palavras-passe, instalar o programa de sshpass:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* Em alguns ambientes, poderá ver um erro sobre a utilização de uma palavra-passe SSH, em vez de uma chave. Se receber este erro, pode desativar a verificação, adicionando a seguinte linha para a chave de anfitrião `/etc/ansible/ansible.cfg` ou `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Execute o manual de procedimentos com o comando seguinte:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

O resultado da execução do comando do ansible playbook indica que foi instalada a aplicação de Java de exemplo para o grupo de anfitriões do conjunto de dimensionamento:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Verificar os resultados

Verifique se os resultados do seu trabalho navegando para o URL do Balanceador de carga para o conjunto de dimensionamento:

![Aplicação de Java em execução em conjuntos de dimensionamento no Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conjuntos de dimensionamento de máquina virtual de dimensionamento automático no Azure com o Ansible](./ansible-auto-scale-vmss.md)