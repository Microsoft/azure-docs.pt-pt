---
title: Tutorial - Implementar aplicações para conjuntos de escala de máquinas virtuais em Azure usando Ansible
description: Aprenda a usar ansible para configurar conjuntos de escala de máquinas virtuais Azure e implementar aplicação no conjunto de escala
keywords: ansible, azure, devops, bash, manual de procedimentos, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940863"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Implementar aplicações para conjuntos de escala de máquinas virtuais em Azure usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Recupere informações do anfitrião para um grupo de VMs Azure
> * Clone e construa a app de amostras
> * Instale o JRE (Java Runtime Environment) num conjunto de escala
> * Implementar a aplicação Java para um conjunto de escala

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **Git** - [git](https://git-scm.com) é usado para descarregar uma amostra java usada neste tutorial.
- **Kit de Desenvolvimento SE do Java (JDK)** - o [JDK](https://aka.ms/azure-jdks) serve para compilar o projeto Java de exemplo.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) é usado para construir o projeto Java da amostra.

## <a name="get-host-information"></a>Obter informações dos anfitriões

O código de jogadanesta secção recupera informações hospedeiras para um grupo de máquinas virtuais. O código obtém os endereços IP públicos e o equilibrador `scalesethosts` de carga dentro de um grupo de recursos especificado e cria um grupo de anfitriões nomeado em inventário.

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
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Preparar uma aplicação para implementação

O código de jogadas nesta secção usa `git` para clonar um projeto de amostra Java do GitHub e constrói o projeto. 

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

Depois de executar o livro de jogadas, vê a saída semelhante aos seguintes resultados:

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

## <a name="deploy-the-application-to-a-scale-set"></a>Implementar a aplicação num conjunto de escala

O código de jogadas nesta secção é usado para:

* Instale o JRE num grupo de anfitriões chamado`saclesethosts`
* Implante a aplicação Java para um grupo de anfitriões chamado`saclesethosts`

Há duas maneiras de obter o livro de jogadas da amostra:

* [Descarregue o](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) livro `vmss-setup-deploy.yml`de jogadas e guarde-o para .
* Crie um `vmss-setup-deploy.yml` novo ficheiro nomeado e copie-o nos seguintes conteúdos:

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

Antes de executar o livro de jogadas, consulte as seguintes notas:

* Na `vars` secção, substitua o `{{ admin_password }}` espaço reservado pela sua própria senha.
* Para utilizar o tipo de ligação SSH com senhas, instale o programa sshpass:

     Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* Em alguns ambientes, pode ver um erro ao utilizar uma senha SSH em vez de uma chave. Se receber esse erro, pode desativar a verificação `/etc/ansible/ansible.cfg` `~/.ansible.cfg`da chave do hospedeiro adicionando a seguinte linha a ou:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Execute o manual de procedimentos com o comando seguinte:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

A saída de executar o comando de manual ansible indica que a aplicação Java da amostra foi instalada no grupo anfitrião do conjunto de escala:

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

## <a name="verify-the-results"></a>Verifique os resultados

Verifique os resultados do seu trabalho navegando até ao URL do equilibrante de carga para o seu conjunto de escala:

![App Java em funcionamento numa escala definida em Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conjuntos de escala automática de máquinas virtuais em Azure usando Ansible](./ansible-auto-scale-vmss.md)