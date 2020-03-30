---
title: Redefinir o acesso a um VM Azure Linux
description: Como gerir os utilizadores administrativos e redefinir o acesso aos VMs Linux utilizando a extensão VMAccess e o Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250364"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Gerir utilizadores administrativos, SSH, e verificar ou reparar discos em VMs Linux utilizando a extensão VMAccess com o Azure CLI
## <a name="overview"></a>Descrição geral
O disco do seu VM Linux está a mostrar erros. De alguma forma, redefiniu a palavra-passe de raiz para o seu VM Linux ou apagou acidentalmente a sua chave privada SSH. Se isso acontecesse nos dias do datacenter, terias de conduzir até lá e depois abrir o KVM para chegar à consola do servidor. Pense na extensão Azure VMAccess como o interruptor KVM que lhe permite aceder à consola para redefinir o acesso ao Linux ou realizar a manutenção do nível do disco.

Este artigo mostra-lhe como utilizar a Extensão Azure VMAccess para verificar ou reparar um disco, redefinir o acesso ao utilizador, gerir contas administrativas de utilizadores ou atualizar a configuração SSH no Linux quando estiverem a funcionar como máquinas virtuais do Azure Resource Manager. Se precisar de gerir as máquinas virtuais clássicas - pode seguir as instruções encontradas na [documentação clássica](../linux/classic/reset-access-classic.md)da VM . 
 
> [!NOTE]
> Se utilizar a extensão VMAccess para redefinir a palavra-passe do seu VM depois de instalar a extensão de login AAD, terá de reexecutar a extensão de login AAD para reativar o Login AAD para a sua máquina.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-system"></a>Sistema operativo

A extensão de Acesso VM pode ser executada contra estas distribuições Linux:

| Distribuição | Versão |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS e 12.04 LTS |
| Debian | Debiano 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 e 12 |
| OpenSuse | abre SUSE Leap 42.2+ |
| CentOS | Centos 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Formas de utilizar a Extensão VMAccess
Existem duas formas de utilizar a extensão VMAccess nos seus VMs Linux:

* Utilize o Azure CLI e os parâmetros necessários.
* [Utilize ficheiros JSON crus que o processo de extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e, em seguida, atuem.

Os seguintes exemplos utilizam comandos [de utilizador az vm.](/cli/azure/vm/user) Para realizar estes passos, necessita do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Update SSH key (Atualizar as chaves de SSH)
O exemplo seguinte atualiza a tecla SSH para o utilizador `azureuser` no VM denominado: `myVM`

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **NOTA:** O `az vm user update` comando anexa o novo texto `~/.ssh/authorized_keys` de chave pública do ficheiro para o utilizador administrativo no VM. Isto não substitui nem remove as teclas SSH existentes. Isto não removerá as chaves anteriores definidas no momento da implantação ou atualizações subsequentes através da extensão VMAccess.

## <a name="reset-password"></a>Repor palavra-passe
O exemplo seguinte repõe a `azureuser` palavra-passe para `myVM`o utilizador no VM denominado:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reiniciar SSH
O exemplo seguinte reinicia o daemon SSH e repõe a configuração `myVM`SSH para valores padrão num VM chamado:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Criar um utilizador administrativo/sudo
O exemplo seguinte cria `myNewUser` um utilizador com permissões **sudo.** A conta utiliza uma chave SSH para `myVM`autenticação no VM denominado . Este método foi concebido para ajudá-lo a recuperar o acesso a um VM no caso de as credenciais atuais serem perdidas ou esquecidas. Como uma boa prática, as contas com permissões **sudo** devem ser limitadas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Eliminar um utilizador
O exemplo seguinte elimina `myNewUser` um utilizador nomeado `myVM`no VM denominado:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Utilizar ficheiros JSON e a extensão VMAccess
Os exemplos que se seguem utilizam ficheiros JSON crus. Utilize o conjunto de [extensão az vm](/cli/azure/vm/extension) para, em seguida, ligar para os seus ficheiros JSON. Estes ficheiros JSON também podem ser chamados a partir de modelos Azure. 

### <a name="reset-user-access"></a>Redefinir o acesso ao utilizador
Se perdeu o acesso à raiz no seu VM Linux, pode lançar um script VMAccess para atualizar a chave SSH ou palavra-passe de um utilizador.

Para atualizar a chave pública SSH de `update_ssh_key.json` um utilizador, crie um ficheiro nomeado e adicione definições no seguinte formato. Substitua os seus `username` `ssh_key` próprios valores pelos e parâmetros:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Para redefinir uma palavra-passe `reset_user_password.json` do utilizador, crie um ficheiro nomeado e adicione definições no seguinte formato. Substitua os seus `username` `password` próprios valores pelos e parâmetros:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Reiniciar SSH
Para reiniciar o daemon SSH e redefinir a configuração SSH para valores predefinidos, crie um ficheiro nomeado `reset_sshd.json`. Adicione o seguinte conteúdo:

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Gerir utilizadores administrativos

Para criar um utilizador com permissões **sudo** que utilize uma chave `create_new_user.json` SSH para autenticação, crie um ficheiro nomeado e adicione definições no seguinte formato. Substitua os seus `username` `ssh_key` próprios valores pelos e parâmetros. Este método foi concebido para ajudá-lo a recuperar o acesso a um VM no caso de as credenciais atuais serem perdidas ou esquecidas. Como uma boa prática, as contas com permissões **sudo** devem ser limitadas.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Para eliminar um utilizador, `delete_user.json` crie um ficheiro nomeado e adicione o seguinte conteúdo. Substitua o seu `remove_user` próprio valor pelo parâmetro:

```json
{
  "remove_user":"myNewUser"
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Verifique ou repare o disco
Utilizando o VMAccess, também pode verificar e reparar um disco que adicionou ao VM Linux.

Para verificar e, em seguida, `disk_check_repair.json` reparar o disco, crie um ficheiro nomeado e adicione definições no seguinte formato. Substitua o seu próprio `repair_disk`valor pelo nome de:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Execute o script VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
