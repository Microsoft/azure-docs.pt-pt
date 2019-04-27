---
title: Utilizar o cloud-init para executar um script de bash numa VM do Linux no Azure | Documentos da Microsoft
description: Como utilizar o cloud-init para executar um script de bash numa VM do Linux durante a criação com a CLI do Azure
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
ms.openlocfilehash: 4f65ebfd2e1ce508c5cf9b224871102a35b55fe0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627848"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Utilizar o cloud-init para executar um script de bash na VM do Linux no Azure
Este artigo mostra-lhe como utilizar [cloud-init](https://cloudinit.readthedocs.io) para executar um existente do script de bash numa máquina virtual (VM) do Linux ou (VMSS) de conjuntos de dimensionamento de máquina virtual no provisionamento de tempo no Azure. Executam estes scripts de inicialização da cloud no primeiro arranque, assim que os recursos foram aprovisionados através do Azure. Para obter mais informações sobre o cloud-init funcionamento nativamente no Azure e distribuições de Linux suportadas, consulte [descrição geral do cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Executar um script de bash com o cloud-init
Com o cloud-init que não é necessário para converter seus scripts existentes numa configuração de cloud, o cloud-init aceita vários tipos de entrada, um dos quais é um script de bash.

Se usa a extensão do Azure do Linux personalizada Script para executar os seus scripts, é possível migrar-os para utilizar o cloud-init. No entanto, as extensões do Azure tiver integrado o relatório de alerta de falhas de script, uma implementação de imagem de inicialização da cloud não irá falhar se o script falhar.

Para ver esta funcionalidade em ação, crie um script de bash simples para fins de teste. Como o cloud-init `#cloud-config` ficheiro, este script deve ser local para onde irá executar os comandos de AzureCLI para aprovisionar a máquina virtual.  Neste exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor simple_bash.sh` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolher #1 para utilizar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Antes de implementar esta imagem, tem de criar um grupo de recursos com o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [az vm crie](/cli/azure/vm) e especifique o ficheiro de script de bash com `--custom-data simple_bash.sh` da seguinte forma:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Certifique-se de que o script de bash foi executada
SSH para o endereço IP público da sua VM mostrada a saída do comando anterior. Introduza o seu próprio **publicIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Alterar para o **/tmp** directory e verifique se esse arquivo myScript.txt existe e tem o texto adequado dentro dela.  Se não existir, pode verificar o **/var/log/cloud-init.log** para obter mais detalhes.  Procure a seguinte entrada:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de cloud-init adicionais das alterações de configuração, consulte o seguinte:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
