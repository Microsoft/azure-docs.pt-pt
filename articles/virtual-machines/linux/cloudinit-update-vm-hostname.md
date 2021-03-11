---
title: Use o init cloud para definir o nome de anfitrião para um Linux VM
description: Como usar o cloud-init para personalizar um Linux VM durante a criação com o CLI Azure
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 5c3195fd338d17e2dae3ff2aadfb90790519715b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559397"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Use cloud-init para definir o nome de anfitrião para um Linux VM em Azure
Este artigo [mostra-lhe](https://cloudinit.readthedocs.io) como usar o init cloud para configurar um nome de hospedeiro específico numa máquina virtual (VM) ou conjuntos de balanças de máquinas virtuais (VMSS) no tempo de provisionamento em Azure. Estes scripts de nuvem funcionam na primeira bota uma vez que os recursos foram a provisionados pela Azure. Para obter mais informações sobre como o cloud-init funciona de forma nativa em Azure e os distros de Linux suportados, consulte [a visão geral de cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Coloque o nome de anfitrião com cloud-init
Por padrão, o nome de hospedeiro é o mesmo que o nome VM quando cria uma nova máquina virtual em Azure.  Para executar um script de cloud-init para alterar este nome de anfitrião predefinido quando criar um VM em Azure com [az vm criar](/cli/azure/vm), especifique o ficheiro cloud-init com o `--custom-data` switch.  

Para ver o processo de atualização em ação, crie um ficheiro na sua concha atual chamada *cloud_init_hostname.txt* e cole a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_hostname.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **nano** editor. Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
hostname: myhostname
```

Antes de implementar esta imagem, é necessário criar um grupo de recursos com o [grupo az criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro cloud-init com o `--custom-data cloud_init_hostname.txt` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Uma vez criado, o Azure CLI mostra informações sobre o VM. Use o `publicIpAddress` SSH no seu VM. Insira o seu próprio endereço da seguinte forma:

```bash
ssh <publicIpAddress>
```

Para ver o nome VM, use o `hostname` comando da seguinte forma:

```bash
hostname
```

O VM deve reportar o nome de hospedeiro como o valor indicado no ficheiro cloud-init, como mostrado na saída do exemplo seguinte:

```bash
myhostname
```

## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador Linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicações, atualize ficheiros de configuração e injete teclas](tutorial-automate-vm-deployment.md)
