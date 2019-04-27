---
title: Utilizar o cloud-init para adicionar um utilizador a uma VM do Linux no Azure | Documentos da Microsoft
description: Como utilizar o cloud-init para adicionar um utilizador a uma VM do Linux durante a criação com a CLI do Azure
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
ms.openlocfilehash: 51de92eb64e9879b769baf7e574ee1dca9355040
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650357"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Utilizar o cloud-init para adicionar um utilizador a uma VM do Linux no Azure
Este artigo mostra-lhe como utilizar [cloud-init](https://cloudinit.readthedocs.io) para adicionar um utilizador de uma máquina virtual ou máquina virtual (VM) de conjuntos de dimensionamento (VMSS) em aprovisionamento tempo no Azure. Este script de inicialização da cloud é executado no primeiro arranque depois dos recursos foram aprovisionados através do Azure. Para obter mais informações sobre o cloud-init funcionamento nativamente no Azure e distribuições de Linux suportadas, consulte [descrição geral do cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Adicionar um utilizador a uma VM com o cloud-init
Uma das primeiras tarefas em qualquer nova VM do Linux é adicionar um utilizador adicional para si próprio evitar a utilização de *raiz*. Chaves SSH são a melhor prática de segurança e usabilidade. As chaves são adicionadas para o *~/.ssh/authorized_keys* ficheiro com este script de inicialização da cloud.

Para adicionar um utilizador a uma VM do Linux, crie um ficheiro na sua shell atual com o nome *cloud_init_add_user.txt* e cole a seguinte configuração. Neste exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_add_user.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolher #1 para utilizar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha.  Tem de fornecer sua própria chave pública (por exemplo, o conteúdo do *~/.ssh/id_rsa.pub*) para o valor do `ssh-authorized-keys:` -tem foram abreviado aqui para simplificar o exemplo.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> O arquivo de configuração de cloud # inclui o `- default` parâmetro incluído. Isso acrescenta o utilizador, para o usuário de administrador existente, criado durante o aprovisionamento. Se criar um utilizador sem o `- default` parâmetro - o utilizador de administrador gerado de automática criado pela plataforma do Azure seria substituído. 

Antes de implementar esta imagem, tem de criar um grupo de recursos com o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm crie](/cli/azure/vm) e especifique o ficheiro cloud-init com `--custom-data cloud_init_add_user.txt` da seguinte forma:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público da sua VM mostrada a saída do comando anterior. Introduza o seu próprio **publicIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Para confirmar que o utilizador foi adicionado para a VM e os grupos especificados, exibir o conteúdo do */etc/grupo* ficheiros da seguinte forma:

```bash
cat /etc/group
```

O resultado de exemplo seguinte mostra o utilizador a partir da *cloud_init_add_user.txt* ficheiro foi adicionado para a VM e o grupo adequado:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de cloud-init adicionais das alterações de configuração, consulte o seguinte:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
