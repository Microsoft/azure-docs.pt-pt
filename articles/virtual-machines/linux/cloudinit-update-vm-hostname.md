---
title: Utilizar o cloud-init para definir o nome de anfitrião para uma VM do Linux no Azure | Documentos da Microsoft
description: Como utilizar o cloud-init para personalizar uma VM do Linux durante a criação com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: da20c4b30e2708bf7754d025cfbd2c269c3b5c7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648946"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Utilizar o cloud-init para definir o nome de anfitrião para uma VM do Linux no Azure
Este artigo mostra-lhe como utilizar [cloud-init](https://cloudinit.readthedocs.io) para configurar um nome de anfitrião específico numa máquina virtual ou máquina virtual (VM) de conjuntos de dimensionamento (VMSS) em aprovisionamento tempo no Azure. Executam estes scripts de inicialização da cloud no primeiro arranque, assim que os recursos foram aprovisionados através do Azure. Para obter mais informações sobre o cloud-init funcionamento nativamente no Azure e distribuições de Linux suportadas, consulte [descrição geral do cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Definir o nome de anfitrião com o cloud-init
Por predefinição, o nome do anfitrião é o mesmo que o nome da VM quando criar uma nova máquina virtual no Azure.  Para executar um script de inicialização da cloud para alterar este nome de anfitrião predefinido quando cria uma VM no Azure com o [az vm crie](/cli/azure/vm), especifique o ficheiro cloud-init com o `--custom-data` mudar.  

Para ver o processo de atualização em ação, crie um ficheiro na sua shell atual com o nome *cloud_init_hostname.txt* e cole a seguinte configuração. Neste exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_hostname.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolher #1 para utilizar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
hostname: myhostname
```

Antes de implementar esta imagem, tem de criar um grupo de recursos com o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm crie](/cli/azure/vm) e especifique o ficheiro cloud-init com `--custom-data cloud_init_hostname.txt` da seguinte forma:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Depois de criado, a CLI do Azure mostra informações sobre a VM. Utilize o `publicIpAddress` para encaminhar o SSH para a VM. Introduza o seu próprio endereço da seguinte forma:

```bash
ssh <publicIpAddress>
```

Para ver o nome da VM, utilize o `hostname` comando da seguinte forma:

```bash
hostname
```

A VM deve comunicar o nome do anfitrião como esse conjunto de valores no ficheiro cloud-init, conforme mostrado no seguinte exemplo:

```bash
myhostname
```

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de cloud-init adicionais das alterações de configuração, consulte o seguinte:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
