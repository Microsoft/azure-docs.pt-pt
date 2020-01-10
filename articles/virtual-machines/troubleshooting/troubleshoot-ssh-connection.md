---
title: Solucionar problemas de conexão SSH para uma VM do Azure | Microsoft Docs
description: Como solucionar problemas como ' falha na conexão SSH ' ou ' conexão SSH recusada ' para uma VM do Azure que executa o Linux.
keywords: conexão SSH recusada, erro de SSH, Azure SSH, falha na conexão SSH
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f0a79fb0f90a633095343c162ccdc80ebc48f1d4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747665"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Solucionar problemas de conexões SSH para uma VM Linux do Azure que falha, erros ou é recusada
Este artigo ajuda você a localizar e corrigir os problemas que ocorrem devido a erros de Secure Shell (SSH), falhas de conexão SSH ou SSH é recusado quando você tenta se conectar a uma VM (máquina virtual) do Linux. Você pode usar a extensão de acesso portal do Azure, CLI do Azure ou VM para Linux para solucionar problemas de conexão.


Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Etapas de solução de problemas rápidos
Após cada etapa de solução de problemas, tente reconectar-se à VM.

1. [Redefina a configuração de SSH](#reset-config).
2. [Redefina as credenciais](#reset-credentials) do usuário.
3. Verifique se as regras do [grupo de segurança de rede](../../virtual-network/security-overview.md) permitem o tráfego SSH.
   * Verifique se existe uma [regra de grupo de segurança de rede](#security-rules) para permitir o tráfego SSH (por padrão, porta TCP 22).
   * Você não pode usar o mapeamento/redirecionamento de porta sem usar um balanceador de carga do Azure.
4. Verifique a [integridade do recurso da VM](../../resource-health/resource-health-overview.md).
   * Verifique se a VM é relatada como íntegra.
   * Se você tiver o [diagnóstico de inicialização habilitado](boot-diagnostics.md), verifique se a VM não está relatando erros de inicialização nos logs.
5. [Reinicie a VM](#restart-vm).
6. [Reimplante a VM](#redeploy-vm).

Continue lendo para ver as etapas e explicações de solução de problemas mais detalhadas.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para solucionar problemas de conexão SSH
Você pode redefinir credenciais ou configuração de SSH usando um dos seguintes métodos:

* [Portal do Azure](#use-the-azure-portal) -ótimo se você precisar redefinir rapidamente a configuração SSH ou a chave SSH e não tiver as ferramentas do Azure instaladas.
* [Console serial da VM do Azure](https://aka.ms/serialconsolelinux) -o console serial da VM funcionará independentemente da configuração do SSH e fornecerá a você um console interativo para sua VM. Na verdade, as situações "não é SSH" são especificamente o que o console serial foi projetado para ajudar a solucioná-lo. Mais detalhes abaixo.
* [CLI do Azure](#use-the-azure-cli) -se você já estiver na linha de comando, redefina rapidamente a configuração ou as credenciais do SSH. Se você estiver trabalhando com uma VM clássica, poderá usar a [CLI clássica do Azure](#use-the-azure-classic-cli).
* [Extensão VMAccessForLinux do Azure](#use-the-vmaccess-extension) – crie e reutilize os arquivos de definição JSON para redefinir a configuração do ssh ou as credenciais do usuário.

Após cada etapa de solução de problemas, tente se conectar à VM novamente. Se você ainda não conseguir se conectar, tente a próxima etapa.

## <a name="use-the-azure-portal"></a>Utilizar o Portal do Azure
O portal do Azure fornece uma maneira rápida de redefinir a configuração do SSH ou as credenciais do usuário sem instalar nenhuma ferramenta no computador local.

Para começar, selecione sua VM no portal do Azure. Role para baixo até a seção **suporte + solução de problemas** e selecione **Redefinir senha** como no exemplo a seguir:

![Redefinir a configuração ou as credenciais do SSH no portal do Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />redefinir a configuração de SSH
Para redefinir a configuração de SSH, selecione `Reset configuration only` na seção **modo** como na captura de tela anterior e, em seguida, selecione **Atualizar**. Depois que essa ação for concluída, tente acessar a VM novamente.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />redefinir credenciais SSH para um usuário
Para redefinir as credenciais de um usuário existente, selecione `Reset SSH public key` ou `Reset password` na seção **modo** como na captura de tela anterior. Especifique o nome de usuário e uma chave SSH ou nova senha, em seguida, selecione **Atualizar**.

Você também pode criar um usuário com privilégios sudo na VM por meio desse menu. Insira um novo nome de usuário e senha associada ou chave SSH e, em seguida, selecione **Atualizar**.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />verificar regras de segurança

Use a [verificação de fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra em um grupo de segurança de rede está bloqueando o tráfego para ou de uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra "permitir" do NSG de entrada exista e seja priorizada para a porta SSH (padrão 22). Para obter mais informações, consulte [usando regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Verificar roteamento

Use a capacidade do [próximo salto](../../network-watcher/network-watcher-check-next-hop-portal.md) do observador de rede para confirmar se uma rota não está impedindo que o tráfego seja roteado para ou de uma máquina virtual. Você também pode examinar rotas efetivas para ver todas as rotas efetivas para uma interface de rede. Para obter mais informações, consulte [usando rotas efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Usar o console serial da VM do Azure
O [console serial da VM do Azure](./serial-console-linux.md) fornece acesso a um console baseado em texto para máquinas virtuais do Linux. Você pode usar o console do para solucionar problemas de conexão SSH em um shell interativo. Verifique se você atendeu os [pré-requisitos](./serial-console-linux.md#prerequisites) para usar o console serial e tente os comandos a seguir para solucionar problemas de conectividade SSH.

### <a name="check-that-ssh-is-running"></a>Verificar se o SSH está em execução
Você pode usar o seguinte comando para verificar se o SSH está em execução em sua VM:
```
$ ps -aux | grep ssh
```
Se houver qualquer saída, o SSH estará ativo e em execução.

### <a name="check-which-port-ssh-is-running-on"></a>Verificar em qual porta o SSH está sendo executado
Você pode usar o seguinte comando para verificar em qual porta o SSH está em execução:
```
$ sudo grep Port /etc/ssh/sshd_config
```
A saída terá uma aparência semelhante a:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Se você ainda não fez isso, instale as [CLI do Azure](/cli/azure/install-az-cli2) mais recentes e entre em uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Se você criou e carregou uma imagem de disco do Linux personalizada, verifique se o [Microsoft Azure agente do Linux](../extensions/agent-linux.md) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, essa extensão de acesso já está instalada e configurada para você.

### <a name="reset-ssh-configuration"></a>Redefinir configuração de SSH
Inicialmente, você pode tentar redefinir a configuração de SSH para valores padrão e reinicializar o servidor SSH na VM. Isso não altera o nome da conta de usuário, a senha ou as chaves SSH.
O exemplo a seguir usa [AZ VM User Reset-SSH](/cli/azure/vm/user) para redefinir a configuração de SSH na vm chamada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
O exemplo a seguir usa [AZ VM User Update](/cli/azure/vm/user) para redefinir as credenciais para `myUsername` para o valor especificado em `myPassword`, na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se estiver usando a autenticação de chave SSH, você poderá redefinir a chave SSH para um determinado usuário. O exemplo a seguir usa **AZ VM Access Set-Linux-User** para atualizar a chave SSH armazenada em `~/.ssh/id_rsa.pub` para o usuário chamado `myUsername`, na VM denominada `myVM` no `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Usar a extensão VMAccess
A extensão de acesso da VM para o Linux lê em um arquivo JSON que define as ações a serem executadas. Essas ações incluem a redefinição de SSHD, a redefinição de uma chave SSH ou a adição de um usuário. Você ainda usa o CLI do Azure para chamar a extensão VMAccess, mas você pode reutilizar os arquivos JSON em várias VMs, se desejado. Essa abordagem permite que você crie um repositório de arquivos JSON que podem ser chamados para determinados cenários.

### <a name="reset-sshd"></a>Redefinir SSHD
Crie um arquivo chamado `settings.json` com o seguinte conteúdo:

```json
{
    "reset_ssh":"True"
}
```

Usando o CLI do Azure, você chama a extensão `VMAccessForLinux` para redefinir sua conexão SSHD especificando o arquivo JSON. O exemplo a seguir usa [AZ VM Extension Set](/cli/azure/vm/extension) para redefinir sshd na vm chamada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
Se SSHD parecer funcionar corretamente, você poderá redefinir as credenciais para um usuário do determinado. Para redefinir a senha de um usuário, crie um arquivo chamado `settings.json`. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`. Insira as linhas a seguir em seu arquivo de `settings.json`, usando seus próprios valores:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou para redefinir a chave SSH para um usuário, primeiro crie um arquivo chamado `settings.json`. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`, na VM denominada `myVM` em `myResourceGroup`. Insira as linhas a seguir em seu arquivo de `settings.json`, usando seus próprios valores:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar o arquivo JSON, use o CLI do Azure para chamar a extensão `VMAccessForLinux` para redefinir suas credenciais de usuário SSH especificando o arquivo JSON. O exemplo a seguir redefine as credenciais na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Usar a CLI clássica do Azure
Se você ainda não fez isso, [Instale a CLI clássica do Azure e conecte-se à sua assinatura do Azure](../../cli-install-nodejs.md). Verifique se você está usando o modo do Resource Manager da seguinte maneira:

```azurecli
azure config mode arm
```

Se você criou e carregou uma imagem de disco do Linux personalizada, verifique se o [Microsoft Azure agente do Linux](../extensions/agent-linux.md) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, essa extensão de acesso já está instalada e configurada para você.

### <a name="reset-ssh-configuration"></a>Redefinir configuração de SSH
A configuração SSHD em si pode estar configurada incorretamente ou o serviço encontrou um erro. Você pode redefinir SSHD para garantir que a configuração de SSH em si seja válida. A redefinição de SSHD deve ser a primeira etapa de solução de problemas que você tomar.

O exemplo a seguir redefine SSHD em uma VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use sua própria VM e nomes de grupos de recursos da seguinte maneira:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir credenciais SSH para um usuário
Se SSHD parecer funcionar corretamente, você poderá redefinir a senha para um usuário do determinado. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`, na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Se estiver usando a autenticação de chave SSH, você poderá redefinir a chave SSH para um determinado usuário. O exemplo a seguir atualiza a chave SSH armazenada em `~/.ssh/id_rsa.pub` para o usuário chamado `myUsername`, na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />reiniciar uma VM
Se você redefiniu a configuração do SSH e as credenciais do usuário ou encontrou um erro ao fazer isso, você pode tentar reiniciar a VM para resolver problemas de computação subjacentes.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar uma VM usando o portal do Azure, selecione sua VM e, em seguida, selecione **reiniciar** como no exemplo a seguir:

![Reiniciar uma VM no portal do Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir usa [AZ VM Restart](/cli/azure/vm) para reiniciar a vm chamada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure
O exemplo a seguir reinicia a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />reimplantar uma VM
Você pode reimplantar uma VM em outro nó no Azure, o que pode corrigir quaisquer problemas de rede subjacentes. Para obter informações sobre como reimplantar uma VM, consulte [reimplantar máquina virtual no novo nó do Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Após a conclusão dessa operação, os dados do disco efêmero são perdidos e os endereços IP dinâmicos associados à máquina virtual são atualizados.
>
>

### <a name="azure-portal"></a>Portal do Azure
Para reimplantar uma VM usando o portal do Azure, selecione sua VM e role para baixo até a seção **suporte + solução de problemas** . Selecione **reimplantar** como no exemplo a seguir:

![Reimplantar uma VM no portal do Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir usa [AZ VM Redeploy](/cli/azure/vm) para reimplantar a vm chamada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure
O exemplo a seguir reimplanta a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criadas usando o modelo de implantação clássico
Tente estas etapas para resolver as falhas de conexão SSH mais comuns para VMs que foram criadas usando o modelo de implantação clássico. Após cada etapa, tente reconectar-se à VM.

* Redefina o acesso remoto do [portal do Azure](https://portal.azure.com). Na portal do Azure, selecione sua VM e, em seguida, selecione **Redefinir remoto...** .
* Reinicie a VM. Na [portal do Azure](https://portal.azure.com), selecione sua VM e selecione **reiniciar**.

* Reimplante a VM em um novo nó do Azure. Para obter informações sobre como reimplantar uma VM, consulte [reimplantar máquina virtual no novo nó do Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Após a conclusão dessa operação, os dados do disco efêmero serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
* Siga as instruções em [como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](../linux/classic/reset-access-classic.md) para:

  * Redefina a senha ou a chave SSH.
  * Crie uma conta de usuário do *sudo* .
  * Redefina a configuração de SSH.
* Verifique a integridade do recurso da VM em busca de qualquer problema de plataforma.<br>
     Selecione sua VM e role para baixo **as configurações** > **verificar integridade**.

## <a name="additional-resources"></a>Recursos adicionais
* Se você ainda não conseguir executar o SSH para sua VM depois de seguir as etapas posteriores, consulte [etapas de solução de problemas mais detalhadas](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para examinar as etapas adicionais para resolver o problema.
* Para obter mais informações sobre como solucionar problemas de acesso a aplicativos, consulte [solucionar problemas de acesso a um aplicativo em execução em uma máquina virtual do Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Para obter mais informações sobre como solucionar problemas de máquinas virtuais que foram criadas usando o modelo de implantação clássico, consulte [como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](../linux/classic/reset-access-classic.md).
