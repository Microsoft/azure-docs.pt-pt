---
title: Redefinir o acesso a uma VM Linux do Azure
description: Como gerenciar usuários administrativos e redefinir o acesso em VMs Linux usando a extensão VMAccess e o CLI do Azure
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073595"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Gerenciar usuários administrativos, SSH e verificar ou reparar discos em VMs Linux usando a extensão VMAccess com o CLI do Azure
## <a name="overview"></a>Descrição geral
O disco em sua VM do Linux está mostrando erros. De alguma forma, você redefine a senha raiz para sua VM Linux ou excluiu acidentalmente sua chave privada SSH. Se isso aconteceu nos dias do datacenter, você precisaria fazer isso e, em seguida, abrir o KVM para obter no console do servidor. Considere a extensão VMAccess do Azure como o comutador KVM que permite acessar o console para redefinir o acesso ao Linux ou executar a manutenção em nível de disco.

Este artigo mostra como usar a extensão VMAccess do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário administrativo ou atualizar a configuração de SSH no Linux quando elas estiverem em execução como Azure Resource Manager máquinas virtuais. Se você precisar gerenciar máquinas virtuais clássicas, você pode seguir as instruções encontradas na [documentação da VM clássica](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Se você usar a extensão VMAccess para redefinir a senha de sua VM depois de instalar a extensão de logon do AAD, será necessário executar novamente a extensão de logon do AAD para reabilitar o logon do AAD para seu computador.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="operating-system"></a>Sistema operativo

A extensão de acesso à VM pode ser executada em relação a essas distribuições do Linux:

| Distribuição | Versão |
|---|---|
| Ubuntu | 16, 4 LTS, 14, 4 LTS e 12, 4 LTS |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7 +, 7.1 + |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 e 12 |
| OpenSuse | openSUSE Leap 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Maneiras de usar a extensão VMAccess
Há duas maneiras de usar a extensão VMAccess em suas VMs do Linux:

* Use o CLI do Azure e os parâmetros necessários.
* [Use arquivos JSON brutos que o processo de extensão VMAccess](#use-json-files-and-the-vmaccess-extension) e, em seguida, atue.

Os exemplos a seguir usam comandos [AZ VM User](/cli/azure/vm/user) . Para executar essas etapas, você precisará do [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalado e conectado a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Atualizar chave SSH
O exemplo a seguir atualiza a chave SSH para o usuário `azureuser` na VM chamada `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Observação:** O comando `az vm user update` acrescenta o novo texto de chave pública ao arquivo de `~/.ssh/authorized_keys` para o usuário administrador na VM. Isso não substitui ou remove nenhuma chave SSH existente. Isso não removerá as chaves anteriores definidas no momento da implantação ou atualizações subsequentes por meio da extensão VMAccess.

## <a name="reset-password"></a>Repor palavra-passe
O exemplo a seguir redefine a senha para o usuário `azureuser` na VM chamada `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reiniciar o SSH
O exemplo a seguir reinicia o daemon SSH e redefine a configuração de SSH para valores padrão em uma VM chamada `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Criar um usuário administrativo/sudo
O exemplo a seguir cria um usuário chamado `myNewUser` com permissões **sudo** . A conta usa uma chave SSH para autenticação na VM chamada `myVM`. Esse método foi criado para ajudá-lo a obter acesso a uma VM caso as credenciais atuais sejam perdidas ou esquecidas. Como prática recomendada, as contas com permissões **sudo** devem ser limitadas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Eliminar um utilizador
O exemplo a seguir exclui um usuário chamado `myNewUser` na VM chamada `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Usar arquivos JSON e a extensão VMAccess
Os exemplos a seguir usam arquivos JSON brutos. Use [AZ VM Extension Set](/cli/azure/vm/extension) para, em seguida, chame seus arquivos JSON. Esses arquivos JSON também podem ser chamados de modelos do Azure. 

### <a name="reset-user-access"></a>Redefinir o acesso do usuário
Se você perdeu o acesso à raiz em sua VM Linux, você pode iniciar um script VMAccess para atualizar a chave ou senha SSH de um usuário.

Para atualizar a chave pública SSH de um usuário, crie um arquivo chamado `update_ssh_key.json` e adicione as configurações no formato a seguir. Substitua seus próprios valores para os parâmetros `username` e `ssh_key`:

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

Para redefinir uma senha de usuário, crie um arquivo chamado `reset_user_password.json` e adicione as configurações no formato a seguir. Substitua seus próprios valores para os parâmetros `username` e `password`:

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

### <a name="restart-ssh"></a>Reiniciar o SSH
Para reiniciar o daemon SSH e redefinir a configuração de SSH para valores padrão, crie um arquivo chamado `reset_sshd.json`. Adicione o seguinte conteúdo:

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

### <a name="manage-administrative-users"></a>Gerenciar usuários administrativos

Para criar um usuário com permissões **sudo** que usa uma chave SSH para autenticação, crie um arquivo chamado `create_new_user.json` e adicione as configurações no formato a seguir. Substitua seus próprios valores para os parâmetros `username` e `ssh_key`. Esse método foi criado para ajudá-lo a obter acesso a uma VM caso as credenciais atuais sejam perdidas ou esquecidas. Como prática recomendada, as contas com permissões **sudo** devem ser limitadas.

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

Para excluir um usuário, crie um arquivo chamado `delete_user.json` e adicione o conteúdo a seguir. Substitua seu próprio valor para o parâmetro `remove_user`:

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

### <a name="check-or-repair-the-disk"></a>Verificar ou reparar o disco
Usando o VMAccess, você também pode verificar e reparar um disco que você adicionou à VM do Linux.

Para verificar e reparar o disco, crie um arquivo chamado `disk_check_repair.json` e adicione as configurações no formato a seguir. Substitua seu próprio valor pelo nome do `repair_disk`:

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
## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
