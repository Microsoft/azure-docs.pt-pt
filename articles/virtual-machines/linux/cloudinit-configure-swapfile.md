---
title: Use cloud-init para configurar uma partição de troca num Linux VM
description: Como usar o cloud-init para configurar uma partição swap num Linux VM durante a criação com o CLI Azure
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559380"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Use cloud-init para configurar uma partição de troca num Linux VM
Este artigo mostra-lhe como usar [a ineit em nuvem](https://cloudinit.readthedocs.io) para configurar a partição swap em várias distribuições Linux. A partição swap foi tradicionalmente configurada pelo Agente Linux (WALA) com base na qual as distribuições exigiam uma.  Este documento irá delinear o processo de construção da partição swap a pedido durante o tempo de fornecimento usando o cloud-init.  Para obter mais informações sobre como o cloud-init funciona de forma nativa em Azure e os distros de Linux suportados, consulte [a visão geral de cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Criar partilha de troca para imagens baseadas em Ubuntu
Por defeito no Azure, as imagens da galeria Ubuntu não criam divisórias de troca. Para ativar a configuração de troca de divisórias durante o tempo de provisão de VM utilizando o cloud-init - consulte o [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) no wiki Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Criar partilha de troca para imagens baseadas em Chapéu Vermelho e CentOS

Crie um ficheiro na sua concha atual chamada *cloud_init_swappart.txt* e cole a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_swappart.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **nano** editor. Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

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

Antes de implementar esta imagem, é necessário criar um grupo de recursos com o [grupo az criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro cloud-init com o `--custom-data cloud_init_swappart.txt` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verifique se a partição swap foi criada
SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **públicoIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Uma vez que você tem SSH'ed no vm, verifique se a partição swap foi criada

```bash
swapon -s
```

A saída deste comando deve ser assim:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se tiver uma imagem Azure existente que tenha uma partição de troca configurada e quiser alterar a configuração de troca de divisórias para novas imagens, deve remover a partição de troca existente. Consulte o documento 'Personalize as imagens para obter por cloud-init' para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador Linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicações, atualize ficheiros de configuração e injete teclas](tutorial-automate-vm-deployment.md)
