---
title: 'Quickstart: Use o CLI Azure para criar um Linux VM'
description: Neste início rápido, vai aprender a utilizar a CLI do Azure para criar uma máquina virtual do Linux
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
ms.openlocfilehash: fd411255247e6a37b857ac11c1b0abbd4558d02a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549775"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Início Rápido: criar uma máquina virtual do Linux com a CLI do Azure

Este quickstart mostra-lhe como usar a interface de linha de comando Azure (CLI) para implantar uma máquina virtual Linux (VM) em Azure. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts.

Neste tutorial, vamos instalar o Ubuntu 16.04 LTS. Para mostrar a VM em ação, irá ligar à mesma através de SSH e instalar o servidor Web NGINX.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode abrir o Cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e selecione **Enter** para executá-lo.

Se preferir instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.30 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm).

O seguinte exemplo cria uma VM com o nome *myVM* e adiciona uma conta de utilizador com o nome *azureuser*. O `--generate-ssh-keys` parâmetro é utilizado para gerar automaticamente uma chave SSH e colocá-la no local da chave predefinido *(~/.ssh).* Para utilizar um conjunto específico de chaves em vez disso, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O seguinte resultado de exemplo mostra que a operação de criação da VM foi concluída com êxito.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Tenha em atenção o seu próprio `publicIpAddress` na saída da VM. Este endereço é utilizado para aceder à VM nos próximos passos.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Por predefinição, apenas as ligações SSH são abertas ao criar uma VM do Linux no Azure. Utilize [az vm open-port](/cli/azure/vm) para abrir a porta TCP 80 para utilização com o servidor Web do NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Estabeleça o SSH para a VM como habitualmente. Substitua **publicIpAddress** pelo endereço IP público da VM, conforme indicado no resultado anterior da VM:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Instalar o servidor Web

Para ver a VM em ação, instale o servidor Web NGINX. Atualize as origens do pacote e, em seguida, instale o pacote NGINX mais recente.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Quando terminar, escreva `exit` para deixar a sessão SSH.

## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Utilize um browser à sua escolha para ver a página predefinida de boas-vindas do NGINX. Utilize o endereço IP público da VM como o endereço Web. O exemplo seguinte mostra o site predefinido do NGINX:

![Ver a página de boas-vindas do NGINX](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group) para remover o grupo de recursos, VM e todos os recursos relacionados. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma máquina virtual simples, abriu uma porta de rede para o tráfego Web e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)
