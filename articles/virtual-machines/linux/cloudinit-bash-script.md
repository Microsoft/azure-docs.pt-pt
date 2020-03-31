---
title: Use cloud-init para executar um roteiro de bash em um Linux VM em Azure
description: Como usar cloud-init para executar um roteiro de bash em um Linux VM durante a criação com o Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969223"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Use cloud-init para executar um roteiro de bash em um Linux VM em Azure
Este artigo mostra-lhe como usar [cloud-init](https://cloudinit.readthedocs.io) para executar um roteiro de bash existente em uma máquina virtual Linux (VM) ou conjuntos de escala de máquina virtual (VMSS) no tempo de provisionamento em Azure. Estes scripts de cloud-init funcionam na primeira bota uma vez que os recursos foram aprovisionados pelo Azure. Para mais informações sobre como o cloud-init funciona nativamente em Azure e os distros linux suportados, consulte a [visão geral cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Executar um roteiro de bash com cloud-init
Com cloud-init você não precisa converter seus scripts existentes em um cloud-config, cloud-init aceita vários tipos de entrada, um dos quais é um roteiro de bash.

Se tiver usado a extensão do Script Azure personalizado do Linux para executar os seus scripts, pode migrar para usar cloud-init. No entanto, as Extensões Azure integraram relatórios para alertar para falhas no script, uma implementação de imagem init-init em nuvem NÃO falhará se o script falhar.

Para ver esta funcionalidade em ação, crie um simples roteiro de bash para testes. Tal como o `#cloud-config` ficheiro cloud-init, este script deve ser local para onde você estará executando os comandos AzureCLI para fornecer a sua máquina virtual.  Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor simple_bash.sh` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **editor nano.** Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Antes de implementar esta imagem, precisa criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm](/cli/azure/vm) `--custom-data simple_bash.sh` criar e especificar o ficheiro de script de bash com o seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verifique se o guião de bash já correu
SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **endereço público Da** seguinte forma:

```bash
ssh <publicIpAddress>
```

Mude para o diretório **/tmp** e verifique se o ficheiro myScript.txt existe e tem o texto adequado no seu interior.  Caso contrário, pode verificar o **/var/log/cloud-init.log** para obter mais detalhes.  Procure a seguinte entrada:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
