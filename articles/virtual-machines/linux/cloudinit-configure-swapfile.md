---
title: Use cloud-init para configurar uma divisória de troca num VM Linux
description: Como usar cloud-init para configurar uma divisória de troca num VM Linux durante a criação com o Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969206"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Use cloud-init para configurar uma divisória de troca num VM Linux
Este artigo mostra-lhe como usar [cloud-init](https://cloudinit.readthedocs.io) para configurar a divisória de troca em várias distribuições linux. A divisória de troca foi tradicionalmente configurada pelo Agente Linux (WALA) com base nas distribuições necessárias.  Este documento irá delinear o processo de construção da divisória de troca a pedido durante o tempo de provisionamento utilizando cloud-init.  Para mais informações sobre como o cloud-init funciona nativamente em Azure e os distros linux suportados, consulte a [visão geral cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Criar divisória de troca para imagens baseadas em Ubuntu
Por defeito em Azure, as imagens da galeria Ubuntu não criam divisórias de troca. Para permitir a configuração da partilha de divisórias durante o tempo de provisionamento vm utilizando cloud-init - consulte o [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) no wiki Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Crie divisória de troca para imagens baseadas em Chapéu Vermelho e CentOS

Crie um ficheiro na sua concha atual chamado *cloud_init_swappart.txt* e cola a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_swappart.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **editor nano.** Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

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

Antes de implementar esta imagem, precisa criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) `--custom-data cloud_init_swappart.txt` e especificar o ficheiro cloud-init com o seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verificar foi criada a partilha de swaps
SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **endereço público Da** seguinte forma:

```bash
ssh <publicIpAddress>
```

Uma vez que tenha SSH'ed no vm, verifique se a divisória de troca foi criada

```bash
swapon -s
```

A saída deste comando deve ser assim:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se tiver uma imagem Azure existente que tenha uma divisória de troca configurada e pretender alterar a configuração da partilha de swaps para novas imagens, deverá remover a divisória de swap existente. Consulte o documento 'Personalize as imagens para fornecer por cloud-init' para mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
