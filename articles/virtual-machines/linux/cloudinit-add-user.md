---
title: Usar Cloud-init para adicionar um usuário a uma VM do Linux no Azure
description: Como usar Cloud-init para adicionar um usuário a uma VM do Linux durante a criação com o CLI do Azure
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
ms.openlocfilehash: 428f489a24c24b173cb1cef0980dd17c1d8483ce
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036785"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Usar Cloud-init para adicionar um usuário a uma VM do Linux no Azure
Este artigo mostra como usar [Cloud-init](https://cloudinit.readthedocs.io) para adicionar um usuário em uma VM (máquina virtual) ou VMSS (conjuntos de dimensionamento de máquinas virtuais) no tempo de provisionamento no Azure. Esse script Cloud-init é executado na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações sobre como a Cloud-init funciona nativamente no Azure e o distribuições do Linux com suporte, consulte [visão geral de Cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Adicionar um usuário a uma VM com Cloud-init
Uma das primeiras tarefas em qualquer nova VM do Linux é adicionar um usuário adicional para evitar o uso da *raiz*. As chaves SSH são práticas recomendadas para segurança e usabilidade. As chaves são adicionadas ao arquivo *~/.ssh/authorized_keys* com esse script Cloud-init.

Para adicionar um usuário a uma VM do Linux, crie um arquivo em seu shell atual chamado *cloud_init_add_user. txt* e cole a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell não em seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_add_user.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o editor do **nano** . Certifique-se de que todo o arquivo Cloud-init seja copiado corretamente, especialmente a primeira linha.  Você precisa fornecer sua própria chave pública (como o conteúdo de *~/.ssh/id_rsa. pub*) para o valor de `ssh-authorized-keys:`-ela foi reduzida aqui para simplificar o exemplo.

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
> O arquivo #cloud-config inclui o parâmetro `- default` incluído. Isso adicionará o usuário ao usuário administrador existente criado durante o provisionamento. Se você criar um usuário sem o parâmetro `- default`, o usuário administrador gerado automaticamente criado pela plataforma Azure será substituído. 

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [AZ VM Create](/cli/azure/vm) e especifique o arquivo Cloud-init com `--custom-data cloud_init_add_user.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Para confirmar que o usuário foi adicionado à VM e aos grupos especificados, exiba o conteúdo do arquivo */etc/Group* da seguinte maneira:

```bash
cat /etc/group
```

A saída de exemplo a seguir mostra que o usuário do arquivo *cloud_init_add_user. txt* foi adicionado à VM e ao grupo apropriado:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de inicialização em nuvem de alterações de configuração, consulte o seguinte:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
