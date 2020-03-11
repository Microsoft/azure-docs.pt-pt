---
title: Use cloud-init para adicionar um utilizador a um Linux VM em Azure
description: Como usar cloud-init para adicionar um utilizador a um VM Linux durante a criação com o Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: f1782bfe0c14e3b44703f89ec7f78590c1bb74c5
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969229"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Use cloud-init para adicionar um utilizador a um Linux VM em Azure
Este artigo mostra-lhe como usar [cloud-init](https://cloudinit.readthedocs.io) para adicionar um utilizador em conjuntos de escala de máquina virtual (VM) ou máquina virtual (VMSS) no tempo de provisionamento em Azure. Este guião de cloud-init funciona na primeira bota uma vez que os recursos foram aprovisionados pelo Azure. Para obter mais informações sobre como o cloud-init funciona de forma nativa em Azure e os distros linux suportados, consulte a [visão geral do cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Adicione um utilizador a um VM com cloud-init
Uma das primeiras tarefas de qualquer novo VM Linux é adicionar um utilizador adicional para si mesmo para evitar o uso da *raiz*. As chaves SSH são as melhores práticas para segurança e usabilidade. As teclas são adicionadas ao ficheiro *~/.ssh/authorized_keys* com este script de init cloud-init.

Para adicionar um utilizador a um VM Linux, crie um ficheiro na sua concha atual chamada *cloud_init_add_user.txt* e colhe a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud_init_add_user.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **editor nano.** Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  Você precisa fornecer sua própria chave pública (como o conteúdo de *~/.ssh/id_rsa.pub*) para o valor de `ssh-authorized-keys:` - foi encurtado aqui para simplificar o exemplo.

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
> O ficheiro #cloud-config inclui o parâmetro `- default` incluído. Isto irá anexar o utilizador ao utilizador administrativo existente criado durante o fornecimento. Se criar um utilizador sem o parâmetro `- default` - o utilizador de administração gerado por automóveis criado pela plataforma Azure seria substituído. 

Antes de implementar esta imagem, precisa criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro cloud-init com `--custom-data cloud_init_add_user.txt` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **endereço público Da** seguinte forma:

```bash
ssh <publicIpAddress>
```

Para confirmar que o seu utilizador foi adicionado ao VM e aos grupos especificados, veja o conteúdo do ficheiro */etc/grupo* da seguinte forma:

```bash
cat /etc/group
```

A saída de exemplo que se segue mostra que o utilizador do ficheiro *cloud_init_add_user.txt* foi adicionado ao VM e ao grupo apropriado:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
