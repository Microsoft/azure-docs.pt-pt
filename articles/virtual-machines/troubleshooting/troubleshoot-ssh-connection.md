---
title: Problemas de ligação SSH a um VM Azure Microsoft Docs
description: Como resolver problemas como "ligação SSH falhou" ou "ligação SSH recusada" para um Azure VM que executa o Linux.
keywords: ligação ssh recusado, erro ssh, azure ssh, ligação SSH falhou
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
ms.openlocfilehash: 678bad67b454ec0930d2cf30df45ba7b2c822e35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371461"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Troubleshoot SSH connections to an Azure Linux VM that fails, errors out, or is refused (Resolver problemas de ligação de SSH a uma VM do Linux do Azure que falha, dá erro ou é recusada)
Este artigo ajuda-o a encontrar e corrigir os problemas que ocorrem devido a erros da Secure Shell (SSH), falhas de ligação SSH ou SSH é recusado quando tenta ligar-se a uma máquina virtual Linux (VM). Pode utilizar o portal Azure, Azure CLI ou extensão de acesso VM para o Linux para resolver problemas e resolver problemas de ligação.


Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **suporte**. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Após cada passo de resolução de problemas, tente voltar a ligar-se ao VM.

1. [Repor a configuração SSH](#reset-the-ssh-configuration).
2. [Repor as credenciais](#reset-ssh-credentials-for-a-user) para o utilizador.
3. Verifique se as regras do [grupo de segurança](../../virtual-network/network-security-groups-overview.md) da rede permitem o tráfego de SSH.
   * Certifique-se de que existe uma [regra do Grupo de Segurança de Rede](#check-security-rules) para permitir o tráfego SSH (por defeito, porta TCP 22).
   * Não é possível utilizar a reorientação /mapeamento da porta sem utilizar um equilibrador de carga Azure.
4. Verifique a saúde dos [recursos VM.](../../service-health/resource-health-overview.md)
   * Certifique-se de que o VM reporta ser saudável.
   * Se tiver [diagnósticos de arranque ativados,](boot-diagnostics.md)verifique se o VM não está a reportar erros de arranque nos registos.
5. [Reiniciar o VM](#restart-a-vm).
6. [Reimplantar o VM](#redeploy-a-vm).

Continuar a ler Para etapas e explicações mais detalhadas de resolução de problemas.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para resolver problemas de conexão SSH
Pode redefinir credenciais ou configuração SSH utilizando um dos seguintes métodos:

* [Portal Azure](#use-the-azure-portal) - ótimo se precisar de redefinir rapidamente a configuração SSH ou a tecla SSH e não tiver as ferramentas Azure instaladas.
* [Consola em série Azure VM](https://aka.ms/serialconsolelinux) - a consola em série VM funcionará independentemente da configuração SSH, e fornecer-lhe-á uma consola interativa para o seu VM. Na verdade, as situações de "não pode SSH" são especificamente o que a consola em série foi desenhada para ajudar a resolver. Mais detalhes abaixo.
* [Azure CLI](#use-the-azure-cli) - se já estiver na linha de comando, reinicie rapidamente a configuração ou credenciais SSH. Se estiver a trabalhar com um VM clássico, pode utilizar o CLI clássico do [Azure.](#use-the-azure-classic-cli)
* [Extensão Azure VMAccessForLinux](#use-the-vmaccess-extension) - crie e reutile ficheiros de definição json para redefinir a configuração SSH ou as credenciais do utilizador.

Depois de cada passo de resolução de problemas, tente ligar-se novamente ao seu VM. Se ainda não conseguir ligar, tente o próximo passo.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
O portal Azure fornece uma forma rápida de redefinir a configuração SSH ou as credenciais do utilizador sem instalar quaisquer ferramentas no seu computador local.

Para começar, selecione o seu VM no portal Azure. Desloque-se para baixo para a secção **De Resolução de Problemas +** e selecione redefinir a **palavra-passe** como no exemplo seguinte:

![Redefinir a configuração ou credenciais do SSH no portal Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Redefinir a configuração SSH
Para reiniciar a configuração SSH, selecione `Reset configuration only` na secção **Modo** como na imagem anterior e, em seguida, selecione **Update**. Uma vez concluída esta ação, tente aceder novamente ao seu VM.

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
Para redefinir as credenciais de um utilizador existente, selecione ou `Reset SSH public key` `Reset password` na secção **Modo** como na imagem anterior. Especifique o nome de utilizador e uma chave SSH ou nova palavra-passe e, em seguida, selecione  **Update**.

Também pode criar um utilizador com privilégios de sudo no VM a partir deste menu. Introduza um novo nome de utilizador e palavra-passe associada ou chave SSH e, em seguida, selecione **Update**.

### <a name="check-security-rules"></a>Verifique as regras de segurança

Utilize [o fluxo IP para](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) confirmar se uma regra de um grupo de segurança de rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode rever as regras eficazes do grupo de segurança para garantir que a regra NSG de entrada existe e é prioritária para a porta SSH (padrão 22). Para obter mais informações, consulte [a utilização de regras de segurança eficazes para resolver o fluxo de tráfego de VM.](../../virtual-network/diagnose-network-traffic-filter-problem.md)

### <a name="check-routing"></a>Verificar encaminhamento

Utilize a capacidade de [lúpulo next](../../network-watcher/diagnose-vm-network-routing-problem.md) do Network Watcher para confirmar que uma rota não impede que o tráfego seja encaminhado para ou de uma máquina virtual. Também pode rever rotas eficazes para ver todas as rotas eficazes para uma interface de rede. Para obter mais informações, consulte [utilizando rotas eficazes para resolver o fluxo de tráfego de VM.](../../virtual-network/diagnose-network-routing-problem.md)

## <a name="use-the-azure-vm-serial-console"></a>Utilize a consola em série Azure VM
A [Consola Em Série Azure VM](./serial-console-linux.md) fornece acesso a uma consola baseada em texto para máquinas virtuais Linux. Pode utilizar a consola para resolver problemas na sua ligação SSH numa concha interativa. Certifique-se de que cumpriu os [pré-requisitos](./serial-console-linux.md#prerequisites) para a utilização da Consola em Série e experimente os comandos abaixo para resolver ainda mais a sua conectividade SSH.

### <a name="check-that-ssh-is-running"></a>Verifique se o SSH está a funcionar
Pode utilizar o seguinte comando para verificar se o SSH está a funcionar no seu VM:

```console
ps -aux | grep ssh
```

Se houver alguma saída, a SSH está a funcionar.

### <a name="check-which-port-ssh-is-running-on"></a>Verifique em que porta o SSH está em execução

Pode utilizar o seguinte comando para verificar em que porta o SSH está em funcionamento:

```console
sudo grep Port /etc/ssh/sshd_config
```

A sua saída será parecida com:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Se ainda não o fez, instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e inicie sessão numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Se criou e carregou uma imagem de disco Linux personalizada, certifique-se de que a versão 2.0.5 ou posterior [do Agente Do Microsoft Azure Linux](../extensions/agent-linux.md) é instalada. Para os VMs criados com imagens gallery, esta extensão de acesso já está instalada e configurada para si.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração SSH
Pode inicialmente tentar redefinir a configuração SSH para valores predefinidos e reiniciar o servidor SSH no VM. Isto não altera o nome da conta de utilizador, palavra-passe ou teclas SSH.
O exemplo a seguir utiliza [o reset-sh do utilizador az vm](/cli/azure/vm/user) para redefinir a configuração SSH no VM nomeado `myVM` em `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
O exemplo a seguir utiliza [a atualização do utilizador AZ VM](/cli/azure/vm/user) para redefinir as credenciais para `myUsername` o valor especificado em , no `myPassword` VM nomeado em `myVM` `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se utilizar a autenticação da chave SSH, pode redefinir a tecla SSH para um determinado utilizador. O exemplo a seguir utiliza **o conjunto de acessos az vm-utilizadores** para atualizar a tecla SSH armazenada `~/.ssh/id_rsa.pub` para o utilizador denominado , no `myUsername` VM nomeado `myVM` em `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Utilize a extensão VMAccess
A Extensão de Acesso VM para Linux lê-se num ficheiro json que define as ações a realizar. Estas ações incluem a reposição do SSHD, a reposição de uma chave SSH ou a adição de um utilizador. Ainda usa o CLI Azure para ligar para a extensão VMAccess, mas pode reutilizar os ficheiros json em vários VMs, se desejar. Esta abordagem permite criar um repositório de ficheiros json que podem ser chamados para determinados cenários.

### <a name="reset-sshd"></a>Reset SSHD
Criar um ficheiro com `settings.json` o seguinte conteúdo:

```json
{
    "reset_ssh":True
}
```

Utilizando o CLI Azure, ligue para a `VMAccessForLinux` extensão para reiniciar a sua ligação SSHD especificando o seu ficheiro json. O exemplo a seguir utiliza [o conjunto de extensão az vm](/cli/azure/vm/extension) para repor o SSHD no VM nomeado `myVM` em `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
Se o SSHD parecer funcionar corretamente, pode redefinir as credenciais para um utilizador doador. Para redefinir a palavra-passe de um utilizador, crie um ficheiro denominado `settings.json` . O exemplo a seguir repõe as credenciais `myUsername` para o valor especificado em `myPassword` . Introduza as seguintes linhas no seu `settings.json` ficheiro, utilizando os seus próprios valores:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou para redefinir a chave SSH para um utilizador, primeiro criar um ficheiro chamado `settings.json` . O exemplo a seguir repõe as credenciais para `myUsername` o valor especificado em , no `myPassword` VM nomeado em `myVM` `myResourceGroup` . Introduza as seguintes linhas no seu `settings.json` ficheiro, utilizando os seus próprios valores:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar o seu ficheiro json, utilize o CLI Azure para ligar `VMAccessForLinux` para a extensão para redefinir as credenciais de utilizador do SSH especificando o seu ficheiro json. O exemplo a seguir repõe credenciais no VM nomeado `myVM` em `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Use o CLI clássico do Azure
Se ainda não o fez, [instale o CLI clássico do Azure e ligue-se à sua subscrição Azure](/cli/azure/install-classic-cli). Certifique-se de que está a utilizar o modo gestor de recursos da seguinte forma:

```azurecli
azure config mode arm
```

Se criou e carregou uma imagem de disco Linux personalizada, certifique-se de que a versão 2.0.5 ou posterior [do Agente Do Microsoft Azure Linux](../extensions/agent-linux.md) é instalada. Para os VMs criados com imagens gallery, esta extensão de acesso já está instalada e configurada para si.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração SSH
A própria configuração SSHD pode estar mal configurada ou o serviço encontrou um erro. Pode reiniciar o SSHD para se certificar de que a configuração SSH em si é válida. A reposição do SSHD deve ser o primeiro passo para resolver problemas.

O exemplo que se segue repõe o SSHD num VM nomeado `myVM` no grupo de recursos `myResourceGroup` . Use os seus próprios nomes de grupos de VM e recursos da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Repor as credenciais de SSH de um utilizador)
Se o SSHD parecer funcionar corretamente, pode redefinir a palavra-passe para um utilizador doador. O exemplo a seguir repõe as credenciais para `myUsername` o valor especificado em , no `myPassword` VM nomeado em `myVM` `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Se utilizar a autenticação da chave SSH, pode redefinir a tecla SSH para um determinado utilizador. O exemplo a seguir atualiza a chave SSH armazenada `~/.ssh/id_rsa.pub` para o utilizador denominado , no `myUsername` VM nomeado em `myVM` `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a>Reiniciar uma VM
Se tiver reiniciado a configuração SSH e as credenciais do utilizador, ou tiver encontrado um erro ao fazê-lo, pode tentar reiniciar o VM para resolver problemas de computação subjacentes.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar um VM utilizando o portal Azure, selecione o seu VM e, em seguida, **selecione Restart** como no exemplo seguinte:

![Reiniciar um VM no portal Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir utiliza [o reinício do VM az vm](/cli/azure/vm) para reiniciar o VM nomeado `myVM` no grupo de recursos denominado `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O exemplo a seguir reinicia o VM nomeado `myVM` no grupo de recursos denominado `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a>Reimplementar uma VM
Pode recolocar um VM noutro nó dentro do Azure, o que pode corrigir quaisquer problemas subjacentes à rede. Para obter informações sobre a recolocação de um VM, consulte [a máquina virtual de recolocação para o novo nó Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> Após o fim desta operação, perdem-se dados efémeros de disco e atualizam-se os endereços IP dinâmicos associados à máquina virtual.
>
>

### <a name="azure-portal"></a>Portal do Azure
Para recolocar um VM utilizando o portal Azure, selecione o seu VM e desloque-se para a secção **Support + Troubleshooting.** Selecione **Reposicionar-se** como no exemplo seguinte:

![Reimplantar um VM no portal Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir utiliza [a reafectação do AZ VM](/cli/azure/vm) para reimplantar o VM nomeado `myVM` no grupo de recursos denominado `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

O exemplo a seguir redistribui o VM nomeado `myVM` no grupo de recursos denominado `myResourceGroup` . Use os seus próprios valores da seguinte forma:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criados usando o modelo de implementação Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Experimente estes passos para resolver as falhas de ligação SSH mais comuns para VMs que foram criadas utilizando o modelo de implementação clássico. Após cada passo, tente voltar a ligar-se ao VM.

* Reinicie o acesso remoto a partir do [portal Azure](https://portal.azure.com). No portal Azure, selecione o seu VM e, em seguida, **selecione Reset Remote...**.
* Reinicie a VM. No [portal Azure,](https://portal.azure.com)selecione o seu VM e **selecione Restart**.

* Recolocar o VM num novo nó Azure. Para obter informações sobre como recolocar um VM, consulte [a máquina virtual De recolocação para o novo nó Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    Após o fim desta operação, perder-se-ão dados efémeros de disco e serão atualizados os endereços IP dinâmicos associados à máquina virtual.
* Siga as instruções em [Como redefinir uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) para:

  * Repor a palavra-passe ou a chave SSH.
  * Criar uma conta de utilizador *sudo.*
  * Reinicie a configuração SSH.
* Verifique se a saúde dos recursos do VM é responsável por quaisquer problemas de plataforma.<br>
     Selecione o seu VM e **desloque-se**para baixo As definições  >  **Verifique saúde**.

## <a name="additional-resources"></a>Recursos adicionais
* Se ainda não conseguir fazer SSH ao seu VM depois de seguir os passos seguintes, consulte [medidas mais detalhadas de resolução de problemas](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para rever medidas adicionais para resolver o seu problema.
* Para obter mais informações sobre o acesso à aplicação de resolução de [problemas, consulte o acesso a uma aplicação em execução numa máquina virtual Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
* Para obter mais informações sobre a resolução de problemas de máquinas virtuais criadas utilizando o modelo de implementação clássico, consulte [Como redefinir uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic).
