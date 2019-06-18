---
title: Reponha o acesso a uma VM do Linux do Azure | Documentos da Microsoft
description: Como gerir os utilizadores administrativos e reponha o acesso em VMs do Linux com a extensão VMAccess e a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 71aecc1748e70e2119b1f54c21a0f705afc5d5d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60800070"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Gerir os utilizadores administrativos, SSH e verificar ou reparar discos em VMs do Linux com a extensão VMAccess com a CLI do Azure
## <a name="overview"></a>Descrição geral
O disco na sua VM do Linux está a Mostrar erros. Alguma forma repor a palavra-passe de raiz para a sua VM do Linux ou eliminado acidentalmente a chave privada SSH. Se o que aconteceu na época do Data Center, precisaria para lá da unidade e, em seguida, abra o KVM para obter a consola do servidor. Considere a extensão VMAccess do Azure como esse comutador KVM que permite-lhe aceder à consola para repor o acesso ao Linux ou realizar a manutenção de nível de disco.

Este artigo mostra-lhe como utilizar a extensão VMAccess do Azure para verificar ou reparar um disco, reponha o acesso de utilizador, gerir contas de utilizador administrativo ou atualizar a configuração de SSH no Linux, que são executados como máquinas de virtuais do Azure Resource Manager. Se precisar de gerir máquinas virtuais de clássico - pode seguir as instruções no [documentação da VM clássica](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Se utilizar a extensão VMAccess para repor a palavra-passe da sua VM depois de instalar a extensão de início de sessão do AAD tem de executar novamente a extensão de início de sessão do AAD para voltar a ativar o início de sessão do AAD para a sua máquina.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-system"></a>Sistema operativo

A extensão de acesso à VM pode ser executada nessas distribuições de Linux:

| Distribuição | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS e 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 e 12 |
| OpenSuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Formas de utilizar a extensão VMAccess
Existem duas formas que pode utilizar a extensão VMAccess nas suas VMs do Linux:

* Utilize a CLI do Azure e os parâmetros necessários.
* [Utilizar ficheiros JSON não processados que processam a extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e, em seguida, tomar medidas.

Os exemplos seguintes utilizam [utilizador de vm de az](/cli/azure/vm/user) comandos. Para executar estes passos, terá da versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Atualizar a chave SSH
O exemplo seguinte atualiza a chave SSH para o usuário `azureuser` na VM com o nome `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **NOTA:** O `az vm user update` comando acrescenta o novo texto de chave público para o `~/.ssh/authorized_keys` ficheiro para o utilizador de administrador na VM. Isso não substituir ou remova quaisquer chaves SSH existentes. Não, esta ação irá remover as chaves anteriores que definido no momento da implementação ou atualizações subsequentes via a extensão VMAccess.

## <a name="reset-password"></a>Repor palavra-passe
O exemplo seguinte repõe a palavra-passe para o usuário `azureuser` na VM com o nome `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reinicie o SSH
O exemplo seguinte reinicia o daemon de SSH e repõe a configuração de SSH para valores padrão numa VM com o nome `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Criar um utilizador administrativo/sudo
O exemplo seguinte cria um utilizador com o nome `myNewUser` com **sudo** permissões. A conta utiliza uma chave SSH para autenticação na VM com o nome `myVM`. Este método destina-se para o ajudar a recuperar o acesso a uma VM no caso de credenciais atuais são perdidas ou esquecidas. Como melhor prática, contas com **sudo** permissões devem ser limitadas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Eliminar um utilizador
O exemplo seguinte elimina um utilizador com o nome `myNewUser` na VM com o nome `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Utilize ficheiros JSON e a extensão VMAccess
Os exemplos seguintes utilizam arquivos brutos de JSON. Uso [conjunto de extensão az vm](/cli/azure/vm/extension) , em seguida, chamar seus ficheiros JSON. Estes ficheiros JSON também podem ser chamados a partir de modelos do Azure. 

### <a name="reset-user-access"></a>Reponha o acesso de utilizador
Se perder acesso à raiz na sua VM do Linux, pode iniciar um script de VMAccess para atualizar a chave SSH ou palavra-passe de um utilizador.

Para atualizar a chave pública de SSH, de um utilizador, crie um ficheiro denominado `update_ssh_key.json` e adicionar as definições no seguinte formato. Substitua os seus valores para o `username` e `ssh_key` parâmetros:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Execute o script de VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Para repor uma palavra-passe do utilizador, crie um ficheiro denominado `reset_user_password.json` e adicionar as definições no seguinte formato. Substitua os seus valores para o `username` e `password` parâmetros:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Execute o script de VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Reinicie o SSH
Para reiniciar o daemon de SSH e repor a configuração de SSH para os valores predefinidos, crie um ficheiro denominado `reset_sshd.json`. Adicione o seguinte conteúdo:

```json
{
  "reset_ssh": true
}
```

Execute o script de VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Gerir os utilizadores administrativos

Para criar um utilizador com **sudo** permissões que utiliza uma chave SSH para autenticação, crie um ficheiro denominado `create_new_user.json` e adicionar as definições no seguinte formato. Substitua os seus valores para o `username` e `ssh_key` parâmetros. Este método destina-se para o ajudar a recuperar o acesso a uma VM no caso de credenciais atuais são perdidas ou esquecidas. Como melhor prática, contas com **sudo** permissões devem ser limitadas.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Execute o script de VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Para eliminar um utilizador, crie um ficheiro denominado `delete_user.json` e adicione o seguinte conteúdo. Substitua o seu próprio valor para o `remove_user` parâmetro:

```json
{
  "remove_user":"myNewUser"
}
```

Execute o script de VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Verificar ou reparar o disco
Utilizar o VMAccess pode também verificar e reparar um disco que adicionou à VM do Linux.

Para verificar e, em seguida, reparar o disco, crie um ficheiro denominado `disk_check_repair.json` e adicionar as definições no seguinte formato. Substitua o seu próprio valor para o nome do `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Execute o script de VMAccess com:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
