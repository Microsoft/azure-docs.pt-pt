---
title: Implementar aplicações em conjuntos de dimensionamento de máquinas virtuais no Azure com o Ansible
description: Aprenda a utilizar o Ansible para configurar um conjunto de dimensionamento de máquinas virtuais e implementar aplicações no mesmo no Azure
ms.service: azure
keywords: ansible, azure, devops, bash, manual de procedimentos, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: 2214dd9505dff86ac26f01967a360140dee0069f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791737"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Implementar aplicações em conjuntos de dimensionamento de máquinas virtuais no Azure com o Ansible
O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. Pode utilizar o Ansible para implementar as suas aplicações no Azure. Este artigo mostra-lhe como implementar uma aplicação do Java num conjunto de dimensionamento de máquinas virtuais do Azure (VMSS).

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Conjunto de dimensionamento de máquinas virtuais** – se ainda não tem um conjunto de dimensionamento de máquinas virtuais, pode [criar um conjunto de dimensionamento de máquinas virtuais com o Ansible](ansible-create-configure-vmss.md).
- O **git** - [git](https://git-scm.com) serve para transferir um exemplo do Java utilizado neste tutorial.
- **Kit de Desenvolvimento SE do Java (JDK)** - o [JDK](https://aka.ms/azure-jdks) serve para compilar o projeto Java de exemplo.
- **Ferramentas de compilação do Apache Maven** – As [ferramentas de compilação do Apache Maven](https://maven.apache.org/download.cgi) são utilizadas para compilar o projeto de Java de exemplo.

> [!Note]
> O Ansible 2.6 é necessário para executar os manuais de procedimentos de exemplo neste tutorial.

## <a name="get-host-information"></a>Obter informações dos anfitriões

Esta secção ilustra como utilizar o Ansible para obter informações do anfitrião para um grupo de máquinas virtuais do Azure. Abaixo encontra-se um manual de procedimentos do Ansible de exemplo. O código obtém os endereços IP públicos e o balanceador de carga no grupo de recursos especificado, e cria um grupo de anfitriões designado **scalesethosts** no inventário.

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

Nesta secção, utilize o git para clonar um projeto de exemplo do Java do GitHub e compile o projeto. Guarde o manual de procedimentos seguinte como `app.yml`:

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

O resultado do comando do manual de procedimentos do ansible apresenta algo semelhante ao seguinte, onde vê que criou a aplicação de exemplo clonada do GitHub:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Implementar a aplicação no VMSS

A seguinte secção num manual de procedimentos do Ansible instala o JRE (Java Runtime Environment) num grupo de anfitriões com o nome **saclesethosts** e implementa a aplicação do Java num grupo de anfitriões com o nome **saclesethosts**:

(Altere o `admin_password` pela sua própria palavra-passe.)

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

Pode guardar o manual de procedimentos do Ansible do exemplo anterior como `vmss-setup-deploy.yml` ou [transferir o manual de procedimentos de exemplo completo](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss).

Para utilizar o tipo de ligação ssh com palavras-passe, tem de instalar o programa sshpass.
  - Para o Ubuntu 16.04, execute o comando `apt-get install sshpass`.
  - Para o CentOSo 7.4, execute o comando `yum install sshpass`.

Poderá ver um erro como **Não pode utilizar uma palavra-passe SSH em vez de uma chave porque a verificação da Chave de Anfitrião está ativada e o sshpass não o suporta. Adicione a impressão digital deste anfitrião ao seu ficheiro known_hosts para gerir este anfitrião.** Se vir este erro, pode desativar a verificação da chave do anfitrião, ao adicionar a seguinte linha ao ficheiro `/etc/ansible/ansible.cfg` ou ao ficheiro `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Execute o manual de procedimentos com o comando seguinte:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

O resultado da execução do comando ansible-playbook indica que a aplicação do Java de exemplo foi instalada no grupo de anfitriões do conjunto de dimensionamento de máquinas virtuais:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Parabéns! A aplicação está agora em execução no Azure. Agora pode navegar para o URL do balanceador de carga para o conjunto de dimensionamento de máquinas virtuais:

![Aplicação do Java em execução num conjunto de dimensionamento de máquinas virtuais no Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Dimensionar automaticamente um conjunto de dimensionamento com o Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)
