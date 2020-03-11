---
title: Use cloud-init para definir o nome de anfitrião para um VM Linux
description: Como usar cloud-init para personalizar um VM Linux durante a criação com o Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969175"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Use cloud-init para definir o nome de anfitrião para um Linux VM em Azure
Este artigo mostra-lhe como usar [cloud-init](https://cloudinit.readthedocs.io) para configurar um nome de anfitrião específico em uma máquina virtual (VM) ou conjuntos de escala de máquina virtual (VMSS) no tempo de provisionamento em Azure. Estes scripts de cloud-init funcionam na primeira bota uma vez que os recursos foram aprovisionados pelo Azure. Para mais informações sobre como o cloud-init funciona nativamente em Azure e os distros linux suportados, consulte a [visão geral cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Detete o nome de anfitrião com cloud-init
Por padrão, o nome de anfitrião é o mesmo que o nome VM quando se cria uma nova máquina virtual em Azure.  Para executar um script de cloud-init para alterar este nome de anfitrião padrão quando criar um VM em Azure com [az vm criar](/cli/azure/vm), especifique o ficheiro cloud-init com o `--custom-data` switch.  

Para ver o processo de atualização em ação, crie um ficheiro na sua concha atual chamado *cloud_init_hostname.txt* e cola a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_hostname.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **editor nano.** Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
hostname: myhostname
```

Antes de implementar esta imagem, precisa criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro cloud-init com `--custom-data cloud_init_hostname.txt` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Uma vez criado, o Azure CLI mostra informações sobre o VM. Utilize o `publicIpAddress` para SSH para o seu VM. Insira o seu próprio endereço da seguinte forma:

```bash
ssh <publicIpAddress>
```

Para ver o nome VM, utilize o comando `hostname` da seguinte forma:

```bash
hostname
```

O VM deve reportar o nome de anfitrião como esse valor definido no ficheiro cloud-init, como mostra a seguinte saída de exemplo:

```bash
myhostname
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
