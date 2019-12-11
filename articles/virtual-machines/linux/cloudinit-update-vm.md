---
title: Usar Cloud-init em uma VM do Linux no Azure
description: Como usar Cloud-init para atualizar e instalar pacotes em uma VM do Linux durante a criação com o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7bb48ec11ec042f021203c1716968daa9ab45047
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973140"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Usar Cloud-init para atualizar e instalar pacotes em uma VM do Linux no Azure
Este artigo mostra como usar [Cloud-init](https://cloudinit.readthedocs.io) para atualizar pacotes em uma VM (máquina virtual) do Linux ou conjuntos de dimensionamento de máquinas virtuais no tempo de provisionamento no Azure. Esses scripts de Cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações sobre como a Cloud-init funciona nativamente no Azure e o distribuições do Linux com suporte, consulte [visão geral de Cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Atualizar uma VM com Cloud-init
Para fins de segurança, talvez você queira configurar uma VM para aplicar as atualizações mais recentes na primeira inicialização. Como o Cloud-init funciona em diferentes distribuições do Linux, não é necessário especificar `apt` ou `yum` para o Gerenciador de pacotes. Em vez disso, você define `package_upgrade` e permite que o processo de inicialização de nuvem determine o mecanismo apropriado para o distribuição em uso. Esse fluxo de trabalho permite que você use os mesmos scripts de Cloud-init em distribuições.

Para ver o processo de atualização em ação, crie um arquivo em seu shell atual chamado *cloud_init_upgrade. txt* e cole a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell não em seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_upgrade.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o editor do **nano** . Certifique-se de que todo o arquivo Cloud-init seja copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [AZ VM Create](/cli/azure/vm) e especifique o arquivo Cloud-init com `--custom-data cloud_init_upgrade.txt` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Execute a ferramenta de gerenciamento de pacotes e verifique se há atualizações.

```bash
sudo yum update
```

À medida que o Cloud-init verificou e instalou as atualizações na inicialização, não deve haver nenhuma atualização adicional a ser aplicada.  Você verá o processo de atualização, o número de pacotes alterados, bem como a instalação de `httpd` executando `yum history` e examinará a saída semelhante à mostrada abaixo.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de inicialização em nuvem de alterações de configuração, consulte o seguinte:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
