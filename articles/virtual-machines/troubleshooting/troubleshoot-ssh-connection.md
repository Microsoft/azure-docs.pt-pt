---
title: Problemas de ligação SSH a um Azure VM / Microsoft Docs
description: Como resolver problemas como a "ligação SSH falhou" ou "Ligação SSH recusada" para um VM Azure que executa o Linux.
keywords: ssh conexão recusada, erro ssh, ssh azul, conexão SSH falhou
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
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060667"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Troubleshoot SSH connections to an Azure Linux VM that fails, errors out, or is refused (Resolver problemas de ligação de SSH a uma VM do Linux do Azure que falha, dá erro ou é recusada)
Este artigo ajuda-o a encontrar e corrigir os problemas que ocorrem devido a erros da Secure Shell (SSH), falhas de ligação SSH ou SSH é recusado quando tenta ligar-se a uma máquina virtual Linux (VM). Pode utilizar o portal Azure, Azure CLI ou VM Access Extension para o Linux para resolver problemas de ligação.


Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Depois de cada passo de resolução de problemas, tente voltar a ligar-se ao VM.

1. [Redefinir a configuração SSH](#reset-config).
2. [Redefinir as credenciais](#reset-credentials) para o utilizador.
3. Verifique se as regras do grupo de segurança da [rede](../../virtual-network/security-overview.md) permitem o tráfego de SSH.
   * Certifique-se de que existe uma regra do Grupo de Segurança da [Rede](#security-rules) para permitir o tráfego de SSH (por defeito, porta TCP 22).
   * Não é possível utilizar a redirecção/mapeamento da porta sem utilizar um equilibrador de carga Azure.
4. Verifique a saúde dos [recursos VM.](../../resource-health/resource-health-overview.md)
   * Certifique-se de que o VM reporta como sendo saudável.
   * Se tiver um diagnóstico de [arranque ativado,](boot-diagnostics.md)verifique se o VM não está a reportar erros de arranque nos registos.
5. [Reiniciar o VM](#restart-vm).
6. [Reutilizar o VM](#redeploy-vm).

Continuar a ler Para mais detalhadas diligências e explicações de resolução de problemas.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para resolver problemas problemas de conexão SSH
Pode redefinir credenciais ou configuração SSH utilizando um dos seguintes métodos:

* [Portal Azure](#use-the-azure-portal) - ótimo se precisar de redefinir rapidamente a configuração SSH ou a chave SSH e não tiver as ferramentas Azure instaladas.
* [Consola em série Azure VM](https://aka.ms/serialconsolelinux) - a consola em série VM funcionará independentemente da configuração SSH e fornecer-lhe-á uma consola interativa para o seu VM. Na verdade, as situações de "não pode SSH" são especificamente o que a consola em série foi concebida para ajudar a resolver. Mais detalhes abaixo.
* [Azure CLI](#use-the-azure-cli) - se já estiver na linha de comando, repor rapidamente a configuração ou credenciais SSH. Se estiver a trabalhar com um VM clássico, pode utilizar o [clássico CLI azure.](#use-the-azure-classic-cli)
* [Extensão Azure VMAccessForLinux](#use-the-vmaccess-extension) - crie e reutilize ficheiros de definição json para redefinir a configuração SSH ou as credenciais de utilizador.

Depois de cada passo de resolução de problemas, tente ligar-se novamente ao seu VM. Se ainda não consegue ligar, tente o próximo passo.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
O portal Azure fornece uma forma rápida de redefinir a configuração SSH ou credenciais de utilizador sem instalar quaisquer ferramentas no seu computador local.

Para começar, selecione o seu VM no portal Azure. Desloque-se até à secção **de resolução** de problemas + e selecione a **palavra-passe de reset** como no seguinte exemplo:

![Redefinir a configuração ou credenciais de SSH no portal Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />Redefinir a configuração SSH
Para redefinir a configuração `Reset configuration only` SSH, selecione na secção **Modo** como na imagem anterior e, em seguida, selecione **'Atualizar**' . Uma vez concluída esta ação, tente aceder novamente ao seu VM.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
Para redefinir as credenciais de um `Reset SSH public key` `Reset password` utilizador existente, selecione ou na secção **Modo,** tal como na imagem anterior. Especifique o nome de utilizador e uma chave SSH ou uma nova senha e, em seguida, selecione **'Atualizar**' .

Também pode criar um utilizador com privilégios sudo no VM a partir deste menu. Introduza um novo nome de utilizador e uma senha ou chave SSH associadas e, em seguida, selecione **Update**.

### <a name="check-security-rules"></a><a id="security-rules" />Verifique as regras de segurança

Utilize a [verificação](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) do fluxo IP para confirmar se uma regra de um grupo de segurança de rede está bloqueando o tráfego de ou para uma máquina virtual. Também pode rever regras eficazes do grupo de segurança para garantir a existência da regra NSG de entrada e é priorizada para a porta SSH (padrão 22). Para mais informações, consulte A utilização de regras de [segurança eficazes para resolver o fluxo](../../virtual-network/diagnose-network-traffic-filter-problem.md)de tráfego vm .

### <a name="check-routing"></a>Verifique o encaminhamento

Utilize a capacidade de [salto Seguinte](../../network-watcher/network-watcher-check-next-hop-portal.md) do Network Watcher para confirmar que uma rota não está a impedir que o tráfego seja encaminhado para ou para uma máquina virtual. Também pode rever rotas eficazes para ver todas as rotas eficazes para uma interface de rede. Para mais informações, consulte [Utilizar rotas eficazes para resolver o fluxo](../../virtual-network/diagnose-network-routing-problem.md)de tráfego vm .

## <a name="use-the-azure-vm-serial-console"></a>Utilize a consola em série Azure VM
A [Consola Série Azure VM](./serial-console-linux.md) fornece acesso a uma consola baseada em texto para máquinas virtuais Linux. Pode utilizar a consola para resolver problemas com a sua ligação SSH numa concha interativa. Certifique-se de que cumpriu os [pré-requisitos](./serial-console-linux.md#prerequisites) para utilizar a Consola em Série e experimente os comandos abaixo para resolver ainda mais a sua conectividade SSH.

### <a name="check-that-ssh-is-running"></a>Verifique se o SSH está a funcionar
Pode utilizar o seguinte comando para verificar se o SSH está a funcionar no seu VM:

```console
ps -aux | grep ssh
```

Se houver alguma saída, o SSH está a funcionar.

### <a name="check-which-port-ssh-is-running-on"></a>Verifique em que porta o SSH está a funcionar

Pode utilizar o seguinte comando para verificar em que porta o SSH está a funcionar:

```console
sudo grep Port /etc/ssh/sshd_config
```

A sua saída será algo como:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Se ainda não o fez, instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e inicie sessão numa conta Azure utilizando [login az](/cli/azure/reference-index).

Se criou e carregou uma imagem personalizada do disco Linux, certifique-se de que a versão 2.0.5 do [Microsoft Azure Linux Agent](../extensions/agent-linux.md) ou posteriormente está instalada. Para VMs criados com imagens da Galeria, esta extensão de acesso já está instalada e configurada para si.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração SSH
Inicialmente, pode tentar reajustar a configuração SSH para valores predefinidos e reiniciar o servidor SSH no VM. Isto não altera o nome da conta de utilizador, palavra-passe ou teclas SSH.
O exemplo seguinte utiliza o [reset-ssh do utilizador az vm](/cli/azure/vm/user) para `myVM` `myResourceGroup`redefinir a configuração SSH no VM nomeado em . Use os seus próprios valores da seguinte forma:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
O exemplo seguinte utiliza a [atualização do utilizador Az vm](/cli/azure/vm/user) para redefinir as credenciais para `myUsername` o valor especificado no `myPassword`VM nomeado `myVM` em `myResourceGroup`. Use os seus próprios valores da seguinte forma:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se utilizar a autenticação da chave SSH, pode redefinir a tecla SSH para um determinado utilizador. O exemplo seguinte utiliza o **az vm access set-linux-user** `~/.ssh/id_rsa.pub` para atualizar `myUsername`a chave SSH armazenada para o utilizador nomeado , no VM nomeado `myVM` em `myResourceGroup`. Use os seus próprios valores da seguinte forma:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Utilize a extensão VMAccess
A Extensão de Acesso VM para Linux lê-se num ficheiro json que define ações a realizar. Estas ações incluem a reposição do SSHD, a reposição de uma chave SSH ou a adição de um utilizador. Ainda utiliza o CLI Azure para ligar para a extensão VMAccess, mas pode reutilizar os ficheiros json em vários VMs, se desejar. Esta abordagem permite criar um repositório de ficheiros JSON que podem ser chamados para determinados cenários.

### <a name="reset-sshd"></a>Redefinir SSHD
Criar um `settings.json` ficheiro com o seguinte conteúdo:

```json
{
    "reset_ssh":"True"
}
```

Utilizando o Azure CLI, `VMAccessForLinux` ligue para a extensão para redefinir a sua ligação SSHD especificando o seu ficheiro json. O exemplo seguinte utiliza [uma extensão az vm definida](/cli/azure/vm/extension) para `myVM` `myResourceGroup`repor o SSHD no VM nomeado em . Use os seus próprios valores da seguinte forma:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
Se o SSHD parecer funcionar corretamente, pode redefinir as credenciais para um utilizador que dá um dador. Para redefinir a palavra-passe para `settings.json`um utilizador, crie um ficheiro com o nome . O exemplo seguinte repõe `myUsername` as credenciais para `myPassword`o valor especificado em . Introduza as seguintes linhas no seu `settings.json` ficheiro, utilizando os seus próprios valores:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou para redefinir a tecla SSH para um `settings.json`utilizador, primeiro criar um ficheiro chamado . O exemplo seguinte repõe `myUsername` as credenciais para `myPassword`o valor especificado no VM nomeado `myVM` em `myResourceGroup`. Introduza as seguintes linhas no seu `settings.json` ficheiro, utilizando os seus próprios valores:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar o seu ficheiro json, `VMAccessForLinux` utilize o ClI Azure para ligar para a extensão para redefinir as credenciais de utilizador do SSH, especificando o seu ficheiro json. O exemplo que se segue repõe `myVM` `myResourceGroup`as credenciais no VM nomeado em . Use os seus próprios valores da seguinte forma:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Use o CLI clássico azure
Se ainda não o fez, [instale o CLI clássico do Azure e ligue-se à subscrição do Azure.](../../cli-install-nodejs.md) Certifique-se de que está a utilizar o modo Gestor de Recursos da seguinte forma:

```azurecli
azure config mode arm
```

Se criou e carregou uma imagem personalizada do disco Linux, certifique-se de que a versão 2.0.5 do [Microsoft Azure Linux Agent](../extensions/agent-linux.md) ou posteriormente está instalada. Para VMs criados com imagens da Galeria, esta extensão de acesso já está instalada e configurada para si.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração SSH
A configuração SSHD em si pode ser mal configurada ou o serviço encontrou um erro. Pode redefinir o SSHD para se certificar de que a configuração SSH em si é válida. Repor o SSHD deve ser o primeiro passo de resolução de problemas que se dá.

O exemplo seguinte repõe o SSHD `myVM` num VM `myResourceGroup`nomeado no grupo de recursos denominado . Use os nomes do seu próprio VM e do grupo de recursos da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
Se o SSHD parecer funcionar corretamente, pode redefinir a palavra-passe para um utilizador dado. O exemplo seguinte repõe `myUsername` as credenciais para `myPassword`o valor especificado no VM nomeado `myVM` em `myResourceGroup`. Use os seus próprios valores da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Se utilizar a autenticação da chave SSH, pode redefinir a tecla SSH para um determinado utilizador. O exemplo seguinte atualiza a chave SSH `~/.ssh/id_rsa.pub` armazenada `myUsername`para o utilizador `myVM` `myResourceGroup`nomeado , no VM nomeado em . Use os seus próprios valores da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Reiniciar uma VM
Se tiver reposto a configuração ssh e as credenciais de utilizador, ou tiver encontrado um erro ao fazê-lo, pode tentar reiniciar o VM para resolver problemas de cálculo subjacentes.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar um VM utilizando o portal Azure, selecione o seu VM e, em seguida, selecione **Reiniciar** como no seguinte exemplo:

![Reinicie um VM no portal Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo seguinte usa [az vm](/cli/azure/vm) restart `myVM` para reiniciar `myResourceGroup`o VM nomeado no grupo de recursos chamado . Use os seus próprios valores da seguinte forma:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O exemplo seguinte reinicia `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado . Use os seus próprios valores da seguinte forma:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Reimplementar uma VM
Pode reimplantar um VM para outro nó dentro do Azure, o que pode corrigir quaisquer problemas de networking subjacentes. Para obter informações sobre a reimplantação de um VM, consulte [Redeploy virtual machine para o novo nó Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Após esta operação terminar, os dados do disco efémero são perdidos e os endereços IP dinâmicos que estão associados à máquina virtual são atualizados.
>
>

### <a name="azure-portal"></a>Portal do Azure
Para reimplantar um VM utilizando o portal Azure, selecione o seu VM e desloque-se até à secção Suporte + Resolução de **Problemas.** Selecione **Redeploy** como no seguinte exemplo:

![Reutilizar um VM no portal Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo seguinte utiliza [a reimplantação az vm](/cli/azure/vm) para reimplantar o VM nomeado `myVM` no grupo de recursos denominado `myResourceGroup`. Use os seus próprios valores da seguinte forma:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

O exemplo seguinte reimplanta `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado . Use os seus próprios valores da seguinte forma:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criados usando o modelo de implementação Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Experimente estes passos para resolver as falhas de ligação SSH mais comuns para VMs que foram criados usando o modelo de implementação clássico. Após cada passo, tente voltar a ligar-se ao VM.

* Redefinir o acesso remoto a partir do [portal Azure](https://portal.azure.com). No portal Azure, selecione o seu VM e, em seguida, selecione **Reset Remote...**.
* Reinicie a VM. No [portal Azure,](https://portal.azure.com)selecione o seu VM e selecione **Reiniciar**.

* Reimplante o VM para um novo nó Azure. Para obter informações sobre como reimplantar um VM, consulte [Redeploy virtual machine para o novo nó Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Após esta operação terminar, os dados do disco efémero serão perdidos e serão atualizados os endereços IP dinâmicos que estão associados à máquina virtual.
* Siga as instruções em [Como redefinir uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](../linux/classic/reset-access-classic.md) para:

  * Redefinir a palavra-passe ou a tecla SSH.
  * Crie uma conta de utilizador *sudo.*
  * Redefinir a configuração SSH.
* Verifique a saúde dos recursos da VM para obter quaisquer problemas na plataforma.<br>
     Selecione o seu VM e desloque as **definições** > para baixo**Verifique a saúde**.

## <a name="additional-resources"></a>Recursos adicionais
* Se ainda não conseguir fazer sSH ao seu VM depois de seguir os passos seguintes, consulte [medidas mais detalhadas](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para rever passos adicionais para resolver o seu problema.
* Para obter mais informações sobre o acesso a aplicações de resolução de problemas, consulte [o acesso da Troubleshoot a uma aplicação em execução numa máquina virtual Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Para obter mais informações sobre a resolução de problemas de máquinas virtuais que foram criadas utilizando o modelo de implementação clássico, consulte [Como redefinir uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](../linux/classic/reset-access-classic.md).
