---
title: Usar Cloud-init para configurar uma partição de permuta em uma VM do Linux | Microsoft Docs
description: Como usar Cloud-init para configurar uma partição de permuta em uma VM do Linux durante a criação com o CLI do Azure
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
ms.openlocfilehash: d8ce12b931b6a30fa375588b73a1140ed4697c2f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640776"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Usar Cloud-init para configurar uma partição de permuta em uma VM do Linux
Este artigo mostra como usar [Cloud-init](https://cloudinit.readthedocs.io) para configurar a partição de permuta em várias distribuições do Linux. A partição de permuta foi tradicionalmente configurada pelo agente do Linux (WALA) com base em quais distribuições exigiam uma.  Este documento descreverá o processo de criação da partição de permuta sob demanda durante o tempo de provisionamento usando Cloud-init.  Para obter mais informações sobre como a Cloud-init funciona nativamente no Azure e o distribuições do Linux com suporte, consulte [visão geral de Cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Criar partição de permuta para imagens baseadas no Ubuntu
Por padrão no Azure, as imagens da galeria do Ubuntu não criam partições de permuta. Para habilitar a configuração da partição de permuta durante o tempo de provisionamento da VM usando Cloud-init, consulte o [documento AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) no wiki do Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Criar partição de permuta para imagens baseadas em Red Hat e CentOS

Crie um arquivo em seu shell atual chamado *cloud_init_swappart. txt* e cole a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell não em seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_swappart.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o editor do **nano** . Certifique-se de que todo o arquivo Cloud-init seja copiado corretamente, especialmente a primeira linha.  

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

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [AZ VM Create](/cli/azure/vm) e especifique o arquivo Cloud-init com `--custom-data cloud_init_swappart.txt` como a seguir:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verificar se a partição de permuta foi criada
SSH para o endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Depois que você tiver SSH'ed na VM, verifique se a partição de permuta foi criada

```bash
swapon -s
```

A saída desse comando deve ter a seguinte aparência:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Se você tiver uma imagem do Azure existente que tenha uma partição de permuta configurada e quiser alterar a configuração da partição de permuta para novas imagens, deverá remover a partição de permuta existente. Consulte o documento "Personalizar imagens para provisionar por Cloud-init" para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de inicialização em nuvem de alterações de configuração, consulte o seguinte:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
