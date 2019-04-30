---
title: Tutorial - configurar os inventários dinâmicos dos seus recursos do Azure com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para gerir os seus inventários dinâmicos do Azure
keywords: ansible, azure, devops, bash, cloudshell, inventário dinâmicas
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: bdd78747505664c0824fffbd41a692818000193f
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764425"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutorial: Configurar os inventários dinâmicos dos seus recursos do Azure com o Ansible

Ansible pode ser utilizado para extrair informações de inventário a partir de várias origens (incluindo origens na cloud, como o Azure) para um *inventário dinâmicas*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configure duas máquinas de virtuais de teste. 
> * Marca uma das máquinas virtuais
> * Instalar o Nginx nas máquinas virtuais marcadas
> * Configurar um inventário dinâmicas que inclui os recursos do Azure configurados

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Criar o teste de VMs

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Crie um grupo de recursos do Azure para manter as máquinas virtuais para este tutorial.

    > [!IMPORTANT]  
    > O grupo de recursos do Azure que criar neste passo tem de ter um nome que é totalmente em minúsculas. Caso contrário, a geração do inventário dinâmica irá falhar.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Crie duas máquinas virtuais do Linux no Azure através de uma das seguintes técnicas:

    - **Manual de comunicação do Ansible** -o artigo [criar uma máquina virtual básica no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm) ilustra como criar uma máquina virtual a partir de playbooks do Ansible. Se usar um playbook de definir uma ou ambas as máquinas virtuais, certifique-se de que a ligação SSH é utilizada em vez de uma palavra-passe.

    - **CLI do Azure** -cada um dos seguintes comandos no Cloud Shell para criar duas máquinas de virtuais de problema:

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

Pode [utilizar etiquetas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorias definidas pelo utilizador. 

Introduza o seguinte [marca do recurso de az](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) comandos para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```
## <a name="generate-a-dynamic-inventory"></a>Gerar um inventário dinâmicas

Assim que tiver suas máquinas virtuais definido (e marcado), é hora de gerar o inventário dinâmicas.

### <a name="using-ansible-version--28"></a>A utilização do Ansible versão < 2.8

Ansible fornece um script de Python com o nome [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que gera um inventário dinâmicas dos seus recursos do Azure. Os seguintes passos guiá-lo por meio de utilizar o `azure_rm.py` testar o script para ligar ao seu duas máquinas virtuais do Azure:

1. Utilizar a GNU `wget` comando para obter o `azure_rm.py` script:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Utilize o `chmod` comando para alterar as permissões de acesso para o `azure_rm.py` script. O seguinte comando utiliza o `+x` parâmetro para permitir a execução (em execução) do ficheiro especificado (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Utilize o [ansible comando](https://docs.ansible.com/ansible/2.4/ansible.html) para ligar ao seu grupo de recursos: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Assim que estiver ligado, verá os resultados semelhantes à saída seguinte:

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

### <a name="ansible-version--28"></a>Versão do Ansible > = 2.8

A partir do Ansible 2.8, Ansible fornece uma [Plug-in do inventário de dinâmico do Azure](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Os seguintes passos guiá-lo por meio de utilizar o plug-in:

1. O plug-in de inventário requer um ficheiro de configuração. O ficheiro de configuração tem de terminar em `azure_rm` e ter uma extensão `yml` ou `yaml`. Para este exemplo do tutorial, salve o playbook seguinte como `myazure_rm.yml`:

    ```yml
    plugin: azure_rm
    include_vm_resource_groups:
    - ansible-inventory-test-rg
    auth_source: auto
    ```

1. Execute o seguinte comando para enviar pings para as VMs no grupo de recursos:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Ao executar o comando anterior, pode receber o erro seguinte:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Se receber o erro de "verificação de chave de anfitrião", adicione a seguinte linha para o ficheiro de configuração do Ansible. O ficheiro de configuração do Ansible está localizado em `/etc/ansible/ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Ao executar o playbook, verá os resultados semelhantes à saída seguinte:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Ativar a marca VM
Assim que tiver configurado uma etiqueta, terá de "ativar" essa marca. Uma forma de ativar uma marca é ao exportar a marca para uma variável de ambiente `AZURE_TAGS` via o `export` comando:

```azurecli-interactive
export AZURE_TAGS=nginx
```

- Se estiver a utilizar o Ansible < 2.8, execute o seguinte comando:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```

- Se estiver a utilizar o Ansible > = 2.8, execute o seguinte comando:
  
    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

Agora, ver apenas uma máquina virtual (aquele cuja etiqueta corresponde ao valor exportados para o `AZURE_TAGS` variável de ambiente):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurar o Nginx na VM marcada

A finalidade das etiquetas é permitir a capacidade de forma rápida e fácil trabalhar com subgrupos das suas máquinas virtuais. Por exemplo, digamos que pretende instalar o Nginx apenas em máquinas virtuais ao qual atribuiu uma marca de `nginx`. Os passos seguintes mostram como é fácil de realizar:

1. Crie um ficheiro denominado `nginx.yml`:

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

1. Guarde o ficheiro e saia do editor.

1. Executar o playbook com o `ansible-playbook` comando:

   - Ansible < 2.8:

    ```bash
    ansible-playbook -i azure_rm.py nginx.yml
    ```

   - Ansible > = 2.8:

    ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml
    ```

1. Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

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

Esta secção ilustra uma técnica para testar que o Nginx está instalado na sua máquina virtual.

1. Utilize o [az vm lista-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) comando para obter o endereço IP do `ansible-inventory-test-vm1` máquina virtual. O valor retornado (endereço IP da máquina virtual), em seguida, é utilizado como o parâmetro para o comando SSH para ligar à máquina virtual.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Enquanto estiver ligado a `ansible-inventory-test-vm1` máquina virtual, execute o [nginx - v](https://nginx.org/en/docs/switches.html) comando para determinar se o Nginx está instalado.

    ```azurecli-interactive
    nginx -v
    ```

1. Depois de executar o `nginx -v` de comando, verá a versão do Nginx (segunda linha) que indica que o Nginx está instalado.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Clique no `<Ctrl>D` combinação para desligar a sessão SSH do teclado.

1. Anterior a fazer os passos para o `ansible-inventory-test-vm2` máquina virtual produz uma mensagem meramente informativa que indica onde pode obter Nginx (o que implica que este não estiver instalado neste momento):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Quickstart: Configurar máquinas virtuais do Linux no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm)