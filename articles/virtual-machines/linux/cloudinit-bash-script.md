---
title: Use cloud-init para executar um roteiro de bash em um Linux VM em Azure
description: Como usar o cloud-init para executar um roteiro de bash em um Linux VM durante a criação com o CLI Azure
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1dabd7db85d4222ca2e9f8387b716af291ee6a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559431"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Use cloud-init para executar um roteiro de bash em um Linux VM em Azure
Este artigo [mostra-lhe](https://cloudinit.readthedocs.io) como usar o init cloud para executar um script de bash existente em uma máquina virtual Linux (VM) ou conjuntos de escala de máquina virtual (VMSS) em tempo de provisionamento em Azure. Estes scripts de nuvem funcionam na primeira bota uma vez que os recursos foram a provisionados pela Azure. Para obter mais informações sobre como o cloud-init funciona de forma nativa em Azure e os distros de Linux suportados, consulte [a visão geral de cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Executar um roteiro de bash com cloud-init
Com a nuvem não precisa converter os scripts existentes num cloud-config, a cloud-init aceita vários tipos de entrada, um dos quais é um script bash.

Se tiver usado a extensão linux custom scripts para executar os seus scripts, pode migrar para usar o cloud-init. No entanto, as Extensões Azure integraram relatórios para alertar para falhas no script, uma implementação de imagem init em nuvem NÃO falhará se o script falhar.

Para ver esta funcionalidade em ação, crie um simples script de bash para testes. Tal como o ficheiro cloud-init, `#cloud-config` este script deve ser local para onde você estará executando os comandos AzureCLI para o fornecimento da sua máquina virtual.  Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor simple_bash.sh` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **nano** editor. Certifique-se de que todo o ficheiro cloud-init é copiado corretamente, especialmente a primeira linha.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Antes de implementar esta imagem, é necessário criar um grupo de recursos com o [grupo az criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie um VM com [az vm criar](/cli/azure/vm) e especificar o ficheiro de script de bash com o `--custom-data simple_bash.sh` seguinte:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verifique o guião da bash já correu
SSH para o endereço IP público do seu VM mostrado na saída do comando anterior. Insira o seu próprio **públicoIpAddress** da seguinte forma:

```bash
ssh <publicIpAddress>
```

Altere para o **diretório /tmp** e verifique se myScript.txt ficheiro existe e tem o texto apropriado dentro do mesmo.  Caso contrário, pode verificar o **(var/log/cloud-init.log** para obter mais detalhes.  Procure a seguinte entrada:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador Linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicações, atualize ficheiros de configuração e injete teclas](tutorial-automate-vm-deployment.md)
