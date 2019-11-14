---
title: Usar Cloud-init para executar um script de bash em uma VM do Linux no Azure
description: Como usar Cloud-init para executar um script de bash em uma VM do Linux durante a criação com o CLI do Azure
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
ms.openlocfilehash: 0e18b740b9b656236bd1958dd191bc9b02283d67
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036805"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Usar Cloud-init para executar um script de bash em uma VM do Linux no Azure
Este artigo mostra como usar [Cloud-init](https://cloudinit.readthedocs.io) para executar um script bash existente em uma VM (máquina virtual) do Linux ou VMSS (conjuntos de dimensionamento de máquinas virtuais) no tempo de provisionamento no Azure. Esses scripts de Cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações sobre como a Cloud-init funciona nativamente no Azure e o distribuições do Linux com suporte, consulte [visão geral de Cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Executar um script bash com Cloud-init
Com Cloud-init você não precisa converter seus scripts existentes em uma Cloud-CONFIG, o Cloud-init aceita vários tipos de entrada, um dos quais é um script de bash.

Se você estiver usando a extensão de script personalizado do Azure do Linux para executar seus scripts, poderá migrá-los para usar Cloud-init. No entanto, as extensões do Azure têm relatórios integrados para alertar sobre falhas de script, uma implantação de imagem de inicialização de nuvem não falhará se o script falhar.

Para ver essa funcionalidade em ação, crie um script bash simples para teste. Assim como o arquivo de `#cloud-config` Cloud-init, esse script deve ser local no qual você executará os comandos AzureCLI para provisionar sua máquina virtual.  Para este exemplo, crie o arquivo no Cloud Shell não em seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor simple_bash.sh` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o editor do **nano** . Certifique-se de que todo o arquivo Cloud-init seja copiado corretamente, especialmente a primeira linha.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Antes de implantar essa imagem, você precisa criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Agora, crie uma VM com [AZ VM Create](/cli/azure/vm) e especifique o arquivo de script bash com `--custom-data simple_bash.sh` da seguinte maneira:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verificar se o script de bash foi executado
SSH para o endereço IP público da VM mostrado na saída do comando anterior. Insira seu próprio **publicIpAddress** da seguinte maneira:

```bash
ssh <publicIpAddress>
```

Altere para o diretório **/tmp** e verifique se o arquivo MyScript. txt existe e tem o texto apropriado dentro dele.  Se não tiver, você poderá verificar o **/var/log/Cloud-init.log** para obter mais detalhes.  Pesquise a seguinte entrada:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de inicialização em nuvem de alterações de configuração, consulte o seguinte:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
