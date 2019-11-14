---
title: Usar Cloud-init para definir o nome do host para uma VM do Linux no Azure
description: Como usar Cloud-init para personalizar uma VM do Linux durante a criação com o CLI do Azure
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
ms.openlocfilehash: adac9dc41aaba7ce6bfd9f01917d647174b41282
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036720"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Usar Cloud-init para definir o nome do host para uma VM do Linux no Azure
Este artigo mostra como usar [Cloud-init](https://cloudinit.readthedocs.io) para configurar um nome de host específico em uma VM (máquina virtual) ou VMSS (conjuntos de dimensionamento de máquinas virtuais) no tempo de provisionamento no Azure. Esses scripts de Cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações sobre como a Cloud-init funciona nativamente no Azure e o distribuições do Linux com suporte, consulte [visão geral de Cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Definir o nome do host com Cloud-init
Por padrão, o hostname é o mesmo que o nome da VM quando você cria uma nova máquina virtual no Azure.  Para executar um script Cloud-init para alterar esse nome de host padrão ao criar uma VM no Azure com [AZ VM Create](/cli/azure/vm), especifique o arquivo Cloud-init com a opção `--custom-data`.  

Para ver o processo de atualização em ação, crie um arquivo em seu shell atual chamado *cloud_init_hostname. txt* e cole a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell não em seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_hostname.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o editor do **nano** . Certifique-se de que todo o arquivo Cloud-init seja copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
hostname: myhostname
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [AZ VM Create](/cli/azure/vm) e especifique o arquivo Cloud-init com `--custom-data cloud_init_hostname.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Depois de criada, a CLI do Azure mostra informações sobre a VM. Use o `publicIpAddress` para SSH para sua VM. Insira seu próprio endereço da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Para ver o nome da VM, use o comando `hostname` da seguinte maneira:

```bash
hostname
```

A VM deve relatar o nome do host como esse valor definido no arquivo Cloud-init, conforme mostrado na seguinte saída de exemplo:

```bash
myhostname
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de inicialização em nuvem de alterações de configuração, consulte o seguinte:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
