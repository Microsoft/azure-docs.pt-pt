---
title: Tutorial-configurar inventários dinâmicos de seus recursos do Azure usando o Ansible
description: Saiba como usar o Ansible para gerenciar seus inventários dinâmicos do Azure
keywords: Ansible, Azure, DevOps, Bash, cloudshell, inventário dinâmico
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/23/2019
ms.openlocfilehash: 6d520518e7180f69ee7293523dd40c8158dcfb99
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990676"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutorial: configurar inventários dinâmicos de seus recursos do Azure usando o Ansible

O Ansible pode ser usado para efetuar pull de informações de inventário de várias fontes (incluindo fontes de nuvem, como o Azure) em um *inventário dinâmico*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configure duas máquinas virtuais de teste. 
> * Marcar uma das máquinas virtuais
> * Instalar o Nginx nas máquinas virtuais marcadas
> * Configurar um inventário dinâmico que inclui os recursos do Azure configurados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Criar as VMs de teste

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Crie um grupo de recursos do Azure para manter as máquinas virtuais para este tutorial.

    > [!IMPORTANT]  
    > O grupo de recursos do Azure que você cria nesta etapa deve ter um nome totalmente em letras minúsculas. Caso contrário, a geração do inventário dinâmico falhará.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Crie duas máquinas virtuais do Linux no Azure usando uma das seguintes técnicas:

    - **Guia estratégico de Ansible** – o artigo [criar uma máquina virtual básica no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm) ilustra como criar uma máquina virtual de um guia estratégico do Ansible. Se você usar um guia estratégico para definir uma ou ambas as máquinas virtuais, verifique se a conexão SSH é usada em vez de uma senha.

    - **CLI do Azure** -emita cada um dos comandos a seguir na Cloud Shell para criar as duas máquinas virtuais:

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

Você pode [usar marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorias definidas pelo usuário. 

### <a name="using-ansible-version--28"></a>Usando o Ansible versão < 2,8
Insira o comando [AZ Resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) a seguir para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Usando o Ansible versão > = 2,8
Insira o comando [AZ Resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) a seguir para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Gerar um inventário dinâmico

Quando você tiver suas máquinas virtuais definidas (e marcadas), é hora de gerar o inventário dinâmico.

### <a name="using-ansible-version--28"></a>Usando o Ansible versão < 2,8

O Ansible fornece um script Python chamado [azure_rm. py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que gera um inventário dinâmico dos recursos do Azure. As etapas a seguir explicam como usar o script `azure_rm.py` para se conectar às suas duas máquinas virtuais do Azure de teste:

1. Use o comando GNU `wget` para recuperar o script de `azure_rm.py`:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Use o comando `chmod` para alterar as permissões de acesso para o script `azure_rm.py`. O comando a seguir usa o parâmetro `+x` para permitir a execução (em execução) do arquivo especificado (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Use o [comando Ansible](https://docs.ansible.com/ansible/2.4/ansible.html) para se conectar ao seu grupo de recursos: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Uma vez conectado, você verá resultados semelhantes à seguinte saída:

    ```Output
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

### <a name="ansible-version--28"></a>Versão Ansible > = 2,8

A partir do Ansible 2,8, o Ansible fornece um [plug-in de inventário dinâmico do Azure](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). As etapas a seguir o orientarão no uso do plug-in:

1. O plug-in de inventário requer um arquivo de configuração. O arquivo de configuração deve terminar em `azure_rm` e ter uma extensão de `yml` ou `yaml`. Para este exemplo de tutorial, salve o seguinte guia estratégico como `myazure_rm.yml`:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Execute o seguinte comando para executar ping em VMs no grupo de recursos:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Ao executar o comando anterior, você pode receber o seguinte erro:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Se você receber o erro "verificação de chave de host", adicione a seguinte linha ao arquivo de configuração Ansible. O arquivo de configuração Ansible está localizado em `/etc/ansible/ansible.cfg` ou `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Ao executar o guia estratégico, você verá resultados semelhantes à seguinte saída:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Habilitar a marca de VM

### <a name="if-youre-using-ansible--28"></a>Se você estiver usando o Ansible < 2,8,

- Depois de definir uma marca, você precisará "habilitar" essa marca. Uma maneira de habilitar uma marca é exportando a marca para uma variável de ambiente `AZURE_TAGS` por meio do comando `export`:

    ```azurecli-interactive
    export AZURE_TAGS=nginx
    ```
    
- Execute o seguinte comando:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Agora você vê apenas uma máquina virtual (aquela cuja marca corresponde ao valor exportado para a variável de ambiente `AZURE_TAGS`):

    ```Output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Se você estiver usando Ansible > = 2,8

- Execute o comando `ansible-inventory -i myazure_rm.yml --graph` para obter a seguinte saída:

    ```Output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Você também pode executar o seguinte comando para testar a conexão com a VM Nginx:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurar o Nginx na VM marcada

A finalidade das marcas é habilitar a capacidade de trabalhar de forma rápida e fácil com subgrupos de suas máquinas virtuais. Por exemplo, digamos que você deseja instalar o Nginx somente em máquinas virtuais às quais você atribuiu uma marca de `nginx`. As etapas a seguir ilustram como é fácil fazer isso:

1. Crie um arquivo chamado `nginx.yml`:

   ```azurecli-interactive
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
            apt: pkg=nginx state=installed
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Salve o arquivo e saia do editor.

1. Execute o guia estratégico usando o comando `ansible-playbook`:

   - Ansible < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible > = 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Depois de executar o guia estratégico, você verá uma saída semelhante aos seguintes resultados:

    ```Output
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

## <a name="test-nginx-installation"></a>Testar a instalação do Nginx

Esta seção ilustra uma técnica para testar se o Nginx está instalado em sua máquina virtual.

1. Use o comando [AZ VM List-IP-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) para recuperar o endereço IP da máquina virtual `ansible-inventory-test-vm1`. O valor retornado (o endereço IP da máquina virtual) é usado como o parâmetro para o comando SSH para se conectar à máquina virtual.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Enquanto estiver conectado à máquina virtual `ansible-inventory-test-vm1`, execute o comando [Nginx-v](https://nginx.org/en/docs/switches.html) para determinar se o Nginx está instalado.

    ```azurecli-interactive
    nginx -v
    ```

1. Depois de executar o comando `nginx -v`, você verá a versão Nginx (segunda linha) que indica que o Nginx está instalado.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Clique na combinação de teclado `<Ctrl>D` para desconectar a sessão SSH.

1. Fazer as etapas anteriores para a máquina virtual `ansible-inventory-test-vm2` produz uma mensagem informativa indicando onde você pode obter Nginx (o que significa que você não o tem instalado neste ponto):

    ```Output
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
> [Início rápido: configurar máquinas virtuais do Linux no Azure usando o Ansible](/azure/virtual-machines/linux/ansible-create-vm)
