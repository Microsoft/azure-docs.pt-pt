---
title: Use cloud-init em um Linux VM em Azure
description: Como utilizar o cloud-init para atualizar e instalar pacotes num VM Linux durante a criação com o Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969142"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Use cloud-init para atualizar e instalar pacotes num VM Linux em Azure
Este artigo mostra-lhe como usar [cloud-init](https://cloudinit.readthedocs.io) para atualizar pacotes em uma máquina virtual Linux (VM) ou conjuntos de escala de máquina virtual no tempo de provisionamento em Azure. Estes scripts de cloud-init funcionam na primeira bota uma vez que os recursos foram aprovisionados pelo Azure. Para mais informações sobre como o cloud-init funciona nativamente em Azure e os distros linux suportados, consulte a [visão geral cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Atualizar um VM com cloud-init
Para fins de segurança, é possível configurar um VM para aplicar as últimas atualizações na primeira bota. Como o cloud-init funciona em diferentes distros Linux, não há necessidade de especificar `apt` ou `yum` para o gestor do pacote. Em vez disso, define `package_upgrade` e deixa que o processo de inite de nuvem determine o mecanismo adequado para a distro em uso. Este fluxo de trabalho permite-lhe usar os mesmos scripts de cloud-init através de distros.

Para ver o processo de atualização em ação, crie um ficheiro na sua concha atual chamado *cloud_init_upgrade.txt* e cola a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_upgrade.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **editor nano.** Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Antes de implementar esta imagem, precisa criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro cloud-init com `--custom-data cloud_init_upgrade.txt` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **endereço público Da** seguinte forma:

```bash
ssh <publicIpAddress>
```

Execute a ferramenta de gestão de pacotes e verifique se há atualizações.

```bash
sudo yum update
```

Como o cloud-init verificou e instalou atualizações no arranque, não deve haver atualizações adicionais a aplicar.  Você vê o processo de atualização, o número de pacotes alterados, bem como a instalação de `httpd` executando `yum history` e revê a saída semelhante à abaixo.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
