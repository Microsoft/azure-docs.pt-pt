---
title: Reiniciar o acesso a um VM Azure Linux
description: Como gerir os utilizadores administrativos e reiniciar o acesso aos VMs Linux utilizando a Extensão VMAccess e o CLI Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 05/10/2018
ms.openlocfilehash: e0e63762fc151d93cb34885c04dd4d03285c29f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557051"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Gerir utilizadores administrativos, SSH e verificar ou reparar discos em VMs Linux utilizando a Extensão VMAccess com o CLI Azure
## <a name="overview"></a>Descrição Geral
O disco do seu Linux VM está a mostrar erros. De alguma forma, repõe a palavra-passe de raiz para o seu VM Linux ou apagou acidentalmente a sua chave privada SSH. Se isso acontecesse nos dias do datacenter, terias de conduzir até lá e depois abrir o KVM para chegar à consola do servidor. Pense na extensão Azure VMAccess como aquele interruptor KVM que lhe permite aceder à consola para reiniciar o acesso ao Linux ou realizar a manutenção do nível do disco.

Este artigo mostra-lhe como utilizar a Extensão Azure VMAccesss para verificar ou reparar um disco, redefinir o acesso do utilizador, gerir contas administrativas do utilizador ou atualizar a configuração SSH no Linux quando estão a funcionar como máquinas virtuais Azure Resource Manager. Se precisar de gerir máquinas virtuais clássicas - pode seguir as instruções encontradas na [documentação clássica](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)do VM . 
 
> [!NOTE]
> Se utilizar a Extensão VMAccess para redefinir a palavra-passe do seu VM depois de instalar a Extensão de Início de Sessão AAD, terá de voltar a aplicar a Extensão de Início de Sessão AAD para reiniciar o Login AAD para a sua máquina.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-system"></a>Sistema operativo

A extensão VM Access pode ser executada contra estas distribuições Linux:

| Distribuição | Versão |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS e 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 e 12 |
| OpenSuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Formas de utilizar a Extensão VMAccess
Existem duas formas de utilizar a extensão VMAccess nos seus VMs Linux:

* Utilize o CLI Azure e os parâmetros necessários.
* [Utilize ficheiros JSON crus que o processo de extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e, em seguida, aja.

Os exemplos a seguir usam comandos [de utilizador az vm.](/cli/azure/vm/user) Para realizar estes passos, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Update SSH key (Atualizar as chaves de SSH)
O exemplo a seguir atualiza a chave SSH para o utilizador `azureuser` no VM `myVM` denominado:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **NOTA:** O `az vm user update` comando anexa o novo texto de chave pública ao ficheiro para o utilizador administrativo no `~/.ssh/authorized_keys` VM. Isto não substitui nem remove quaisquer teclas SSH existentes. Isto não removerá as teclas anteriores definidas no tempo de implementação ou atualizações subsequentes através da Extensão VMAccess.

## <a name="reset-password"></a>Repor palavra-passe
O exemplo a seguir repõe a palavra-passe para o utilizador `azureuser` no VM denominado `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reiniciar SSH
O exemplo a seguir reinicia o daemon SSH e repõe a configuração SSH para valores predefinidos num VM denominado `myVM` :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Criar um utilizador administrativo/sudo
O exemplo a seguir cria um utilizador `myNewUser` com permissões **de sudo.** A conta utiliza uma chave SSH para autenticação no VM denominado `myVM` . Este método foi concebido para ajudá-lo a recuperar o acesso a um VM no caso de as credenciais atuais serem perdidas ou esquecidas. Como uma boa prática, as contas com permissões **de sudo** devem ser limitadas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Eliminar um utilizador
O exemplo a seguir elimina um utilizador nomeado `myNewUser` no VM denominado `myVM` :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Utilizar ficheiros JSON e a extensão VMAccess
Os exemplos a seguir utilizam ficheiros JSON crus. Utilize [o conjunto de extensão az vm](/cli/azure/vm/extension) para, em seguida, ligar para os seus ficheiros JSON. Estes ficheiros JSON também podem ser chamados a partir de modelos Azure. 

### <a name="reset-user-access"></a>Redefinir o acesso do utilizador
Se perdeu o acesso à raiz no seu VM Linux, pode lançar um script VMAccess para atualizar a chave ou senha SSH de um utilizador.

Para atualizar a chave pública SSH de um utilizador, crie um ficheiro nomeado `update_ssh_key.json` e adicione definições no seguinte formato. Substitua os seus próprios valores pelos `username` `ssh_key` parâmetros:

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

Para redefinir uma palavra-passe do utilizador, crie um ficheiro nomeado `reset_user_password.json` e adicione definições no seguinte formato. Substitua os seus próprios valores pelos `username` `password` parâmetros:

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
Para reiniciar o daemon SSH e redefinir a configuração SSH para valores predefinidos, crie um ficheiro denominado `reset_sshd.json` . Adicione o seguinte conteúdo:

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

Para criar um utilizador com permissões **de sudo** que utilize uma chave SSH para autenticação, crie um ficheiro nomeado `create_new_user.json` e adicione definições no seguinte formato. Substitua os seus próprios valores pelos `username` `ssh_key` parâmetros e parâmetros. Este método foi concebido para ajudá-lo a recuperar o acesso a um VM no caso de as credenciais atuais serem perdidas ou esquecidas. Como uma boa prática, as contas com permissões **de sudo** devem ser limitadas.

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

Para eliminar um utilizador, crie um ficheiro nomeado `delete_user.json` e adicione o seguinte conteúdo. Substitua o seu próprio valor pelo `remove_user` parâmetro:

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
Utilizando o VMAccess, também pode verificar e reparar um disco que adicionou ao Linux VM.

Para verificar e, em seguida, reparar o disco, crie um ficheiro nomeado `disk_check_repair.json` e adicione definições no seguinte formato. Substitua o seu próprio valor pelo nome `repair_disk` de:

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

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
