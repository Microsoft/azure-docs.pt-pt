---
title: Utilizar o Ansible para gerir os seus inventários dinâmicos do Azure
description: Saiba como utilizar o Ansible para gerir os seus inventários dinâmicos do Azure
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, inventário dinâmicas
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: tutorial
ms.openlocfilehash: b4dd1788291198926d620e406edb568d486be8b5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792383"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Utilizar o Ansible para gerir os seus inventários dinâmicos do Azure
Ansible pode ser utilizado para extrair informações de inventário a partir de várias origens (incluindo origens na cloud, como o Azure) para um *inventário dinâmicas*. Neste artigo, vai utilizar o [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) para configurar um inventário dinâmicas de Ansible do Azure que vai criar duas máquinas virtuais, marcar uma das máquinas virtuais e instalar o Nginx na máquina virtual marcada.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Credenciais do Azure** - [Azure criar credenciais e configurar o Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

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

## <a name="tag-a-virtual-machine"></a>Etiquetar uma máquina virtual
Pode [utilizar etiquetas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorias definidas pelo utilizador. 

Introduza o seguinte [marca do recurso de az](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) comandos para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Gerar um inventário dinâmicas
Assim que tiver suas máquinas virtuais definido (e marcado), é hora de gerar o inventário dinâmicas. Ansible fornece um script de Python chamado [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que gera um inventário dinâmicas dos seus recursos do Azure ao efetuar pedidos da API para o Azure Resource Manager. Os seguintes passos guiá-lo por meio de utilizar o `azure_rm.py` testar o script para ligar ao seu duas máquinas virtuais do Azure:

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

## <a name="enable-the-virtual-machine-tag"></a>Ativar a marca de máquina virtual
Assim que tiver configurado a etiqueta pretendida, precisa para a etiqueta de "ativar". É uma forma de ativar uma etiqueta ao exportar a marca para uma variável de ambiente chamada `AZURE_TAGS` através do **exportar** comando:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Assim que a marca tenha sido exportada, pode experimentar o `ansible` novamente o comando:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Agora, ver apenas uma máquina virtual (aquele cuja etiqueta corresponde ao valor exportados para o **AZURE_TAGS** variável de ambiente):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurar o Nginx na VM marcada
A finalidade das etiquetas é permitir a capacidade de forma rápida e fácil trabalhar com subgrupos das suas máquinas virtuais. Por exemplo, digamos que pretende instalar o Nginx apenas em máquinas virtuais ao qual atribuiu uma marca de `nginx`. Os passos seguintes mostram como é fácil de realizar:

1. Crie um ficheiro (para conter o playbook) denominado `nginx.yml` da seguinte forma:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Insira o seguinte código para o recém-criado `nginx.yml` ficheiro:

    ```yml
    ---
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
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

1. Execute o `nginx.yml` playbook:

    ```azurecli-interactive
    ansible-playbook -i azure_rm.py nginx.yml
    ```

1. Depois de executar o playbook, verá os resultados semelhantes à saída seguinte:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
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

1. Prima a  **&lt;Ctrl > 1!d** combinação para desligar a sessão SSH do teclado.

1. Anterior a efetuar os passos para o `ansible-inventory-test-vm2` máquina virtual produz uma mensagem meramente informativa que indica onde pode obter Nginx (o que implica que este não estiver instalado neste momento):

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
> [Criar uma máquina virtual básica no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm)
