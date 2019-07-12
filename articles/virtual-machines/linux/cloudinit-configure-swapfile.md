---
title: Utilizar o cloud-init para configurar um swapfile numa VM do Linux | Documentos da Microsoft
description: Como utilizar o cloud-init para configurar um swapfile numa VM do Linux durante a criação com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: adf03ea912a028c1059683c49350dea3743ee7a6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671707"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Utilizar o cloud-init para configurar um swapfile numa VM do Linux
Este artigo mostra-lhe como utilizar [cloud-init](https://cloudinit.readthedocs.io) para configurar o swapfile em várias distribuições de Linux. O swapfile tradicionalmente foi configurado pelo Linux agente (WALA) com base em exigia que as distribuições.  Este documento descreverá o processo de criação do swapfile a pedido durante o tempo de aprovisionamento com o cloud-init.  Para obter mais informações sobre o cloud-init funcionamento nativamente no Azure e distribuições de Linux suportadas, consulte [descrição geral do cloud-init](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Criar swapfile para Ubuntu com base em imagens
Por predefinição no Azure, Ubuntu imagens da Galeria não crie arquivos de swap. Para ativar a configuração do ficheiro de troca durante a VM do provisionamento de tempo com o cloud-init - consulte o [AzureSwapPartitions documento](https://wiki.ubuntu.com/AzureSwapPartitions) no wiki do Ubuntu.

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Criar swapfile para Red Hat e CentOS com base em imagens

Crie um ficheiro na sua shell atual com o nome *cloud_init_swapfile.txt* e cole a seguinte configuração. Neste exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_swapfile.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolher #1 para utilizar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Antes de implementar esta imagem, tem de criar um grupo de recursos com o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm crie](/cli/azure/vm) e especifique o ficheiro cloud-init com `--custom-data cloud_init_swapfile.txt` da seguinte forma:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Certifique-se de que foi criado swapfile
SSH para o endereço IP público da sua VM mostrada a saída do comando anterior. Introduza o seu próprio **publicIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Assim que tiver SSH'ed na vm, verifique se o swapfile foi criado

```bash
swapon -s
```

O resultado deste comando deve ter este aspeto:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se tiver uma imagem do Azure existente que tenha um ficheiro de comutação configurado e pretender alterar a configuração do ficheiro de troca para novas imagens, deve remover o ficheiro de troca existente. Consulte 'Personalizar imagens para aprovisionar pela cloud-init' documento para obter mais detalhes.

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de cloud-init adicionais das alterações de configuração, consulte o seguinte:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
