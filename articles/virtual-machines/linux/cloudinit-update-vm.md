---
title: Utilizar o cloud-init para atualizar e instalar pacotes numa VM do Linux no Azure | Documentos da Microsoft
description: Como utilizar o cloud-init para atualizar e instalar pacotes numa VM do Linux durante a criação com a CLI do Azure
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: d5f4dc7f4abc13f253a206a63e65faf1106f9c7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627746"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Utilizar o cloud-init para atualizar e instalar pacotes numa VM do Linux no Azure
Este artigo mostra-lhe como utilizar [cloud-init](https://cloudinit.readthedocs.io) para atualizar os pacotes numa Linux máquina virtual (VM) ou o dimensionamento de máquinas virtuais define (VMSS) em aprovisionamento tempo no Azure. Executam estes scripts de inicialização da cloud no primeiro arranque, assim que os recursos foram aprovisionados através do Azure. Para obter mais informações sobre o cloud-init funcionamento nativamente no Azure e distribuições de Linux suportadas, consulte [descrição geral do cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Atualizar uma VM com o cloud-init
Por motivos de segurança, pode querer configurar uma VM para aplicar as atualizações mais recentes no primeiro arranque. Como o cloud-init funciona em diferentes distribuições de Linux, não é necessário especificar `apt` ou `yum` para o Gestor de pacotes. Em vez disso, define `package_upgrade` e deixar que o processo de inicialização da cloud determinam o mecanismo apropriado para a distribuição em utilização. Este fluxo de trabalho permite-lhe utilizar os mesmos scripts de cloud-init em distribuições.

Para ver o processo de atualização em ação, crie um ficheiro na sua shell atual com o nome *cloud_init_upgrade.txt* e cole a seguinte configuração. Neste exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_upgrade.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolher #1 para utilizar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Antes de implementar esta imagem, tem de criar um grupo de recursos com o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm crie](/cli/azure/vm) e especifique o ficheiro cloud-init com `--custom-data cloud_init_upgrade.txt` da seguinte forma:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público da sua VM mostrada a saída do comando anterior. Introduza o seu próprio **publicIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Execute a ferramenta de gestão de pacotes e procurar atualizações.

```bash
sudo yum update
```

Como na cloud-init selecionado para e atualizações instaladas no arranque, não deve haver nenhum atualizações adicionais para aplicar.  Veja o processo de atualização, o número de pacotes alteradas, bem como a instalação do `httpd` executando `yum history` e reveja o resultado semelhante ao abaixo.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de cloud-init adicionais das alterações de configuração, consulte o seguinte:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
