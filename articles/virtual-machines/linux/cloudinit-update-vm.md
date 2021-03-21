---
title: Use cloud-init em um Linux VM em Azure
description: Como utilizar o cloud-init para atualizar e instalar pacotes num VM Linux durante a criação com o Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: bbd3c30cb00dae25afeea356cefb86a9c860cde5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559346"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Use cloud-init para atualizar e instalar pacotes num Linux VM em Azure
Este artigo mostra-lhe como utilizar [o cloud-init](https://cloudinit.readthedocs.io) para atualizar pacotes numa máquina virtual Linux (VM) ou na escala de máquina virtual em tempo de provisionamento em Azure. Estes scripts de nuvem funcionam na primeira bota uma vez que os recursos foram a provisionados pela Azure. Para obter mais informações sobre como o cloud-init funciona de forma nativa em Azure e os distros de Linux suportados, consulte [a visão geral de cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Atualize um VM com cloud-init
Para fins de segurança, é melhor configurar um VM para aplicar as últimas atualizações no primeiro arranque. Como o cloud-init funciona em diferentes distros Linux, não há necessidade de especificar `apt` ou para o gestor de `yum` pacotes. Em vez disso, define `package_upgrade` e deixa o processo de inimento de nuvem determinar o mecanismo apropriado para o distro em uso. Este fluxo de trabalho permite-lhe usar os mesmos scripts de inibição de nuvem através de distros.

Para ver o processo de atualização em ação, crie um ficheiro na sua concha atual chamada *cloud_init_upgrade.txt* e cole a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_upgrade.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **nano** editor. Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Antes de implementar esta imagem, é necessário criar um grupo de recursos com o [grupo az criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro cloud-init com o `--custom-data cloud_init_upgrade.txt` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **públicoIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Executar a ferramenta de gestão de pacotes e verificar se há atualizações.

```bash
sudo yum update
```

Como as atualizações cloud-init verificaram e instalaram atualizações no arranque, não deverá haver atualizações adicionais para aplicar.  Vê o processo de atualização, o número de pacotes alterados, bem como a instalação de `httpd` executando `yum history` e reveja a saída semelhante à abaixo.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador Linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicações, atualize ficheiros de configuração e injete teclas](tutorial-automate-vm-deployment.md)
