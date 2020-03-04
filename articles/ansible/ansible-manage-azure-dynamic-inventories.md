---
title: Tutorial - Configure inventários dinâmicos dos seus recursos Azure usando Ansible
description: Aprenda a usar ansible para gerir os seus inventários dinâmicos Azure
keywords: ansível, azul, devops, bash, cloudshell, inventário dinâmico
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247861"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutorial: Configure inventários dinâmicos dos seus recursos Azure usando Ansible

O ansible pode ser usado para puxar informações de inventário de várias fontes (incluindo fontes em nuvem como o Azure) para um *inventário dinâmico*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configure duas máquinas virtuais de teste. 
> * Marque uma das máquinas virtuais
> * Instale Nginx nas máquinas virtuais marcadas
> * Configure um inventário dinâmico que inclua os recursos Azure configurados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Criar os VMs de teste

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Crie um grupo de recursos Azure para manter as máquinas virtuais para este tutorial.

    > [!IMPORTANT]  
    > O grupo de recursos Azure que cria neste passo deve ter um nome inteiramente minúsculo. Caso contrário, a geração do inventário dinâmico falhará.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Crie duas máquinas virtuais Linux em Azure utilizando uma das seguintes técnicas:

    - **Livro de jogadas ansível** - O artigo, [Criar uma máquina virtual básica em Azure com Ansible](./ansible-create-vm.md) ilustra como criar uma máquina virtual a partir de um livro de ansible. Se utilizar um manual para definir uma ou ambas as máquinas virtuais, certifique-se de que a ligação SSH é utilizada em vez de uma palavra-passe.

    - **Azure CLI** - Emita cada um dos seguintes comandos na Cloud Shell para criar as duas máquinas virtuais:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Etiquetar uma VM

Pode [utilizar tags para organizar os seus recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorias definidas pelo utilizador. 

### <a name="using-ansible-version--28"></a>Usando a versão Ansible < 2.8
Introduza o comando de etiqueta de [recurso az](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) aseguinte para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Utilizando a versão Ansible >= 2.8
Introduza o comando de etiqueta de [recurso az](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) aseguinte para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Gerar um inventário dinâmico

Uma vez definidas (e marcadas as suas máquinas virtuais), é hora de gerar o inventário dinâmico.

### <a name="using-ansible-version--28"></a>Usando a versão Ansible < 2.8

Ansible fornece um script Python chamado [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que gera um inventário dinâmico dos seus recursos Azure. Os seguintes passos passam por você usando o roteiro `azure_rm.py` para ligar às suas duas máquinas virtuais Azure de teste:

1. Utilize o comando `wget` GNU para recuperar o `azure_rm.py` script:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Utilize o comando `chmod` para alterar as permissões de acesso ao `azure_rm.py` script. O seguinte comando utiliza o parâmetro `+x` para permitir a execução (em execução) do ficheiro especificado (`azure_rm.py`):

    ```python
    chmod +x azure_rm.py
    ```

1. Utilize o [comando ansível](https://docs.ansible.com/ansible/2.4/ansible.html) para se ligar ao seu grupo de recursos: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Uma vez ligado, vê-se resultados semelhantes aos seguintes resultados:

    ```output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Versão ansível >= 2.8

Começando com Ansible 2.8, ansible fornece um [plug-in](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py)de inventário dinâmico Azure . Os seguintes passos passam por você através do plug-in:

1. O plug-in de inventário requer um ficheiro de configuração. O ficheiro de configuração deve terminar em `azure_rm` e ter uma extensão de `yml` ou `yaml`. Para este exemplo tutorial, guarde o seguinte livro como `myazure_rm.yml`:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Executar o seguinte comando para ping VMs no grupo de recursos:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Ao executar o comando anterior, pode receber o seguinte erro:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Se receber o erro de verificação da "chave de anfitrião", adicione a seguinte linha ao ficheiro de configuração Ansible. O ficheiro de configuração Ansible está localizado em `/etc/ansible/ansible.cfg` ou `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Ao executar o livro de jogadas, vê resultados semelhantes aos seguintes resultados:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Ativar a etiqueta VM

### <a name="if-youre-using-ansible--28"></a>Se estiver a usar Ansible < 2.8,

- Depois de definir uma etiqueta, precisa de "ativar" essa etiqueta. Uma forma de permitir uma etiqueta é exportando a etiqueta para uma variável ambiente `AZURE_TAGS` através do comando `export`:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Execute o seguinte comando:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Agora vê-se apenas uma máquina virtual (aquela cuja etiqueta corresponde ao valor exportado para a variável ambiente `AZURE_TAGS`):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Se estiver a usar Ansible >= 2.8

- Executar o comando `ansible-inventory -i myazure_rm.yml --graph` para obter a seguinte saída:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Também pode executar o seguinte comando para testar a ligação ao Nginx VM:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurar Nginx no VM marcado

O objetivo das etiquetas é permitir a capacidade de trabalhar de forma rápida e fácil com subgrupos das suas máquinas virtuais. Por exemplo, digamos que só quer instalar Nginx em máquinas virtuais às quais atribuiu uma etiqueta de `nginx`. Os seguintes passos ilustram como isso é fácil de realizar:

1. Crie um ficheiro chamado `nginx.yml`:

   ```console
   code nginx.yml
   ```

1. Cole o seguinte código de exemplo no editor:

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Guarde o ficheiro e saia do editor.

1. Executar o manual usando o comando `ansible-playbook`:

   - Ansible < 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2.8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Depois de executar o livro de jogadas, vê a saída semelhante aos seguintes resultados:

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testar a instalação nginx

Esta secção ilustra uma técnica para testar que o Nginx está instalado na sua máquina virtual.

1. Utilize o comando [az vm list-ip-addresss](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) para recuperar o endereço IP da máquina virtual `ansible-inventory-test-vm1`. O valor devolvido (o endereço IP da máquina virtual) é então utilizado como parâmetro do comando SSH para ligar à máquina virtual.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Durante a ligação à `ansible-inventory-test-vm1` máquina virtual, execute o comando [nginx-v](https://nginx.org/en/docs/switches.html) para determinar se o Nginx está instalado.

    ```console
    nginx -v
    ```

1. Uma vez executado o comando `nginx -v`, você vê a versão Nginx (segunda linha) que indica que Nginx está instalado.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Clique na combinação de teclado `<Ctrl>D` para desligar a sessão SSH.

1. Fazer os passos anteriores para o `ansible-inventory-test-vm2` máquina virtual fornece uma mensagem informativa indicando onde pode obter Nginx (o que implica que não a tem instalado neste momento):

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configure máquinas virtuais Linux em Azure usando Ansible](./ansible-create-vm.md)